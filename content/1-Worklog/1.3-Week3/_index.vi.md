---
title: "Worklog Tuần 3"
date: "2026-05-04"
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu Tuần 3:
- Khởi tạo, cấu hình và vận hành cơ sở dữ liệu quan hệ **Amazon RDS (MySQL)**, kiểm tra kết nối mạng từ máy ảo EC2.
- Nghiên cứu mạng lưới phân phối nội dung **Amazon CloudFront CDN**, kết hợp CloudFront với Amazon S3 Origin để tối ưu hóa tốc độ tải trang web và áp dụng SSL/TLS.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Khởi tạo Amazon RDS MySQL Database:** Khởi tạo instance RDS MySQL (phân hạng Free Tier), cấu hình DB Subnet Group, thiết lập master username/password và đặt tên database ban đầu. | 04/05/2026 | 04/05/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html> |
| 2 (Thứ 3) | **Cấu hình Tường lửa DB & Kiểm tra Kết nối:** Tạo DB Security Group mở port 3306 chỉ cho phép IP của máy ảo EC2. Thực hành cài đặt `mysql-client` trên EC2 và truy vấn thử nghiệm thành công. | 05/05/2026 | 05/05/2026 | <https://000008.awsstudygroup.com/> |
| 3 (Thứ 4) | **Nghiên cứu Amazon CloudFront CDN:** Tìm hiểu cơ chế caching tại các Edge Location, Origin Server, CNAMEs và chứng chỉ SSL/TLS khi tích hợp CDN với S3 Bucket. | 06/05/2026 | 06/05/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html> |
| 4 (Thứ 5) | **Cấu hình CloudFront Distribution với S3 Origin:** Tạo CloudFront Distribution trỏ đến S3 Website Bucket, thiết lập Origin Access Control (OAC) để bảo vệ S3 origin và bật mã hóa HTTPS. | 07/05/2026 | 07/05/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html> |
| 5 (Thứ 6) | **Đánh giá Hiệu năng & Tối ưu Caching:** Đo đạc tốc độ tải trang trước và sau khi qua CloudFront Edge Locations (giảm độ trễ truy cập đến 70%), cấu hình Cache Behaviors và TTL cho file static. | 08/05/2026 | 08/05/2026 | AWS CloudFront Best Practices |

### Kết quả đạt được Tuần 3:
* **Vận hành Relational Database trên Cloud:** Khởi tạo thành công database RDS MySQL, kiểm soát phân quyền kết nối qua Security Group chỉ cho phép giao tiếp nội bộ từ EC2.
* **Tăng tốc Truy cập Mạng Toàn cầu với CloudFront:** Tích hợp thành công CloudFront CDN phía trước S3 Bucket, giúp tối ưu hóa tốc độ tải trang web, bảo mật dữ liệu gốc qua OAC và hỗ trợ HTTPS mặc định.
