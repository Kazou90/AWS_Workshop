---
title: "Lab Objectives & Scope"
weight: 51
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Business Context & Engineering Challenge

The target platform is a specialized e-commerce web application selling laptops, monitors, and computer hardware. To drive data-backed sales strategies and optimize digital marketing spend, the business requires a robust Clickstream Analytics Platform.

#### Key Business Questions Addressed:
- **User Journey & Funnel Analysis**: How do users navigate from Home -> Product View -> Add to Cart (`add_to_cart`) -> Checkout Completion (`checkout`)? Where are the highest drop-off points?
- **Product Engagement**: Which computer items generate high page views but low purchase conversions?
- **Traffic Patterning**: What are the peak activity windows during the day/week to target flash sales?

---

### 2. Learning Objectives & Hands-On Engineering Skills

By completing this workshop lab, you will master essential Cloud Infrastructure & Data Pipeline patterns:

```
[ Frontend: Next.js + Amplify ] 
        │ (POST /clickstream)
        ▼
[ API Gateway + Lambda Ingest ] ──▶ [ S3 Raw Bucket (events/YYYY/MM/DD/HH/) ]
                                              │
                                     (S3 Gateway Endpoint)
                                              ▼
[ EventBridge (Hourly Cron) ] ──▶ [ Lambda ETL (VPC Private) ] ──▶ [ EC2 Private DWH (PostgreSQL) ]
                                                                             │
                                                                   (Port Forward 3838 via SSM)
                                                                             ▼
                                                                 [ R Shiny Admin Dashboard ]
```

1. **Private Cloud Infrastructure Design**:
   - Strictly segregate OLTP workloads (Public Subnet) from Analytics (Private Subnet).
   - Secure the Data Warehouse and R Shiny dashboard completely out of public internet reach.
2. **Serverless Ingestion & Data Lake Architecture**:
   - Build a stateless HTTP API Gateway backed by an ingestion Lambda.
   - Design an S3 Data Lake layout with UTC hour-based partitioning (`events/YYYY/MM/DD/HH/`).
3. **VPC Endpoints & Cost Management**:
   - Configure an **S3 Gateway VPC Endpoint** so Lambda ETL reads S3 over AWS internal network backbone, avoiding 100% of NAT Gateway charges.
   - Provision **SSM Interface Endpoints** (`ssm`, `ssmmessages`, `ec2messages`) for zero-SSH management.
4. **ETL Pipeline & Data Visualization**:
   - Develop a Lambda ETL function to parse raw JSON payloads, flatten fields, and upsert records into PostgreSQL DWH.
   - Deploy R Shiny Server to render conversion funnels, product ranking matrices, and activity trends.

---

### 3. Lab Scope Boundaries

| Area | In-Scope | Out-of-Scope |
| --- | --- | --- |
| **Data Processing Model** | Batch-based (Hourly cron execution) | Real-time event streaming (Kinesis / Kafka / MSK) |
| **Data Warehouse** | PostgreSQL on EC2 (Ubuntu 22.04 LTS) | Managed DWH (Amazon Redshift / Snowflake) |
| **Authentication & Access** | AWS SSM Session Manager Port Forwarding | Public SSH access, Bastion Host |
| **ETL & Scheduling** | AWS Lambda + EventBridge Cron Rule | Apache Airflow / AWS Glue Orchestration |
| **Network Security** | VPC Endpoints, SG Scoping, IAM Least Privilege | Multi-account VPC Peering, Complex WAF Rules |

---

### 4. Target Infrastructure Specifications

- **VPC Network**: CIDR `10.0.0.0/16` (`SCAJ_Project_VPC`)
- **Public Subnet**: `10.0.0.0/20` (For EC2 OLTP `SCAJ_EC2_WebDB`)
- **Private Subnet**: `10.0.128.0/20` (For DWH `SCAJ_EC2_ShinyDWH` & Lambda ETL)
- **Data Warehouse DB**: PostgreSQL 18 (`clickstream_dw`), Table: `clickstream_events`
- **Dashboard Port**: R Shiny Server port `3838`, Path `/sbw_dashboard/`
