---
title: "Mục tiêu & Phạm vi Lab Guide"
weight: 51
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Bối cảnh Kinh doanh & Thách thức Kỹ thuật

Nền tảng mục tiêu là một hệ thống thương mại điện tử chuyên kinh doanh dòng sản phẩm laptop, màn hình máy tính và thiết bị ngoại vi. Nhằm tối ưu hóa hiệu quả kinh doanh và chiến dịch marketing, doanh nghiệp đặt ra bài toán thu thập và khai thác dữ liệu hành vi người dùng (Clickstream Data).

#### Các câu hỏi kinh doanh cần giải quyết:
- **Hành trình người dùng (User Funnel)**: Người dùng điều hướng ra sao từ lúc vào Trang chủ -> Xem Sản phẩm -> Thêm vào Giỏ hàng (`add_to_cart`) -> Hoàn tất Bán hàng (`checkout`). Tỷ lệ rớt (drop-off rate) ở từng bước là bao nhiêu?
- **Hiệu suất Sản phẩm**: Những mặt hàng nào được quan tâm nhiều nhất nhưng tỷ lệ mua thấp? Sản phẩm nào có tỷ lệ chuyển đổi cao nhất?
- **Phân bổ Lưu lượng theo Thời gian**: Những khung giờ nào có lượng truy cập đỉnh trong ngày để lên lịch khuyến mãi?

---

### 2. Mục tiêu Học tập & Kỹ năng Thực hành (Learning Objectives)

Hoàn thành bài workshop này, bạn sẽ làm chủ được các kỹ năng hạ tầng Cloud & Data Engineering:

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

1. **Thiết kế Hạ tầng Bảo mật (Private Infrastructure)**:
   - Tách biệt miền OLTP (Public Subnet) và Analytics (Private Subnet).
   - Bảo vệ Data Warehouse và Shiny Dashboard hoàn toàn ẩn với Internet public.
2. **Triển khai Serverless Ingestion & Data Lake**:
   - Xây dựng HTTP API Gateway tích hợp Lambda Ingest stateless.
   - Thiết kế lưu trữ S3 Data Lake phân vùng theo giờ UTC (`events/YYYY/MM/DD/HH/`).
3. **Cấu hình VPC Endpoints & Tối ưu Chi phí**:
   - Cấu hình **S3 Gateway VPC Endpoint** cho phép Lambda ETL đọc S3 trực tiếp trong đường ống mạng nội bộ AWS, loại bỏ 100% chi phí NAT Gateway.
   - Cấu hình **SSM Interface Endpoints** (`ssm`, `ssmmessages`, `ec2messages`) phục vụ truy cập quản trị không qua SSH.
4. **Viết Pipeline ETL & Trực quan hóa dữ liệu**:
   - Xây dựng hàm Lambda ETL xử lý JSON thô, flatten thành định dạng SQL-ready và upsert vào PostgreSQL DWH.
   - Triển khai R Shiny Server hiển thị KPI Cards, Funnel Chart và Product Engagement Matrix.

---

### 3. Phạm vi Bài Lab (Scope Boundary)

| Hạng mục | Trong phạm vi (In-Scope) | Ngoài phạm vi (Out-of-Scope) |
| --- | --- | --- |
| **Kiến trúc dữ liệu** | Batch processing (Lô định kỳ mỗi 1 giờ) | Streaming thời gian thực (Kinesis / Kafka / MSK) |
| **Data Warehouse** | PostgreSQL trên EC2 (t3.small / Jammy 22.04) | Managed DWH (Amazon Redshift / Snowflake) |
| **Xác thực & Kết nối** | AWS SSM Session Manager Port Forwarding | SSH công khai, Bastion Host |
| **ETL & Scheduling** | AWS Lambda + EventBridge Cron Rule | Apache Airflow / AWS Glue Workflows |
| **Bảo mật Mạng** | VPC Endpoints, Security Group Scoping, IAM Least Privilege | Cross-account VPC Peering, AWS WAF Rules phức tạp |

---

### 4. Bảng Thông số Hạ tầng Chuẩn (Target Specs)

- **VPC Network**: CIDR `10.0.0.0/16` (`SCAJ_Project_VPC`)
- **Public Subnet**: `10.0.0.0/20` (Dành cho EC2 OLTP `SCAJ_EC2_WebDB`)
- **Private Subnet**: `10.0.128.0/20` (Dành cho DWH `SCAJ_EC2_ShinyDWH` & Lambda ETL)
- **Data Warehouse DB**: PostgreSQL 18 (`clickstream_dw`), Bảng chính `clickstream_events`
- **Dashboard Port**: R Shiny Server port `3838`, Path `/sbw_dashboard/`
