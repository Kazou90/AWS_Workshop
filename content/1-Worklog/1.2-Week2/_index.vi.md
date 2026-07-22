---
title: "Worklog Tuần 2"
date: "2026-04-27"
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu Tuần 2:
- Khởi tạo, cấu hình tường lửa **Security Group** và quản lý kết nối SSH an toàn tới máy chủ **Amazon EC2**.
- Tìm hiểu các khái niệm cốt lõi của dịch vụ lưu trữ đối tượng **Amazon S3** (Buckets, Objects, Storage Classes, Versioning).
- Thực hành thiết lập **S3 Static Website Hosting**, phân quyền truy cập public qua Bucket Policy để lưu trữ giao diện giao diện ứng dụng web.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Khởi tạo Máy chủ Amazon EC2:** Lựa chọn Amazon Linux 2023 AMI, chọn instance type `t3.micro`, tạo Key Pair và khởi chạy thành công máy chủ ảo EC2. | 27/04/2026 | 27/04/2026 | <https://docs.aws.amazon.com/ec2/> |
| 2 (Thứ 3) | **Cấu hình Security Group & Kết nối SSH:** Thiết lập Inbound Rules cho Security Group (mở port 22 SSH và port 80 HTTP), thực hành truy cập máy chủ EC2 từ terminal qua SSH Key. | 28/04/2026 | 28/04/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html> |
| 3 (Thứ 4) | **Làm quen với Amazon S3 Storage:** Khởi tạo S3 Bucket, tìm hiểu nguyên lý lưu trữ theo định dạng Key-Value đối tượng, tải tệp tin mẫu và bật tính năng Object Versioning. | 29/04/2026 | 29/04/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html> |
| 4 (Thứ 5) | **Cấu hình S3 Static Website Hosting:** Bật tính năng hosting trang web tĩnh trên S3 Bucket, tải các file `index.html` và `error.html` lên root bucket. | 30/04/2026 | 30/04/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html> |
| 5 (Thứ 6) | **Phân quyền Truy cập Public & Kiểm thử:** Tắt tính năng Block Public Access, viết bản JSON Bucket Policy cấp quyền `s3:GetObject` cho tất cả người dùng và kiểm tra đường dẫn website. | 01/05/2026 | 01/05/2026 | <https://000004.awsstudygroup.com/> |

### Kết quả đạt được Tuần 2:
* **Vận hành Máy chủ EC2 Thành thạo:** Làm chủ việc khởi tạo, quản lý khóa SSH Key Pairs và cấu hình Security Group bảo vệ máy chủ ảo EC2.
* **Lưu trữ Dữ liệu với S3:** Nắm vững cấu trúc lưu trữ đối tượng không giới hạn của S3 và quản lý các phiên bản dữ liệu an toàn.
* **Triển khai Web Tĩnh trên S3:** Host thành công giao diện web tĩnh trực tiếp trên S3 Bucket với chi phí thấp và khả năng tự động mở rộng chịu tải cực cao.
