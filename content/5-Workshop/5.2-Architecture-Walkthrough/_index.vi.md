---
title: "Phân tích Kiến trúc Nền tảng"
weight: 52
chapter: false
pre: " <b> 5.2. </b> "
---

![Architecture](/images/architecture.png)
<p align="center"><em>Hình 5.2: Phân ranh giới các miền ứng dụng và luồng dữ liệu Clickstream Analytics của Team f5-SCAJ.</em></p>

---

### 1. Miền User-Facing & OLTP (Public Network Zone)

Miền User-Facing phục vụ toàn bộ trải nghiệm mua sắm của khách hàng và lưu trữ các giao dịch thương mại điện tử.

| Thành phần | Tên tài nguyên AWS | Chức năng kỹ thuật & Vai trò trong kiến trúc |
| --- | --- | --- |
| **User Browser** | Client Devices | Nơi người dùng thực hiện duyệt sản phẩm máy tính, thêm giỏ hàng và thanh toán. Phát sinh sự kiện Clickstream qua JS Client. |
| **Amazon CloudFront** | CDN Distribution | Cung cấp lớp CDN toàn cầu, tăng tốc nén/cache các file tĩnh (HTML/CSS/JS/Ảnh) từ S3 Asset và Amplify Origin. |
| **Amazon S3 Asset** | `clickstream-s3-scaj` | Lưu trữ toàn bộ hình ảnh sản phẩm laptop/phụ kiện. Đứng sau CloudFront OAC. Tách biệt hoàn toàn với S3 Ingest. |
| **AWS Amplify** | `ClickSteam.NextJS` | Build & Host ứng dụng Next.js SSR/ISR, kết nối với Cognito Auth và gọi API Backend. |
| **Amazon Cognito** | User Pool | Quản lý danh tính người dùng (đăng ký, đăng nhập, JWT tokens). Gắn `userId` và `user_login_state` vào sự kiện clickstream. |
| **EC2 OLTP DB** | `SCAJ_EC2_WebDB` | Máy chủ EC2 nằm tại **Public Subnet** (`10.0.0.0/20`), chạy PostgreSQL (`clickstream_web`) lưu trữ sản phẩm, đơn hàng và giỏ hàng. |

---

### 2. Miền Ingestion & Serverless Buffer

Miền Ingestion đảm nhận nhiệm vụ thu nhận lưu lượng lớn các event hành vi với độ trễ cực thấp mà không làm gián đoạn trải nghiệm mua sắm.

```
[ Browser Event ] ──▶ [ API Gateway: POST /clickstream ] ──▶ [ Lambda Ingest ] ──▶ [ S3 Raw: events/YYYY/MM/DD/HH/ ]
```

- **Amazon API Gateway (HTTP API)**: Tên `clickstream-http-api`. Định tuyến request `POST /clickstream`, hỗ trợ CORS origins và tích hợp access log vào CloudWatch.
- **AWS Lambda Ingest**: Hàm `clickstream-lambda-ingest` nhận JSON payload, gắn thêm khối metadata `_ingest` (`receivedAt`, `sourceIp`, `userAgent`, `requestId`) và ghi file JSON vào S3.
- **S3 Raw Clickstream Bucket**: Bucket `clickstream-s3-ingest`. Lưu trữ file sự kiện thô theo quy tắc đặt tên:
  `s3://clickstream-s3-ingest/events/YYYY/MM/DD/HH/event-<uuid>.json`

---

### 3. Miền Analytics & Private DWH (Private Network Zone)

Toàn bộ hệ thống Data Warehouse và xử lý phân tích dữ liệu được bảo vệ an toàn bên trong **Private Subnet** (`10.0.128.0/20`), tuyệt đối không gán IP Public.

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

#### Chi tiết các dịch vụ trong Private Zone:

1. **VPC Networking & Subnets**:
   - VPC CIDR: `10.0.0.0/16` (`SCAJ_Project_VPC`)
   - Public Subnet: `10.0.0.0/20` (Route table trỏ `0.0.0.0/0` -> Internet Gateway)
   - Private Subnet: `10.0.128.0/20` (Không có route ra Internet Gateway hoặc NAT Gateway)

2. **AWS S3 Gateway VPC Endpoint**:
   - Cho phép `Lambda ETL` và `EC2 Private` giao tiếp trực tiếp với S3 thông qua tuyến đường nội bộ của AWS.
   - Giúp giảm 100% chi phí NAT Gateway khi đọc/ghi file từ S3 Raw Bucket.

3. **AWS SSM Interface VPC Endpoints**:
   - Bao gồm các endpoint: `com.amazonaws.ap-southeast-1.ssm`, `ssmmessages`, `ec2messages`.
   - Cho phép tính năng **AWS Systems Manager Session Manager** kết nối vào EC2 private để quản trị và forward port `3838` về `localhost` mà không mở cổng 22 SSH.

4. **Amazon EC2 Private (DWH + Dashboard)**:
   - Tên instance: `SCAJ_EC2_ShinyDWH`
   - Hệ điều hành: Ubuntu 22.04 LTS
   - Chạy Data Warehouse PostgreSQL 18 (Database: `clickstream_dw`, bảng chính `clickstream_events`).
   - Chạy R Shiny Server lắng nghe tại port `3838` (`/srv/shiny-server/sbw_dashboard/app.R`).

5. **AWS Lambda ETL**:
   - Hàm `SCAJ_Lambda_ETL` được cấu hình VPC join vào Private Subnet.
   - Nhận trigger từ **Amazon EventBridge Rule** (`SCAJ_ETL_HOURLY_RULE` với lịch chạy `rate(1 hour)`).
   - Đọc danh sách file JSON thô trong S3 giờ vừa qua, loại bỏ trùng lặp (`event_id`), transform kiểu dữ liệu và thực hiện `INSERT INTO clickstream_events ON CONFLICT DO NOTHING`.

---

### 4. Bảng Ranh giới Security Groups (Network Access Control)

| Security Group Name | Inbound Rules Allowed | Outbound Rules Allowed | Mục đích sử dụng |
| --- | --- | --- | --- |
| `sg_oltp_webDB` | Port `5432/tcp` từ `sg_amplify` hoặc Public Subnet | Allow All Outbound | Cho phép Web App truy vấn CSDL OLTP |
| `sg_Lambda_ETL` | No Inbound Required | Port `5432/tcp` tới `sg_analytics_ShinyDWH`, S3 Endpoint | Cho phép Lambda kết nối DB DWH & đọc S3 |
| `sg_analytics_ShinyDWH` | Port `5432/tcp` từ `sg_Lambda_ETL`<br>Port `3838/tcp` từ SSM Interface Endpoint | Port `443/tcp` tới SSM Endpoints | Bảo vệ DWH & Shiny Dashboard khỏi Internet |