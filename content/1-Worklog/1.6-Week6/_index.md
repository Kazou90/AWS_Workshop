---
title: "Week 6 Worklog"
date: "2026-05-25"
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:
* Containerize Microservice applications (Spring Boot/Node.js Backend and React Frontend) using **Docker**.
* Manage enterprise container image registries securely on **Amazon ECR (Elastic Container Registry)**.
* Deploy and orchestrate serverless containers using **Amazon ECS (Elastic Container Service)** with **AWS Fargate** launch type.
* Configure container load balancing via ALB and implement automated task scaling using **ECS Service Auto Scaling**.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Application Containerization with Docker:** Write multi-stage Dockerfiles for React frontend (Nginx web server) and Backend services. Validate multi-container orchestration locally using Docker Compose. | 25/05/2026 | 25/05/2026 | <https://docs.docker.com/> |
| 2 (Tue) | **Lab 14 – Amazon ECR Container Registry:** Create private ECR Repositories, authenticate AWS CLI Docker Credential Helper, tag image versions, and push built Docker images to ECR. | 26/05/2026 | 26/05/2026 | <https://000014.awsstudygroup.com/> |
| 3 (Wed) | **Amazon ECS Task Definitions Setup:** Author ECS Task Definitions (CPU/Memory allocations, container port mappings, environment variables, and log aggregation via `awslogs` driver to CloudWatch). | 27/05/2026 | 27/05/2026 | <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html> |
| 4 (Thu) | **Deploy ECS Services on AWS Fargate:** Launch ECS Clusters, create ECS Services on serverless Fargate launch type, and integrate containers with ALB Target Groups. | 28/05/2026 | 28/05/2026 | <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html> |
| 5 (Fri) | **ECS Service Auto Scaling Configuration:** Configure target-tracking auto-scaling policies to scale ECS Tasks automatically based on real-time CPU and Memory utilization metrics. | 29/05/2026 | 29/05/2026 | <https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html> |

### Week 6 Achievements:
* **Standardized Container Packaging:** Successfully containerized applications into lightweight Docker images, ensuring complete environment consistency across development and production.
* **Secured ECR Image Registry:** Centralized container image management on Amazon ECR with automated image vulnerability scanning enabled.
* **Serverless Container Orchestration:** Successfully operated application containers on AWS Fargate, eliminating underlying EC2 server management while achieving seamless ALB load balancing and auto-scaling.
