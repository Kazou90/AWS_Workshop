---
title: "Week 11 Worklog"
date: "2026-06-29"
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:
- Test the **Analytics Dashboard** page, ensuring interactive chart components (**Chart.js**) render metric insights accurately from CSV report files downloaded via Amazon S3.
- Audit overall system security: Review **IAM Roles**, enforcing the Principle of Least Privilege for Lambda S3 bucket access.
- Execute cloud resource **Clean-up** procedures to purge unused components and optimize AWS operational costs.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Analytics Dashboard Testing:** Verify S3 CSV file fetching flows and inspect Chart.js visualization components (Hourly Clickstream Charts, Revenue Performance Graphs). | 29/06/2026 | 29/06/2026 | Chart.js Documentation |
| 2 (Tue) | **Data Reconciliation Verification:** Cross-check data points rendered on Chart.js graphs against raw S3 CSV file contents and RDS MySQL database records. | 30/06/2026 | 30/06/2026 | Data Verification Test |
| 3 (Wed) | **IAM Security Audit:** Audit all active IAM Roles, scoping `s3:GetObject` and `s3:PutObject` policies strictly to designated S3 Bucket ARNs. | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html> |
| 4 (Thu) | **Idle Resource Audit & Cost Clean-up:** Release unattached Elastic IPs, prune obsolete EBS Snapshots, and clean up temporary staging S3 buckets. | 02/07/2026 | 02/07/2026 | AWS Cost Optimization |
| 5 (Fri) | **Monthly Cloud Expenditure Review:** Review AWS Cost Explorer usage reports, confirming that total resource spend remains strictly within budget targets. | 03/07/2026 | 03/07/2026 | AWS Cost Explorer |

### Week 11 Achievements:
* **Interactive Analytics Dashboard Integration:** Rendered Clickstream user trends and sales metrics seamlessly using Chart.js powered by S3 CSV data lakes.
* **Hardened IAM Access Control:** Standardized IAM Policies across Lambda and EC2 under Least Privilege rules, mitigating privilege escalation risks.
* **Cloud Cost Optimization:** Pruned 100% of unused staging assets, maintaining an optimized monthly AWS cloud budget.
