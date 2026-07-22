---
title: "Event 1"
date: 2026-04-17
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Kick Off AWS First Cloud AI Journey

### Mục Đích Của Sự Kiện

- Định hình lộ trình nghiên cứu và thực hành điện toán đám mây bài bản cho các kỹ sư cũng như sinh viên trong cộng đồng.
-Cung cấp nền tảng kiến thức cốt lõi về hệ sinh thái AWS, bao gồm Hạ tầng toàn cầu (Global Infrastructure), Quản lý định danh (IAM) và các công cụ quản trị (Management Tools).

### Danh Sách Diễn Giả

- **Nguyen Gia Hung** - Head of Solution Architect, FCAJ

### Nội Dung Nổi Bật

#### 1. Khởi động hành trình (Prologue & Mindset)

- Giới thiệu 6 nguyên tắc cốt lõi: Builder & Troubleshooter, Teamwork, Resilience, Hands-on & Sharing, Invest in yourself, Lifelong learning.
- Mục tiêu thực tiễn: Thực hành "from scratch" và hoàn thành 5 projects thực tế để chứng minh năng lực chuyên môn.

#### 2. Nền tảng hạ tầng AWS

- Giá trị cốt lõi từ Điện toán đám mây: Tiết kiệm ngân sách nhờ mô hình chi trả theo thực tế (Pay-as-you-go), tăng tốc độ triển khai sản phẩm và khả năng mở rộng quy mô trên toàn cầu một cách linh hoạt.
- Tổng quan về Hạ tầng toàn cầu (Global Infrastructure): Mô hình phân cấp mạch lạc từ Trung tâm dữ liệu (Data Center) đến Cụm vùng khả dụng (Availability Zone - AZ, đảm bảo cấu hình tối thiểu 2 AZ để dự phòng rủi ro) và Vùng địa lý (Region).
- Giới thiệu Edge Location và Local Zone để tối ưu độ trễ tại Việt Nam.

#### 3. Tối ưu hóa chi phí (Cost Optimization)

- Nguyên tắc Right-sizing: Chỉ cấp phát tài nguyên đúng với nhu cầu hiện tại, không mua dư thừa như môi trường On-premise.
- Áp dụng linh hoạt các hình thức thanh toán: Đặt trước dài hạn, tận dụng tài nguyên nhàn rỗi giá rẻ (Spot Instances) hoặc chuyển sang kiến trúc không máy chủ.
- Giám sát thông minh bằng công cụ: Thiết lập hạn mức và cảnh báo tự động qua AWS Budgets, kết hợp dự toán ngân sách dự án chính xác với AWS Pricing Calculator.
- 3 phương thức tương tác chính: AWS Management Console, AWS CLI (tự động hóa qua terminal) và AWS SDK.

#### 4. Công cụ quản lý & Bảo mật cơ bản

- Cảnh báo rủi ro nghiêm trọng khi lạm dụng tài khoản gốc (Root User). Khuyến nghị luôn luôn vận hành bằng IAM User, thiết lập bảo mật đa lớp (MFA) và quản lý nghiêm ngặt các mã khóa truy cập.

### Những Gì Học Được

#### Kiến Trúc Kỹ Thuật

- **Thiết kế tính sẵn sàng cao (HA) & Khôi phục sau thảm họa (DR):** Làm chủ kỹ thuật cấu hình ứng dụng phân tán trên tối thiểu 2 AZs để duy trì uptime liên tục
- **Tối ưu trải nghiệm với Edge Computing:** Nhận diện đúng thời điểm cần điều hướng lưu lượng dữ liệu tĩnh (media, hình ảnh) qua các Edge Location nhờ CloudFront, giảm tải trực tiếp cho máy chủ tại Region.

#### Chiến Lược Hiện Đại Hóa Hệ Thống

- **Ưu tiên kiến trúc Serverless:** Cân nhắc thay thế hệ thống máy chủ ảo (EC2) truyền thống bằng Serverless đối với các ứng dụng có lượng truy cập biến động mạnh để tối ưu chi phí.
- **Áp dụng Well-Architected Framework:** Biến việc dùng framework này thành một thói quen để đánh giá, sinh báo cáo (report) và cải thiện hệ thống hiện tại định kỳ.

### Trải Nghiệm Trong Event

Định hình lại phương pháp học tập và làm việc trong kỷ nguyên Cloud kết hợp AI. Những ấn tượng sâu sắc bao gồm:

- **Sức mạnh từ sự kết nối cộng đồng:** Tinh thần chia sẻ không giới hạn từ các chuyên gia thuộc AWS Study Group. Điểm giá trị không chỉ dừng lại ở kiến thức kỹ thuật, mà còn là tư duy làm nghề và triết lý "no sharing, no growing" (không chia sẻ, không phát triển).
- **Cách tiếp cận thực hành thực chất:** Việc bắt buộc phải tự xây dựng các bài lab từ con số 0 thay vì rập khuôn theo các bước có sẵn giúp tôi thấu hiểu tận gốc bản chất của hệ thống.
- **Sức mạnh của Kiro:** Các phần trình diễn về Kiro Autonomous Agent và Custom Agent đã mở ra một viễn cảnh thực tế, nơi lập trình viên sẽ cộng tác với AI như những người đồng nghiệp thực thụ.

### Bài Học Rút Ra

- Lĩnh vực Cloud và GenAI đang dịch chuyển với tốc độ chóng mặt. Việc đưa AI (như Kiro) vào quy trình phát triển không còn là một giải pháp tùy chọn, mà đã trở thành yếu tố then chốt để đột phá hiệu suất làm việc.
-Tinh thần đồng đội (Teamwork).


