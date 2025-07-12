---
title: "Chapter 1: Overview Introduction"
weight: 1
---

## Workshop Goals

In this workshop, you will learn how to deploy a multi-container application (frontend + backend) to AWS ECS Fargate. Main objectives:

- Understand the basic CI/CD process.
- Dockerize frontend and backend applications.
- Use AWS services like ECR, ECS Fargate, and CloudWatch.

### Key Steps
1. Learn about CI/CD and containerization.
2. Dockerize frontend and backend applications.
3. Push container images to Amazon ECR.
4. Deploy containers to ECS Fargate.
5. Automate the process with GitHub Actions.

## Core DevOps Knowledge

### CI/CD
CI/CD (Continuous Integration/Continuous Deployment) is the process of automating source code integration and deployment. It helps minimize errors and accelerate software development.

- **Continuous Integration**: Regularly integrate source code to detect errors early.
- **Continuous Deployment**: Automatically deploy source code after successful testing.
- **Example**: Every time you push to the `main` branch, GitHub Actions will automatically perform steps: build image, push to ECR, and deploy to ECS Fargate.

### Containerization
Containerization is the technique of packaging applications and their dependencies into a container. Docker is the most popular tool to accomplish this.

- **Benefits**: Easy deployment, consistency across environments.
- **Tools**: Docker, Kubernetes.

### ECS Fargate
ECS Fargate is an AWS service that helps run containers without managing servers.

- **Benefits**: No need to manage infrastructure.
- **Application**: Run containerized applications.

## Technologies Used

- **GitHub Actions**: Automate CI/CD processes. This workshop uses GitHub Actions instead of AWS CodePipeline because it's more accessible to beginners.
- **Docker**: Package applications.
- **Amazon ECR**: Store container images.
- **Amazon ECS Fargate**: Run containers.
- **CloudWatch**: Monitor logs and system status.

## Overall Architecture

Below is the overall system architecture diagram:

- **Frontend**: React app.
- **Backend**: Node.js or Flask app.
- **ECR**: Store container images.
- **ECS Fargate**: Run containers.
- **Application Load Balancer**: Distribute traffic.
- **CloudWatch**: Log and monitor.

![Overall Architecture](/FCJ-LeDuy-Workshop/images/architecture.png)

> **Tip**: Make sure you have an AWS account and understand the basics of AWS services before starting.
