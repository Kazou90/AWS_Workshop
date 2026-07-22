---
title: "Week 7 Worklog"
date: "2026-06-01"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:
- Provision **RDS MySQL** instances, performing data migration of product catalogs and order history from local to the Cloud.
- Package **Spring Boot Backend** applications (`.jar`), install Java OpenJDK environments, and operate services on **Amazon EC2**.
- Configure **Security Group** firewall rules to enforce 100% secure inter-service communication between EC2 Backends and RDS Databases.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Provision RDS MySQL Database:** Create RDS MySQL instances in private subnets, configuring DB Subnet Groups and initializing `ecommerce_db` schemas. | 01/06/2026 | 01/06/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html> |
| 2 (Tue) | **Product & Order Data Migration:** Execute SQL migration scripts to convert table schemas and import product catalogs, categories, and order samples onto RDS MySQL. | 02/06/2026 | 02/06/2026 | MySQL Workbench Migration |
| 3 (Wed) | **Package Spring Boot Application:** Update `application-prod.properties` to reference RDS JDBC Endpoints, building production `app.jar` artifacts. | 03/06/2026 | 03/06/2026 | Spring Boot Maven Build |
| 4 (Thu) | **Configure EC2 Environment & Deploy Backend:** SSH into EC2 instances, install Java OpenJDK 17, transfer `app.jar`, and run the application as a background systemd service. | 04/06/2026 | 04/06/2026 | EC2 Deployment Guide |
| 5 (Fri) | **Inter-Service Security Group Hardening:** Configure DB Security Group Inbound rules allowing port 3306 exclusively from EC2 Security Group IDs, verifying REST API responses. | 05/06/2026 | 05/06/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html> |

### Week 7 Achievements:
* **Successful Cloud Database Migration:** Migrated all product catalog and order records seamlessly from local environments to production RDS MySQL instances.
* **Spring Boot Backend Execution on EC2:** Deployed and operated Spring Boot APIs reliably on Amazon EC2 as persistent background services.
* **Inter-Service Security Hardening:** Established direct Security Group references between EC2 and RDS, isolating database instances completely from public Internet exposure.
