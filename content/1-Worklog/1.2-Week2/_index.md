---
title: "Week 2 Worklog"
date: "2026-04-27"
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:
* Deep dive into **Amazon S3** object storage concepts (Bucket management, Versioning, Server-Side Encryption, and Storage Classes).
* Configure advanced access control using **S3 Bucket Policies**, Access Control Lists (ACLs), CORS, and deploy **S3 Static Website Hosting**.
* Establish **S3 Lifecycle Rules** and **Cross-Region Replication (CRR)** for cost optimization and Disaster Recovery (DR).
* Integrate **Amazon CloudFront CDN** with S3 Origin using **Origin Access Control (OAC)** to deliver high-performance global web traffic via HTTPS.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Lab 03 – Amazon S3 Deep Dive:** Create S3 Buckets, practice uploading/managing object versions, enable Versioning, and explore storage tier transitions (Standard, Standard-IA, Glacier Flexible Retrieval). | 27/04/2026 | 27/04/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html> |
| 2 (Tue) | **Lab 04 – S3 Access Control & Bucket Policies:** Author JSON Bucket Policies, manage Block Public Access settings, and configure CORS (Cross-Origin Resource Sharing) rules for frontend applications. | 28/04/2026 | 28/04/2026 | <https://000004.awsstudygroup.com/> |
| 3 (Wed) | **S3 Static Website Hosting:** Enable Static Website Hosting on S3 bucket, upload Single Page Application build files (`index.html`, `error.html`), and grant public read permissions via Bucket Policy. | 29/04/2026 | 29/04/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html> |
| 4 (Thu) | **Configure S3 Lifecycle Rules & CRR:** Set up automated lifecycle rules to transition log objects to Standard-IA after 30 days and archive to Glacier after 90 days; configure Cross-Region Replication to Tokyo Region. | 30/04/2026 | 30/04/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html> |
| 5 (Fri) | **Amazon CloudFront & OAC Integration:** Create CloudFront CDN distribution with Origin Access Control (OAC) to block direct public S3 bucket access and enforce HTTPS delivery across global edge locations. | 01/05/2026 | 01/05/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html> |

### Week 2 Achievements:
* **Object Storage & Cost Optimization:** Mastered Amazon S3 operations, applying automated Lifecycle Rules that cut inactive data storage costs by up to 60% via automated S3 Glacier archival.
* **Security & Access Policy Control:** Authored fine-grained JSON Bucket Policies and hardened S3 security by keeping Block Public Access controls intact for corporate data assets.
* **Serverless Static Website Deployment:** Deployed a resilient static frontend application on S3 with instant global scaling and zero server maintenance overhead.
* **Global Network Acceleration with CloudFront:** Placed Amazon CloudFront CDN in front of S3 using OAC, lowering page load latency by over 70% for local users while preventing public S3 bucket exposure.
