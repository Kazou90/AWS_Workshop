---
title: "Worklog Tuần 11"
date: "2026-06-29"
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu Tuần 11:
- Kiểm tra trang **Analytics Dashboard**, đảm bảo biểu đồ trực quan hóa (**Chart.js**) hiển thị chính xác các chỉ số từ tệp CSV phân tích tải xuống qua Amazon S3.
- Rà soát toàn bộ kiến trúc bảo mật hệ thống: Kiểm tra **IAM Roles**, đảm bảo áp dụng nguyên tắc cấp quyền tối thiểu (Least Privilege) cho Lambda khi truy cập S3.
- Thực hiện dọn dẹp (**Clean-up**) các tài nguyên không còn sử dụng để tối ưu hóa chi phí vận hành trên AWS Cloud.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Kiểm thử Trang Analytics Dashboard:** Kiểm tra việc kết nối lấy file CSV từ S3, kiểm tra các linh kiện biểu đồ Chart.js (Biểu đồ doanh thu, Biểu đồ Clickstream theo giờ). | 29/06/2026 | 29/06/2026 | Chart.js Documentation |
| 2 (Thứ 3) | **Xác minh Khớp dữ liệu Báo cáo:** Kiểm tra và so sánh đối chiếu số liệu hiển thị trên biểu đồ Chart.js với dữ liệu thô trong file CSV và cơ sở dữ liệu RDS MySQL. | 30/06/2026 | 30/06/2026 | Data Verification Test |
| 3 (Thứ 4) | **Audit Bảo mật IAM Roles & Policies:** Rà soát lại tất cả các IAM Roles, giới hạn chính xác quyền `s3:GetObject` và `s3:PutObject` cho Lambda trên đúng ARN Bucket target. | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html> |
| 4 (Thứ 5) | **Rà soát & Dọn dẹp Tài nguyên Nhàn rỗi (Cost Clean-up):** Thu hồi các Elastic IP không gán máy chủ, xóa các EBS Snapshots thử nghiệm cũ và dọn dẹp các S3 Bucket rác. | 02/07/2026 | 02/07/2026 | AWS Cost Optimization |
| 5 (Thứ 6) | **Đánh giá Báo cáo Chi phí Hàng tháng:** Phân tích hóa đơn sử dụng tài nguyên trên AWS Cost Explorer, đảm bảo chi phí nằm trong hạn mức cho phép. | 03/07/2026 | 03/07/2026 | AWS Cost Explorer |

### Kết quả đạt được Tuần 11:
* **Hoàn thiện Analytics Dashboard Trực quan:** Hiển thị mượt mà các biểu đồ doanh thu và hành vi Clickstream sử dụng Chart.js đọc dữ liệu từ file CSV trên S3.
* **Siết chặt Bảo mật Hệ thống:** Chuẩn hóa các chính sách IAM Policy cho Lambda và EC2 theo đúng nguyên tắc Least Privilege, chặn hoàn toàn các nguy cơ rò rỉ quyền hạn.
* **Tối ưu Chi phí Vận hành:** Thu hồi và dọn dẹp 100% các tài nguyên thừa, tối ưu hóa mức chi tiêu AWS hàng tháng một cách hiệu quả.
