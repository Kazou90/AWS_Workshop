---
title: "Worklog Tuần 1"
date: "2026-04-17"
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu Tuần 1:
- Tham gia sự kiện Kickoff chương trình **First Cloud AI Journey**, làm quen với đồng nghiệp và nắm rõ lộ trình đào tạo/thực tập.
- Thiết lập cảnh báo ngân sách tự động bằng **AWS Budgets** để kiểm soát chi phí tài nguyên Cloud ngay từ đầu.
- Nắm vững kiến thức quản trị định danh và phân quyền an toàn với **AWS IAM** (IAM Users, Groups, Roles, Policies) và kích hoạt bảo mật đa yếu tố (MFA).

### Các công việc triển khai trong tuần:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 (Thứ 6, 17/04) | **Kickoff & Lộ trình Thực tập:** Tham gia buổi Kickoff AWS First Cloud AI Journey, lắng nghe định hướng từ các diễn giả, làm quen với team và đọc kỹ nội quy văn phòng. | 17/04/2026 | 17/04/2026 | Slide Kickoff FCAJ |
| 2 (Thứ 2, 20/04) | **Cấu hình AWS Budgets:** Tìm hiểu mô hình chi trả theo mức sử dụng (Pay-as-you-go). Tạo hạn mức ngân sách và thiết lập tự động gửi email cảnh báo qua SNS khi chi phí chạm ngưỡng $10 và $20. | 20/04/2026 | 20/04/2026 | <https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html> |
| 3 (Thứ 3, 21/04) | **Lab AWS IAM - Khởi tạo IAM Users & Groups:** Tạo tài khoản IAM User cá nhân, tạo nhóm quản trị AdministratorGroup, thực hành gán Managed Policies và khóa tài khoản Root User. | 21/04/2026 | 21/04/2026 | <https://000002.awsstudygroup.com/> |
| 4 (Thứ 4, 22/04) | **Bật xác thực đa yếu tố MFA:** Cấu hình Virtual MFA Device (Google Authenticator) cho cả tài khoản Root và các IAM Users để nâng cao cấp độ bảo mật hệ thống. | 22/04/2026 | 22/04/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html> |
| 5 (Thứ 5, 23/04) | **Nghiên cứu IAM Roles & Custom Inline Policies:** Soạn thảo cấu hình JSON Policy theo nguyên tắc Least Privilege và thử nghiệm tạo IAM Roles phân quyền tạm thời cho dịch vụ. | 23/04/2026 | 23/04/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html> |
| 6 (Thứ 6, 24/04) | **Thực hành Switch Role & Kiểm tra an toàn:** Kiểm tra lại toàn bộ phân quyền tài khoản, thực hành tính năng Switch Role trên AWS Console để chuyển đổi linh hoạt giữa các môi trường. | 24/04/2026 | 24/04/2026 | <https://000002.awsstudygroup.com/4-switch-roles/> |

### Kết quả đạt được Tuần 1:
* **Hòa nhập Môi trường & Lộ trình:** Đã hiểu rõ mục tiêu chương trình thực tập, thiết lập tinh thần học tập chủ động và kết nối thành công với các thành viên trong nhóm.
* **Tối ưu Chi phí Đám mây:** Kích hoạt thành công AWS Budgets, giúp phát hiện và ngăn chặn sớm nguy cơ phát sinh chi phí ngoài ý muốn trong suốt quá trình làm lab.
* **Bảo mật Định danh Chuẩn Enterprise:** Vô hiệu hóa việc sử dụng Root User hàng ngày, làm chủ IAM phân quyền tối thiểu và bảo vệ 100% tài khoản với MFA 2 lớp.
