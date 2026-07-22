---
title: "Week 3 Worklog"
date: "2026-05-04"
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:
- Provision, configure, and operate an **Amazon RDS (MySQL)** relational database instance, validating secure network connectivity from EC2 virtual machines.
- Research **Amazon CloudFront CDN**, integrating CloudFront with Amazon S3 Origins to optimize web page load times and enforce HTTPS encryption.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Provision Amazon RDS MySQL Database:** Launch an RDS MySQL instance (Free Tier), configure DB Subnet Groups, set master database credentials, and initialize default schema settings. | 04/05/2026 | 04/05/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html> |
| 2 (Tue) | **DB Security Group & Connectivity Check:** Create DB Security Groups opening port 3306 restricted to EC2 instance security groups. Install `mysql-client` on EC2 and verify DB connection. | 05/05/2026 | 05/05/2026 | <https://000008.awsstudygroup.com/> |
| 3 (Wed) | **Research Amazon CloudFront CDN:** Study Edge Location caching mechanics, Origin Servers, CNAME aliases, and SSL/TLS certificate integration with S3 buckets. | 06/05/2026 | 06/05/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html> |
| 4 (Thu) | **Configure CloudFront Distribution with S3 Origin:** Create CloudFront Distributions targeting the S3 Website Bucket, set Origin Access Control (OAC) to secure S3 origins, and enable HTTPS. | 07/05/2026 | 07/05/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html> |
| 5 (Fri) | **Performance Benchmarking & Cache Tuning:** Measure page load speeds before and after CloudFront edge deployment (achieving up to 70% latency reduction), configuring Cache Behaviors and TTLs. | 08/05/2026 | 08/05/2026 | AWS CloudFront Best Practices |

### Week 3 Achievements:
* **Cloud Relational Database Operations:** Successfully deployed RDS MySQL databases, enforcing tight access control via Security Groups allowing private EC2-only communication.
* **Global Web Acceleration with CloudFront:** Placed CloudFront CDN in front of S3, accelerating content delivery globally while securing S3 origins with OAC and enforcing HTTPS encryption.
