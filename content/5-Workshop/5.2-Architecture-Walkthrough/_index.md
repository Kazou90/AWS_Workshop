---
title: "Architecture Walkthrough"
weight: 52
chapter: false
pre: " <b> 5.2. </b> "
---

![Architecture](/images/architecture.png)
<p align="center"><em>Figure 5.2: End-to-End Architecture Domains & Data Boundaries of f5-SCAJ Clickstream Platform.</em></p>

---

### 1. User-Facing & OLTP Domain (Public Network Zone)

The User-Facing domain handles customer web interactions and stores operational e-commerce transactions.

| Component | AWS Resource Name | Technical Function & Role |
| --- | --- | --- |
| **User Browser** | Client Devices | Emits client-side clickstream events (`page_view`, `product_view`, `add_to_cart`, `checkout`) via JavaScript SDK. |
| **Amazon CloudFront** | CDN Distribution | Global edge caching for static website assets (HTML/CSS/JS/Images) backed by S3 Asset & Amplify Origins. |
| **Amazon S3 Asset** | `clickstream-s3-scaj` | Stores computer product images and static media assets behind CloudFront OAC. Completely separated from Raw S3. |
| **AWS Amplify** | `ClickSteam.NextJS` | Builds & hosts the Next.js SSR application, integrates Cognito Auth, and communicates with backend services. |
| **Amazon Cognito** | User Pool | Manages user registration/login. Attaches `userId` and `user_login_state` to clickstream event context. |
| **EC2 OLTP DB** | `SCAJ_EC2_WebDB` | EC2 instance located in the **Public Subnet** (`10.0.0.0/20`), running PostgreSQL (`clickstream_web`) for e-commerce transactions. |

---

### 2. Ingestion & Serverless Buffer Domain

The Ingestion domain handles high-concurrency event ingestion with minimal latency.

```
[ Browser Event ] ──▶ [ API Gateway: POST /clickstream ] ──▶ [ Lambda Ingest ] ──▶ [ S3 Raw: events/YYYY/MM/DD/HH/ ]
```

- **Amazon API Gateway (HTTP API)**: Resource name `clickstream-http-api`. Routes `POST /clickstream`, enforces CORS, and streams access logs to CloudWatch.
- **AWS Lambda Ingest**: Function `clickstream-lambda-ingest` validates payload, appends an `_ingest` block (`receivedAt`, `sourceIp`, `userAgent`, `requestId`), and writes JSON files to S3.
- **S3 Raw Clickstream Bucket**: Bucket `clickstream-s3-ingest`. Stores raw event JSON files partitioned by UTC hour:
  `s3://clickstream-s3-ingest/events/YYYY/MM/DD/HH/event-<uuid>.json`

---

### 3. Analytics & Private DWH Domain (Private Network Zone)

The Data Warehouse and analytical components reside strictly inside the **Private Subnet** (`10.0.128.0/20`) with no public IP assigned.

```
                  ┌────────────────────────────────────────────────────────┐
                  │                 VPC Private Subnet                     │
                  │                                                        │
[ S3 Raw Data ] ──┼─(S3 Gateway Endpoint)──▶ [ Lambda ETL ]               │
                  │                             │                          │
                  │                             ▼ (Port 5432)              │
                  │                   [ EC2 DWH (PostgreSQL 18) ]          │
                  │                             │                          │
                  │                             ▼ (Port 3838)              │
                  │                   [ R Shiny Server ]                   │
                  │                             ▲                          │
                  └─────────────────────────────┼──────────────────────────┘
                                                │ (SSM Port Forwarding)
                                      [ Admin Client Machine ]
```

#### Private Zone Components Breakdown:

1. **VPC Networking & Subnets**:
   - VPC CIDR: `10.0.0.0/16` (`SCAJ_Project_VPC`)
   - Public Subnet: `10.0.0.0/20` (Route table: `0.0.0.0/0` -> Internet Gateway)
   - Private Subnet: `10.0.128.0/20` (No route to Internet Gateway or NAT Gateway)

2. **AWS S3 Gateway VPC Endpoint**:
   - Routes S3 traffic directly over AWS private backbone network for `Lambda ETL` and `EC2 Private`.
   - Eliminates 100% of NAT Gateway transfer fees.

3. **AWS SSM Interface VPC Endpoints**:
   - Endpoints: `com.amazonaws.ap-southeast-1.ssm`, `ssmmessages`, `ec2messages`.
   - Enables **AWS Systems Manager Session Manager** port forwarding (`3838` -> `localhost:3838`) without opening SSH port 22.

4. **Amazon EC2 Private (DWH + Dashboard)**:
   - Instance Name: `SCAJ_EC2_ShinyDWH` (Ubuntu 22.04 LTS)
   - Runs PostgreSQL 18 Data Warehouse (`clickstream_dw`, table `clickstream_events`).
   - Hosts R Shiny Server on port `3838` (`/srv/shiny-server/sbw_dashboard/app.R`).

5. **AWS Lambda ETL**:
   - Function `SCAJ_Lambda_ETL` attached to VPC Private Subnet.
   - Triggered by **Amazon EventBridge Rule** (`SCAJ_ETL_HOURLY_RULE` at `rate(1 hour)`).
   - Reads hourly raw JSON files from S3, flattens payload schema, and executes `INSERT INTO clickstream_events ON CONFLICT DO NOTHING`.

---

### 4. Security Group Access Control Matrix

| Security Group Name | Inbound Rules Allowed | Outbound Rules Allowed | Intended Purpose |
| --- | --- | --- | --- |
| `sg_oltp_webDB` | Port `5432/tcp` from `sg_amplify` / Public Subnet | Allow All Outbound | Allows Web App to query OLTP DB |
| `sg_Lambda_ETL` | No Inbound Required | Port `5432/tcp` to `sg_analytics_ShinyDWH`, S3 Endpoint | Allows Lambda ETL to access DWH & S3 |
| `sg_analytics_ShinyDWH` | Port `5432/tcp` from `sg_Lambda_ETL`<br>Port `3838/tcp` from SSM Interface Endpoint | Port `443/tcp` to SSM Endpoints | Protects DWH & Shiny Dashboard from Internet |
