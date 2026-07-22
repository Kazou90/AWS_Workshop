---
title: "Worklog Tuần 5"
date: "2026-05-18"
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu Tuần 5:
- Xây dựng các luồng xử lý dữ liệu tự động hóa hoàn toàn bằng cách kết hợp **Amazon EventBridge** và **AWS Lambda**.
- Thực hành cấu hình các bộ lập lịch thời gian (**Cron Jobs / Scheduled Rules**) trên EventBridge để kích hoạt các tác vụ hệ thống trên Cloud theo chu kỳ định kỳ.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Thiết kế Luồng Xử lý Dữ liệu Tự động:** Vẽ sơ đồ luồng dữ liệu xử lý sự kiện, xác định các trigger đầu vào và kết quả đầu ra cần ghi nhận. | 18/05/2026 | 18/05/2026 | AWS Event-Driven Patterns |
| 2 (Thứ 3) | **Cấu hình EventBridge Scheduled Rules (Cron Expression):** Tìm hiểu cú pháp biểu thức Cron trong AWS (`cron(0 0 * * ? *)`), tạo Scheduled Rule trên EventBridge để lên lịch chạy tác vụ. | 19/05/2026 | 19/05/2026 | <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-scheduled-rule.html> |
| 3 (Thứ 4) | **Viết Lambda Task thực thi định kỳ:** Lập trình hàm AWS Lambda thực hiện tác vụ kiểm tra dữ liệu, trích xuất báo cáo hoặc dọn dẹp log định kỳ theo lịch từ EventBridge. | 20/05/2026 | 20/05/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/services-cloudwatchevents.html> |
| 4 (Thứ 5) | **Kiểm thử Luồng Lập lịch Tự động:** Theo dõi log thực thi trên Amazon CloudWatch Logs để đảm bảo hàm Lambda được kích hoạt chính xác theo chu kỳ thời gian đã cấu hình. | 21/05/2026 | 21/05/2026 | CloudWatch Logs Console |
| 5 (Thứ 6) | **Tối ưu hóa & Xử lý Ngoại lệ:** Thêm cơ chế Dead Letter Queue (DLQ) với SQS để lưu trữ các sự kiện bị lỗi và retry tự động khi tác vụ Lambda gặp sự cố. | 22/05/2026 | 22/05/2026 | AWS DLQ Best Practices |

### Kết quả đạt được Tuần 5:
* **Tự động hóa Tác vụ trên Cloud:** Xây dựng thành công hệ thống lập lịch tự động không cần máy chủ (Serverless Cron Jobs) thay thế việc chạy cron job truyền thống trên EC2.
* **Xử lý Dữ liệu Định kỳ Đáng tin cậy:** Đảm bảo các tác vụ quét log, tổng hợp dữ liệu báo cáo được thực thi đúng giờ 100% với cơ chế xử lý lỗi DLQ an toàn.
