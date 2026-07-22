---
title: "Worklog Tuần 8"
date: "2026-06-08"
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu Tuần 8:
- Cấu hình **Amazon Cognito User Pool** để xử lý toàn bộ luồng đăng ký, đăng nhập bảo mật và xác thực mã OTP gửi về Email/SMS cho người dùng mua sắm.
- Đóng gói ứng dụng **Frontend React Vite**, tải lên **Amazon S3 Bucket** và thiết lập phân phối qua **Amazon CloudFront CDN** với HTTPS mã hóa.

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 2) | **Cấu hình Cognito User Pool & OTP Trigger:** Thiết lập quy tắc đăng ký người dùng trong Cognito User Pool, cấu hình dịch vụ gửi mã OTP xác thực qua Email (SES/Cognito Default). | 08/06/2026 | 08/06/2026 | <https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-email-phone-verification.html> |
| 2 (Thứ 3) | **Tích hợp Auth SDK vào React Frontend:** Tích hợp thư viện AWS Amplify / Cognito SDK vào React Vite, viết giao diện Form Đăng ký, Nhập mã OTP và Đăng nhập nhận JWT Tokens. | 09/06/2026 | 09/06/2026 | AWS Amplify Auth Docs |
| 3 (Thứ 4) | **Build ứng dụng Frontend React Vite:** Đóng gói ứng dụng React Vite sang bộ tệp tĩnh tối ưu (`dist/` folder chứa HTML, JS bundle, CSS) bằng lệnh `npm run build`. | 10/06/2026 | 10/06/2026 | Vite Build Guide |
| 4 (Thứ 5) | **Deploy Frontend lên S3 Bucket:** Tải toàn bộ thư mục `dist/` lên S3 Bucket, cấu hình S3 Static Website Hosting và Bucket Policy cho phép CloudFront đọc dữ liệu. | 11/06/2026 | 11/06/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html> |
| 5 (Thứ 6) | **Cấu hình CloudFront CDN & Single Page App Routing:** Tạo CloudFront Distribution trỏ về S3 Bucket, cấu hình Custom Error Responses (chuyển hướng lỗi 403/404 về `index.html` cho React Router). | 12/06/2026 | 12/06/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/DefaultPages.html> |

### Kết quả đạt được Tuần 8:
* **Xác thực Tài khoản Bảo mật qua OTP:** Xây dựng hoàn chỉnh luồng đăng ký/đăng nhập khách hàng tích hợp mã xác thực OTP qua Amazon Cognito.
* **Triển khai Frontend Toàn cầu với CloudFront:** Deploy giao diện mua sắm React Vite lên S3 và CloudFront, giúp tốc độ tải trang phản hồi tức thì và hỗ trợ HTTPS chuẩn bảo mật.
