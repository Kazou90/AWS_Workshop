---
title: "Week 9 Worklog"
date: "2026-06-15"
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:
* Manage custom domain DNS routing for the production application using **Amazon Route 53** (Hosted Zones, Record Sets, Alias Records).
* Issue, validate, and manage free SSL/TLS certificates via **AWS Certificate Manager (ACM)**.
* Configure Application Load Balancer (ALB) HTTPS Listeners (Port 443) and set up automatic HTTP-to-HTTPS (301 Redirect) rules.
* Implement advanced **Path-Based Routing** on ALB to distribute traffic to respective microservices efficiently.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Amazon Route 53 Custom Domain Setup:** Create Public Hosted Zones in Route 53, configuring A/AAAA Alias records pointing custom domain endpoints to ALB and CloudFront distributions. | 15/06/2026 | 15/06/2026 | <https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html> |
| 2 (Tue) | **AWS Certificate Manager (ACM) Provisioning:** Request public wildcard SSL/TLS certificates for custom domains, completing automated DNS validation via Route 53 CNAME records. | 16/06/2026 | 16/06/2026 | <https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html> |
| 3 (Wed) | **ALB HTTPS Listener & Redirection Rules:** Attach ACM certificates to ALB HTTPS Listeners (Port 443). Configure HTTP Listeners (Port 80) to automatically 301-redirect incoming HTTP traffic to HTTPS. | 17/06/2026 | 17/06/2026 | <https://docs.aws.amazon.com/alb/latest/userguide/component-automated-redirection.html> |
| 4 (Thu) | **Path-Based Routing Configuration:** Author ALB Listener Rules routing `/api/*` request paths to Backend ECS Services and `/*` paths to Frontend S3/CloudFront distributions. | 18/06/2026 | 18/06/2026 | <https://docs.aws.amazon.com/alb/latest/userguide/listener-update-rules.html> |
| 5 (Fri) | **Security Audit & SSL Labs Verification:** Audit SSL/TLS cipher suites via SSL Labs Test (achieving A+ rating), verifying DNS resolution latency and CORS policy headers. | 19/06/2026 | 19/06/2026 | <https://www.ssllabs.com/ssltest/> |

### Week 9 Achievements:
* **Optimized DNS Routing:** Applied Route 53 Alias records to speed up DNS resolution and reduce latency for local users.
* **100% End-to-End HTTPS Encryption:** Encrypted all network traffic using ACM managed SSL/TLS certificates with zero-cost auto-renewals.
* **Flexible Microservice Architecture:** Configured ALB Path-Based Routing, consolidating multi-tier microservices seamlessly under a unified brand domain endpoint.
