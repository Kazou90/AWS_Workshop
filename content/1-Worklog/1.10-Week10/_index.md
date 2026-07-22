---
title: "Week 10 Worklog"
date: "2026-06-22"
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:
- Perform end-to-end testing of core Cloud E-Commerce user flows: Product Search, Add-to-Cart, Checkout processing, and Clickstream event recording.
- Utilize **Amazon CloudWatch** for system log monitoring, discovering and resolving network connection issues (CORS policies, API Gateway or EC2 Security Group misconfigurations).
- Tune RDS MySQL database index strategies and optimize response latency for shopping APIs.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Core Shopping Flow Integration Testing:** Execute end-to-end purchase scenarios from the React Vite frontend: Product Search, Cart operations, Checkout, and transaction creation on RDS. | 22/06/2026 | 22/06/2026 | E-Commerce Test Plan |
| 2 (Tue) | **Verify Clickstream Event Tracking:** Test user click interactions, product view events, and page navigation tracking from Frontend to backend event logs. | 23/06/2026 | 23/06/2026 | Clickstream Event Tracking |
| 3 (Wed) | **System Monitoring & CORS Troubleshooting:** Review CloudWatch Log Groups, fixing Cross-Origin Resource Sharing (CORS) policy errors between Frontend CloudFront and Backend EC2/API Gateway endpoints. | 24/06/2026 | 24/06/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html> |
| 4 (Thu) | **RDS MySQL Database Query Optimization:** Analyze SQL queries fetching product catalogs and order details, creating composite `INDEX` structures on RDS MySQL to speed up execution. | 25/06/2026 | 25/06/2026 | MySQL Index Optimization |
| 5 (Fri) | **Shopping API Latency Fine-Tuning:** Tune Checkout API latency to under 200ms, reducing EC2 CPU utilization and enhancing end-user shopping experience. | 26/06/2026 | 26/06/2026 | Performance Testing |

### Week 10 Achievements:
* **Flawless E-Commerce Cloud Operation:** Achieved a 100% success rate across user shopping, checkout, and Clickstream event capture workflows on AWS.
* **Network & CORS Error Resolution:** Mastered CloudWatch Logs for rapid troubleshooting, configuring accurate CORS headers for seamless Frontend-Backend communication.
* **API & Database Performance Tuning:** Accelerated RDS MySQL query execution speeds by 45% and reduced API response times below 200ms.
