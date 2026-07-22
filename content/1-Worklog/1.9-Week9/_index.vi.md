---
title: "Worklog Tuần 9"
date: "2026-06-15"
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu Tuần 9:
- Cấu hình **Amazon S3 Bucket** làm kho chứa dữ liệu thô (**Raw Data Lake**) lưu trữ các tệp báo cáo định dạng CSV.
- Triển khai hàm **AWS Lambda ETL** thực hiện trích xuất dữ liệu Clickstream (User behavior), dữ liệu phiên làm việc (Session) và doanh thu từ Backend/RDS.
- Cấu hình **Amazon EventBridge Scheduled Rule** tự động kích hoạt hàm Lambda xuất file CSV báo cáo định kỳ theo lịch lên S3.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Cấu hình S3 Raw Data Lake Bucket:** Khởi tạo S3 Bucket chuyên biệt `raw-clickstream-data`, tạo cấu trúc cây thư mục lưu trữ theo mốc ngày (`/year=2026/month=06/day=15/`). | 15/06/2026 | 15/06/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html> |
| 2 (Thứ 3) | **Lập trình Lambda ETL Function (Trích xuất Dữ liệu):** Viết mã nguồn hàm Lambda (Python/Node.js) thực hiện kết nối database, truy vấn thống kê dữ liệu Clickstream, phiên làm việc và doanh thu bán hàng. | 16/06/2026 | 16/06/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/welcome.html> |
| 3 (Thứ 4) | **Định dạng & Chuyển đổi dữ liệu sang CSV:** Bổ sung logic biến đổi dữ liệu thành các tệp CSV chuẩn hóa (`clickstream_report.csv`, `revenue_summary.csv`) và đẩy lên S3 Bucket via AWS SDK. | 17/06/2026 | 17/06/2026 | AWS SDK Boto3 Docs |
| 4 (Thứ 5) | **Tự động hóa Luồng ETL bằng EventBridge Cron:** Tạo Scheduled Rule trên EventBridge cấu hình Cron job kích hoạt tự động hàm Lambda ETL xuất báo cáo CSV định kỳ lúc 00:00 hàng ngày. | 18/06/2026 | 18/06/2026 | <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-scheduled-rule.html> |
| 5 (Thứ 6) | **Kiểm thử Kiểm soát Chất lượng File CSV:** Kiểm tra việc tự động ghi file CSV lên S3, xác minh nội dung dữ liệu Clickstream và kiểm tra log thực thi trên CloudWatch Logs. | 19/06/2026 | 19/06/2026 | CloudWatch Logs Console |

### Kết quả đạt được Tuần 9:
* **Xây dựng Data Lake Lưu trữ Dữ liệu Thô:** Tạo thành công kho lưu trữ dữ liệu thô trên S3 cho các báo cáo Clickstream và doanh thu với chi phí cực thấp.
* **Tự động hóa Đường ống ETL (Serverless ETL Pipeline):** Triển khai luồng trích xuất dữ liệu không máy chủ hoạt động tự động 100% qua sự phối hợp giữa EventBridge và Lambda.
