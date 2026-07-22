---
title: "Worklog Tuần 10"
date: "2026-06-22"
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu Tuần 10:
- Thực hiện kiểm thử toàn diện luồng mua sắm cốt lõi trên hạ tầng Cloud: Tìm kiếm sản phẩm, Thêm vào giỏ hàng, Thanh toán (Checkout) và ghi nhận sự kiện Clickstream.
- Sử dụng **Amazon CloudWatch** giám sát log hệ thống, phát hiện và gỡ lỗi (debug) các vấn đề kết nối mạng (CORS policy, cấu hình API Gateway hoặc Security Group trên EC2).
- Tinh chỉnh chỉ mục (Indexes) cơ sở dữ liệu RDS MySQL và tối ưu hóa thời gian phản hồi (latency) của các API mua sắm.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Kiểm thử Luồng Mua sắm Cốt lõi:** Thực hiện kịch bản mua hàng end-to-end từ Frontend React Vite: Tìm kiếm, Thêm giỏ hàng, Checkout đơn hàng và tạo bản ghi giao dịch trên RDS. | 22/06/2026 | 22/06/2026 | E-Commerce Test Plan |
| 2 (Thứ 3) | **Kiểm tra Ghi nhận Sự kiện Clickstream:** Kiểm tra việc phát sự kiện nhấp chuột, xem sản phẩm, chuyển trang từ Frontend và ghi nhận log Clickstream vào hệ thống. | 23/06/2026 | 23/06/2026 | Clickstream Event Tracking |
| 3 (Thứ 4) | **Giám sát & Khắc phục Lỗi bằng CloudWatch Logs:** Rà soát CloudWatch Log Groups, khắc phục các lỗi chặn tên miền CORS (Cross-Origin Resource Sharing) giữa Frontend CloudFront và Backend EC2/API Gateway. | 24/06/2026 | 24/06/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html> |
| 4 (Thứ 5) | **Tối ưu hóa Truy vấn RDS MySQL Database:** Đánh giá các câu lệnh SQL truy vấn sản phẩm và đơn hàng, tạo các chỉ mục `INDEX` nâng cao trên RDS MySQL giúp tăng tốc truy vấn. | 25/06/2026 | 25/06/2026 | MySQL Index Optimization |
| 5 (Thứ 6) | **Tinh chỉnh Tốc độ Phản hồi API Mua sắm:** Tối ưu hóa thời gian phản hồi API Checkout dưới 200ms, giảm tải xử lý CPU cho máy chủ EC2 và nâng cao trải nghiệm người dùng. | 26/06/2026 | 26/06/2026 | Performance Testing |

### Kết quả đạt me Tuần 10:
* **Vận hành Trơn tru Luồng E-Commerce:** Kiểm thử và đảm bảo 100% tỷ lệ thành công cho luồng mua sắm, thanh toán và thu thập dữ liệu Clickstream trên Cloud.
* **Xử lý Triệt để Lỗi Mạng & CORS:** Làm chủ công cụ CloudWatch Logs để phát hiện và cấu hình chính xác chính sách CORS, giúp Frontend giao tiếp thông suốt với Backend.
* **Tối ưu Hiệu năng API & Database:** Cải thiện 45% tốc độ truy vấn cơ sở dữ liệu RDS MySQL và giảm thời gian phản hồi API xuống dưới 200ms.
