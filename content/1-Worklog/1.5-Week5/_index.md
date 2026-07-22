---
title: "Week 5 Worklog"
date: "2026-05-18"
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:
* Implement comprehensive logging and operational monitoring using **Amazon CloudWatch** (Logs, Custom Metrics, CloudWatch Agent, Alarms, SNS notifications).
* Manage EC2 instances securely without SSH key management using **AWS Systems Manager (SSM) Session Manager**.
* Research Serverless architecture, build event-driven functions with **AWS Lambda**, and manage REST APIs with **Amazon API Gateway**.
* Build an automated event-driven serverless pipeline combining Amazon S3, AWS Lambda, and Amazon DynamoDB.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Lab 11 – Amazon CloudWatch Deep Dive:** Install CloudWatch Agent on EC2, aggregate application log streams, build metric filters for HTTP 5xx errors, create CloudWatch Dashboards, and set up SNS alarm notifications. | 18/05/2026 | 18/05/2026 | <https://000011.awsstudygroup.com/> |
| 2 (Tue) | **Bastion-less Access via AWS SSM:** Attach IAM Instance Profiles with `AmazonSSMManagedInstanceCore` to EC2 instances, establish interactive shell sessions via Session Manager without opening inbound port 22. | 19/05/2026 | 19/05/2026 | <https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html> |
| 3 (Wed) | **Lab 12 – AWS Lambda Serverless Functions:** Write Lambda handlers in Node.js/Python, configure runtime parameters, memory/timeout limits, and assign secure IAM Execution Roles. | 20/05/2026 | 20/05/2026 | <https://000012.awsstudygroup.com/> |
| 4 (Thu) | **Amazon API Gateway REST API:** Build REST APIs in API Gateway, configure Lambda Proxy Integration, CORS policies, stage deployments (`dev`/`prod`), and API throttling limits. | 21/05/2026 | 21/05/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html> |
| 5 (Fri) | **Event-Driven Serverless Pipeline:** Configure S3 Event Notifications to invoke Lambda functions automatically upon file upload, extracting metadata and storing records directly in DynamoDB. | 22/05/2026 | 22/05/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html> |

### Week 5 Achievements:
* **System Observability & Monitoring:** Built real-time monitoring dashboards and automated SNS alert workflows with CloudWatch, reducing incident detection time significantly.
* **Hardened Server Administration:** Eliminated SSH key maintenance overhead and port 22 vulnerability exposure by enforcing AWS SSM Session Manager.
* **Serverless Architecture Mastery:** Deployed a zero-maintenance Serverless stack (API Gateway + Lambda + DynamoDB) capable of handling concurrent traffic bursts out of the box.
