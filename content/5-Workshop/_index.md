---
title: "Workshop"
weight: 5
chapter: false
pre: "<b>5. </b>"
---

# Technical Hands-On Workshop: Batch-Based Clickstream Analytics Platform

![Architecture](/images/architecture.png)

<p align="center"><em>Figure 5.0: End-to-End Architecture of the f5-SCAJ Clickstream Analytics Platform.</em></p>

#### Overview & Engineering Objectives

In this hands-on workshop, you will build a production-grade **Batch-Based Clickstream Analytics Platform** designed for an e-commerce laptop and computer accessories platform.

Key Engineering Principles:
- **Strict Separation of OLTP and Analytics**: Analytical queries do not degrade core e-commerce database operations.
- **Private Analytical Backend**: The Data Warehouse and R Shiny dashboard run inside a **Private Subnet** with no public IP and zero open SSH ports.
- **Zero-NAT Gateway Cost Optimization**: Uses **AWS S3 Gateway VPC Endpoint** and **SSM Interface VPC Endpoints** for private connectivity.
- **Automated Serverless Batch ETL**: Ingests raw clickstream JSON events into S3 and executes hourly batch transformations via **EventBridge + AWS Lambda ETL**.

#### Workshop Structure

1. **[5.1. Lab Objectives & Scope](5.1-objectives--scope/)** - Business requirements, learning goals, and design boundaries.
2. **[5.2. Architecture Walkthrough](5.2-architecture-walkthrough/)** - Detailed breakdown of all 20 AWS components and data movement.
3. **[5.3. Implementing Clickstream Ingestion](5.3-implementing-clickstream-ingestion/)** - Configure API Gateway, deploy Lambda Ingest, design S3 Raw Data Lake, and wire Next.js SDK.
4. **[5.4. Building the Private Analytics Layer](5.4-building-the-private-analytics-layer/)** - Provision VPC subnets, VPC Endpoints, PostgreSQL DWH on EC2 Private, Lambda ETL, and EventBridge hourly trigger.
5. **[5.5. Visualizing Analytics with Shiny Dashboards](5.5-visualizing-analytics-with-shiny-dashboards/)** - Package installation, R Shiny Server deployment, and secure local access via SSM Port Forwarding.
6. **[5.6. Summary & Clean-up](5.6-summary--clean-up/)** - Engineering insights, cost analysis, and step-by-step teardown guide.
7. **[5.7. Demo & Video Recording](5.7-demo/)** - Live website showcase and recorded demonstration video.
