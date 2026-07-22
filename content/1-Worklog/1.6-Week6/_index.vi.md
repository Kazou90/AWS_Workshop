---
title: "Worklog Tuần 6"
date: "2026-05-25"
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu Tuần 6:
- Đánh giá toàn bộ cấu trúc mã nguồn hiện tại của dự án Thương mại điện tử (Frontend React Vite, Backend Spring Boot và luồng dữ liệu Clickstream).
- Lập kế hoạch chuyển đổi hệ thống sang Cloud: Thay thế cơ sở dữ liệu tạm H2 bằng **RDS MySQL**, cấu hình lưu trữ file CSV và dữ liệu thô trên **Amazon S3**.
- Thiết kế sơ đồ kiến trúc hạ tầng AWS tổng thể kết nối đồng bộ các dịch vụ: EC2, RDS MySQL, S3, CloudFront, Cognito và EventBridge.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Đánh giá Mã nguồn Frontend & Backend:** Review cấu trúc thư mục dự án React Vite (UI mua sắm), Spring Boot Backend (REST API) và phân tích định dạng dữ liệu Clickstream (User Session, Click events). | 25/05/2026 | 25/05/2026 | Source Code Repository |
| 2 (Thứ 3) | **Kế hoạch Chuyển đổi Database sang RDS MySQL:** Phân tích cấu hình `application.properties` của Spring Boot, chuẩn bị driver kết nối MySQL và kịch bản chuyển đổi schema từ H2 sang RDS. | 26/05/2026 | 26/05/2026 | Spring Data JPA Docs |
| 3 (Thứ 4) | **Kế hoạch Lưu trữ File CSV trên Amazon S3:** Thiết kế cấu trúc thư mục lưu trữ file CSV báo cáo trên S3 (`s3://my-bucket/raw-clickstream/`), cấu hình S3 SDK client cho Spring Boot. | 27/05/2026 | 27/05/2026 | AWS SDK for Java |
| 4 (Thứ 5) | **Vẽ Sơ đồ Kiến trúc Hạ tầng AWS:** Thiết kế sơ đồ kiến trúc đám mây toàn vẹn kết nối các dịch vụ EC2 (Backend), RDS MySQL (Database), S3 (Static Web & Data Lake), CloudFront (CDN), Cognito (Auth) và EventBridge/Lambda (ETL). | 28/05/2026 | 28/05/2026 | AWS Architecture Center |
| 5 (Thứ 6) | **Review Kiến trúc với Mentor:** Trình bày bản vẽ sơ đồ kiến trúc với Cán bộ hướng dẫn, nhận góp ý tối ưu hóa luồng mạng và quy hoạch IP/Subnet trong VPC. | 29/05/2026 | 29/05/2026 | Architecture Review |

### Kết quả đạt được Tuần 6:
* **Đánh giá Chi tiết Dự án:** Nắm vững cấu trúc mã nguồn dự án E-Commerce và định dạng dữ liệu Clickstream cần xử lý.
* **Chiến lược Chuyển đổi Cloud Rõ ràng:** Lập kế hoạch thành công chuyển đổi DB từ H2 local sang RDS MySQL và đưa toàn bộ file lưu trữ lên S3 Bucket.
* **Hoàn thiện Sơ đồ Kiến trúc AWS:** Thiết kế bản vẽ kiến trúc Cloud hoàn chỉnh đạt tiêu chuẩn AWS Well-Architected Framework, làm kim chỉ nam cho giai đoạn triển khai thực tế.
