---
title: "Worklog Tuần 4"
date: "2026-05-11"
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu Tuần 4:
- Tìm hiểu kiến trúc hướng sự kiện (Event-Driven Architecture) kết hợp **Amazon EventBridge** và **AWS Lambda** để xử lý các sự kiện hệ thống bất đồng bộ.
- Tìm hiểu và thực hành cấu hình dịch vụ **Amazon Cognito (User Pool)** để quản lý xác thực, đăng ký, đăng nhập và cấp Token cho người dùng ứng dụng.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Nghiên cứu Kiến trúc Event-Driven:** Tìm hiểu cơ chế hoạt động của Event Bus, Event Rules và Event Targets trên Amazon EventBridge. | 11/05/2026 | 11/05/2026 | <https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html> |
| 2 (Thứ 3) | **Viết hàm AWS Lambda xử lý Event:** Viết các hàm Lambda (Node.js/Python) xử lý logic khi nhận dữ liệu sự kiện truyền sang từ EventBridge. | 12/05/2026 | 12/05/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/welcome.html> |
| 3 (Thứ 4) | **Tích hợp EventBridge với AWS Lambda Target:** Tạo Event Rule trên EventBridge để lắng nghe sự kiện thay đổi trạng thái và tự động kích hoạt Lambda xử lý. | 13/05/2026 | 13/05/2026 | <https://000012.awsstudygroup.com/> |
| 4 (Thứ 5) | **Khởi tạo Amazon Cognito User Pool:** Tìm hiểu dịch vụ Amazon Cognito, khởi tạo User Pool quản lý danh sách người dùng, cấu hình thuộc tính bắt buộc (Email, Phone). | 14/05/2026 | 14/05/2026 | <https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html> |
| 5 (Thứ 6) | **Thử nghiệm Đăng ký & Cấp Token Authentication:** Cấu hình App Client trong Cognito, thử nghiệm API tạo tài khoản người dùng, xác thực email/SMS và nhận JWT Tokens (ID Token, Access Token). | 15/05/2026 | 15/05/2026 | AWS Cognito SDK Docs |

### Kết quả đạt được Tuần 4:
* **Làm chủ Kiến trúc Hướng Sự kiện (Event-Driven):** Hiểu rõ tư duy phát triển ứng dụng hiện đại sử dụng EventBridge kết hợp Lambda giúp giảm độ phụ thuộc (Decoupling) giữa các dịch vụ.
* **Quản trị Xác thực Người dùng với Amazon Cognito:** Tạo thành công Cognito User Pool quản lý thông tin tài khoản người dùng và cấp mã JWT Tokens bảo mật cho ứng dụng web.
