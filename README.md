# Resilient Application Infrastructure Engineering on AWS

A multi-AZ AWS application environment provisioned with Terraform, operated with observability and failure testing, and delivered through controlled CI/CD.

## Overview

This project designs and implements a resilient AWS application infrastructure that can be reproducibly deployed, securely administered, observed, failure-tested, scaled, and destroyed/rebuilt for cost control.

The system is developed across three engineering layers:

1. **Infrastructure / IaC** — networking, compute, database, security, and Terraform
2. **Operations & Observability** — monitoring, logging, alarms, scaling, failure testing, and recovery
3. **Controlled Delivery** — GitHub Actions (CI/CD), Terraform validation/planning, OIDC authentication, approval gates, and reproducible deployment

## Architecture

```mermaid
flowchart TD
    Internet --> ALB[Application Load Balancer]

    subgraph AWS["AWS VPC — 2 Availability Zones"]
        ALB --> ASG[EC2 Auto Scaling Group]
        ASG --> EC2A[EC2 — AZ A]
        ASG --> EC2B[EC2 — AZ B]
        EC2A --> RDS[(Amazon RDS)]
        EC2B --> RDS
    end

    SSM[AWS Systems Manager] -. administration .-> ASG
    CW[Amazon CloudWatch] -. metrics / logs / alarms .-> ASG
    CW -. monitoring .-> ALB
    CW -. monitoring .-> RDS
```

- VPC spanning two Availability Zones
- Public subnets for the Application Load Balancer and NAT
- Private application subnets for EC2
- Private database subnets for Amazon RDS
- EC2 Auto Scaling Group across multiple Availability Zones
- Security-group-based tier isolation
- AWS Systems Manager Session Manager for administration
- Amazon CloudWatch metrics, logs, alarms, and operational testing
- Terraform infrastructure provisioning
- Amazon S3-backed remote Terraform state
- GitHub Actions CI/CD with AWS OIDC authentication
- Controlled infrastructure deployment with manual approval
- Cost-controlled destroy/rebuild workflow

## Engineering Goals

The project is designed to validate:

- Designing a segmented multi-tier AWS architecture
- Provisioning infrastructure reproducibly with Terraform
- Isolating application and database workloads from direct public access
- Maintaining and scaling compute capacity automatically
- Observing infrastructure and application behavior
- Deliberately introducing failures and verifying recovery
- Reasoning about resilience, security, cost, and production tradeoffs
- Delivering infrastructure changes through a controlled CI/CD process
- Destroying and reproducibly rebuilding the environment
