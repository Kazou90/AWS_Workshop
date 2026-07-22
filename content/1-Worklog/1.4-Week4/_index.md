---
title: "Week 4 Worklog"
date: "2026-05-11"
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:
* Provision, configure, and secure relational databases using **Amazon RDS** (PostgreSQL/MySQL) in Multi-AZ deployment mode.
* Understand NoSQL architecture and master **Amazon DynamoDB** (Partition Key, Sort Key, Global Secondary Indexes - GSI, DynamoDB Streams).
* Establish secure database connectivity from backend applications (Spring Boot / Node.js) inside private VPC subnets.
* Practice database backup and point-in-time recovery (PITR) mechanisms on AWS.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Lab 08 – Amazon RDS Multi-AZ Deployment:** Launch Multi-AZ RDS PostgreSQL/MySQL clusters, configure private DB Subnet Groups, Parameter Groups, and restrict access via DB Security Groups. | 11/05/2026 | 11/05/2026 | <https://000008.awsstudygroup.com/> |
| 2 (Tue) | **Database Backup & Recovery:** Perform manual DB Snapshots, test Point-in-Time Recovery (PITR), and execute schema migration scripts using `psql` / `mysql-client`. | 12/05/2026 | 12/05/2026 | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_CommonTasks.BackupRestore.html> |
| 3 (Wed) | **Lab 09 – Data Modeling with Amazon DynamoDB:** Create NoSQL tables with Partition Keys and Sort Keys. Configure Global Secondary Indexes (GSIs) to accelerate multi-attribute queries. | 13/05/2026 | 13/05/2026 | <https://000009.awsstudygroup.com/> |
| 4 (Thu) | **Advanced DynamoDB Features (TTL & Streams):** Configure Time To Live (TTL) for automatic item expiration and enable DynamoDB Streams to track real-time data modifications. | 14/05/2026 | 14/05/2026 | <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html> |
| 5 (Fri) | **Backend Database Integration:** Configure Spring Boot (Spring Data JPA) and Node.js (AWS SDK v3) applications to connect securely to private RDS and DynamoDB endpoints. | 15/05/2026 | 15/05/2026 | <https://aws.amazon.com/developer/language/java/> |

### Week 4 Achievements:
* **Relational Database Administration:** Successfully deployed Multi-AZ RDS clusters with automated failover capabilities, ensuring 99.99% data availability.
* **NoSQL Data Modeling:** Designed efficient DynamoDB schemas with GSIs, optimizing read/write capacity and leveraging TTL to automate data cleanup.
* **Secure Database Connectivity:** Hardened database network access by hosting instances exclusively in private subnets, restricting connections to authorized backend application security groups.
