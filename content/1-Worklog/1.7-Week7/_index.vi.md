---
title: "Worklog Tuần 7"
date: "2026-06-01"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu Tuần 7:
- Khởi tạo cơ sở dữ liệu **RDS MySQL**, thực hiện migration chuyển đổi toàn bộ dữ liệu danh mục sản phẩm và đơn hàng từ Local lên Cloud.
- Đóng gói ứng dụng **Backend Spring Boot** (file `.jar`), cài đặt môi trường Java OpenJDK và vận hành thành công ứng dụng trên máy chủ **Amazon EC2**.
- Cấu hình tường lửa **Security Group** cho phép kết nối giao tiếp nội bộ an toàn 100% giữa EC2 Backend và RDS Database.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Khởi tạo Database RDS MySQL:** Tạo RDS MySQL instance trên AWS, cấu hình DB Subnet Group an toàn trong Private Subnet và khởi tạo database `ecommerce_db`. | 01/06/2026 | 01/06/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html> |
| 2 (Thứ 3) | **Migration Dữ liệu Sản phẩm/Đơn hàng:** Chạy SQL migration scripts chuyển đổi cấu trúc bảng và import dữ liệu sản phẩm, danh mục, đơn hàng mẫu từ local lên RDS MySQL. | 02/06/2026 | 02/06/2026 | MySQL Workbench Migration |
| 3 (Thứ 4) | **Đóng gói Spring Boot Application:** Cập nhật file `application-prod.properties` trỏ kết nối JDBC về RDS Endpoint, build ứng dụng thành file `app.jar` tối ưu. | 03/06/2026 | 03/06/2026 | Spring Boot Maven Build |
| 4 (Thứ 5) | **Cấu hình Môi trường EC2 & Deploy Backend:** SSH vào máy chủ EC2, cài đặt Java OpenJDK 17, chuyển file `app.jar` lên EC2 và khởi chạy dịch vụ dưới dạng systemd service. | 04/06/2026 | 04/06/2026 | EC2 Deployment Guide |
| 5 (Thứ 6) | **Cấu hình Security Group Inter-Service:** Bật Inbound Rules trên DB Security Group cho phép port 3306 chỉ nhận traffic từ Security Group ID của EC2 Backend. Kiểm tra gọi thử API REST. | 05/06/2026 | 05/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html> |

### Kết quả đạt được Tuần 7:
* **Chuyển đổi Database thành công:** Migration toàn bộ dữ liệu sản phẩm, đơn hàng từ H2/MySQL local lên RDS MySQL an toàn không mất mát dữ liệu.
* **Vận hành Spring Boot Backend trên EC2:** Deploy và chạy ổn định ứng dụng Spring Boot trên Amazon EC2 dưới dạng dịch vụ chạy ngầm background service.
* **Bảo mật Kết nối Nội bộ:** Cấu hình Security Group liên kết trực tiếp giữa EC2 và RDS, đảm bảo database hoàn toàn biệt lập với Internet công cộng.
