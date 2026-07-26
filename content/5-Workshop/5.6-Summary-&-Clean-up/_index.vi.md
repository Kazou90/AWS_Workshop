---
title: "Tổng kết & Quy trình Dọn dẹp Tài nguyên"
weight: 56
chapter: false
pre: " <b> 5.6. </b> "
---

### 1. Tổng kết Thành quả Kỹ thuật (Technical Summary)

Sau khi hoàn thành bài Workshop này, bạn đã tự tay triển khai thành công một **Batch-Based Clickstream Analytics Platform** chuẩn Production dành cho thương mại điện tử với các ưu điểm nổi bật:

```
[ Frontend: Next.js + Amplify ] ──▶ [ Serverless Ingestion ] ──▶ [ S3 Raw Data Lake ]
                                                                        │
                                                              (Gateway VPC Endpoint)
                                                                        ▼
[ SSM Port Forward: 3838 ] ◄── [ R Shiny Dashboard ] ◄── [ Private DWH Postgres ] ◄── [ Lambda ETL (VPC) ]
```

1. **Phân tách Ranh giới Kiến trúc (Separation of Concerns)**:
   - Hệ thống OLTP (`SCAJ_EC2_WebDB`) và Analytics DWH (`SCAJ_EC2_ShinyDWH`) được tách biệt hoàn toàn cả về logic lẫn hạ tầng vật lý.
2. **Bảo mật Hạ tầng Tối đa (Zero-Trust Security Baseline)**:
   - Data Warehouse và Dashboard R Shiny nằm trọn trong **Private Subnet**, không có IP Public, không mở port SSH.
   - Quản trị viên truy cập Dashboard qua **AWS SSM Session Manager Port Forwarding** an toàn tuyệt đối.
3. **Tối ưu Chi phí Vận hành (Zero-NAT Cost Optimization)**:
   - Loại bỏ hoàn toàn chi phí NAT Gateway đắt đỏ bằng cách sử dụng **S3 Gateway VPC Endpoint** cho các tác vụ đọc/ghi dữ liệu của Lambda ETL.
   - Tổng chi phí ước tính duy trì hạ tầng ~2.65 USD/tháng.

---

### 2. Quy trình Dọn dẹp Tài nguyên (Resource Cleanup Checklist)

> [!IMPORTANT]
> Để tránh phát sinh chi phí ngoài ý muốn trên tài khoản AWS của bạn, hãy thực hiện dọn dẹp tài nguyên theo đúng thứ tự phụ thuộc bên dưới:

#### Bước 1: Xóa Lớp Frontend & API Ingestion
- **AWS Amplify**: Xóa App `ClickSteam.NextJS` (Thao tác này tự động dọn dẹp CloudFront Distribution đi kèm).
- **Amazon API Gateway**: Xóa HTTP API `clickstream-http-api`.
- **AWS Lambda Functions**: Xóa 2 hàm:
  - `clickstream-lambda-ingest`
  - `SCAJ_Lambda_ETL`

#### Bước 2: Xóa Lớp Lên lịch & Lưu trữ Data Lake
- **Amazon EventBridge**: Xóa Rule `SCAJ_ETL_HOURLY_RULE`.
- **Amazon S3 Buckets**: 
  - Mở S3 Console -> Chọn **Empty** toàn bộ đối tượng bên trong bucket.
  - Xóa 2 buckets: `clickstream-s3-ingest` và `clickstream-s3-scaj`.

#### Bước 3: Terminate Máy chủ EC2 & Networking
- **Amazon EC2 Instances**:
  - Select và **Terminate** 2 máy chủ: `SCAJ_EC2_WebDB` và `SCAJ_EC2_ShinyDWH`.
  - Giải phóng (Release) các Elastic IP nếu có.
- **AWS VPC Endpoints**:
  - Xóa **S3 Gateway VPC Endpoint**.
  - Xóa 3 **SSM Interface VPC Endpoints** (`ssm`, `ssmmessages`, `ec2messages`).
- **AWS VPC Infrastructure**:
  - Xóa các Subnets, Route Tables, Internet Gateway.
  - Xóa VPC `SCAJ_Project_VPC`.
