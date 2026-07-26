---
title: "Workshop"
weight: 5
chapter: false
pre: "<b>5. </b>"
---

# Thách thức Kỹ thuật & Thực hành Triển khai: Batch-Based Clickstream Analytics Platform

![Architecture](/images/architecture.png)

<p align="center"><em>Hình 5.0: Sơ đồ kiến trúc tổng quan nền tảng Clickstream Analytics của Team f5-SCAJ.</em></p>

#### Giới thiệu bài Lab Thực hành

Trong bài Workshop này, bạn sẽ từng bước tự tay xây dựng một **Nền tảng Phân tích Clickstream theo lô (Batch-Based Clickstream Analytics Platform)** hoàn chỉnh dành cho hệ thống thương mại điện tử chuyên kinh doanh sản phẩm máy tính.

Hệ thống được thiết kế theo các tiêu chuẩn vận hành thực tế (Production-ready):
- **Phân tách hoàn toàn OLTP và Analytics**: Đảm bảo lưu lượng phân tích nặng không ảnh hưởng đến giao dịch người dùng.
- **Bảo mật tuyệt đối Lớp Phân tích (Private Analytics Layer)**: Data Warehouse và Dashboard R Shiny chạy hoàn toàn trong **Private Subnet**, không có IP Public, không mở port SSH.
- **Chi phí tối ưu không dùng NAT Gateway**: Tận dụng **AWS Gateway VPC Endpoint cho S3** và **SSM Interface VPC Endpoints** cho việc quản trị.
- **Tự động hóa Batch ETL**: Sự kiện được thu thập theo thời gian thực vào S3 Data Lake và xử lý theo lô hàng giờ bằng **EventBridge + Serverless Lambda ETL**.

#### Cấu trúc các bài thực hành

1. **[5.1. Mục tiêu & Phạm vi Lab Guide](5.1-objectives--scope/)** - Nắm vững yêu cầu bài toán, kiến trúc mục tiêu và các giới hạn hệ thống.
2. **[5.2. Phân tích chi tiết Kiến trúc Nền tảng](5.2-architecture-walkthrough/)** - Đi sâu vào chức năng và luồng dữ liệu của 20 thành phần AWS.
3. **[5.3. Xây dựng Lớp Thu nhận Clickstream (Ingestion Layer)](5.3-implementing-clickstream-ingestion/)** - Cấu hình API Gateway, viết hàm Lambda Ingest, thiết kế S3 Raw Data Lake và tích hợp Client SDK.
4. **[5.4. Xây dựng Lớp Phân tích dữ liệu Bảo mật (Private Analytics)](5.4-building-the-private-analytics-layer/)** - Thiết lập VPC, Private Subnet, VPC Endpoints, DWH PostgreSQL trên EC2 Private, Lambda ETL và EventBridge Rule.
5. **[5.5. Trực quan hóa dữ liệu với R Shiny Dashboard](5.5-visualizing-analytics-with-shiny-dashboards/)** - Đóng gói, cài đặt môi trường R/Postgres client, deploy R Shiny Server và kết nối qua SSM Port Forwarding.
6. **[5.6. Tổng kết & Quy trình Dọn dẹp Tài nguyên](5.6-summary--clean-up/)** - Tổng kết kinh nghiệm thực chiến và checklist dọn dẹp hạ tầng AWS.
7. **[5.7. Demo & Video hướng dẫn](5.7-demo/)** - Trải nghiệm thực tế hệ thống live và video minh họa chi tiết.
