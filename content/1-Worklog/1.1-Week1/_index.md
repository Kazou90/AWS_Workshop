---
title: "Week 1 Worklog"
date: "2026-04-20"
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:
* Understand and practice core AWS IAM concepts (Users, Groups, Roles, Policies, Switch Role, MFA).
* Learn to deploy and manage Amazon EC2 (Launch instances, Security Groups, Snapshots, AMIs, Apache web server).
* Implement a high-availability and auto-scaling architecture using Elastic Load Balancer (ELB) and Auto Scaling Group (ASG).
* Understand and apply IAM Roles as a secure method for EC2 to access other AWS services (like S3) without hardcoded access keys.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference/Material |
| --- | --- | --- | --- | --- |
| 1 (Mon) | **Lab 02 – Introduction to AWS IAM:** Practice creating/managing IAM Users, Groups, Roles, Policies; configure MFA multi-factor authentication and perform Switch Role across accounts. | 20/04/2026 | 20/04/2026 | <https://000002.awsstudygroup.com/> |
| 2 (Tue) | **Learn & Deploy Amazon EC2:** Launch EC2 instances (Windows & Linux); install Apache web server; practice creating Snapshots & AMIs for server cloning. | 21/04/2026 | 21/04/2026 | <https://docs.aws.amazon.com/ec2/> |
| 3 (Wed) | **Security Group Lab:** Configure Security Groups (stateful firewall) to allow and block network traffic (HTTP, HTTPS, SSH) to an EC2 instance based on Least Privilege rules. | 22/04/2026 | 22/04/2026 | <https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html> |
| 4 (Thu) | **Deploy Auto-Scaling System:** Configure Launch Template, Auto Scaling Group (with CPU-based dynamic target policy), and Application Load Balancer (ALB). | 23/04/2026 | 23/04/2026 | <https://docs.aws.amazon.com/autoscaling/> |
| 5 (Fri) | **Lab: Assign IAM Role to EC2 for S3 Access:** Create an S3 read-only IAM Role, assign it to EC2; use AWS CLI to test secure access without hardcoded access keys. | 24/04/2026 | 24/04/2026 | <https://000002.awsstudygroup.com/4-switch-roles/> |

### Week 1 Achievements:
* **IAM Security & Governance:** Mastered basic concepts and practical operations of AWS IAM, understanding the key differences between Users, Groups, Roles, and Policies. Applied Least Privilege access and MFA.
* **Amazon EC2 Operation:** Successfully deployed and managed Amazon EC2 virtual machines across AMIs (Amazon Linux 2023, Windows Server), installed Apache web servers, and performed system backup/cloning using Snapshots and AMIs.
* **Network Infrastructure Security:** Understood and proficiently configured Security Groups to secure instances, applying stateful firewall rules to block unauthorized port scans.
* **High Availability & Auto Scaling:** Deployed a complete high-availability (HA) and auto-scaling architecture by combining Launch Templates, Application Load Balancers (ALB) for traffic distribution, and Auto Scaling Groups (ASG) to dynamically scale instances based on CPU utilization.
* **Security Best Practices:** Applied the security best practice of attaching IAM Service Roles directly to EC2 instances for S3 interactions, eliminating long-term hardcoded access credentials.
