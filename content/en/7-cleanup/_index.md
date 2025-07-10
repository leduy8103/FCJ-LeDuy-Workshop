---
title: "Chapter 7: Cleanup & Summary"
weight: 7
---

## Cleanup

### Delete ECS service and task

#### Steps to follow

1. Access AWS Management Console.
2. Navigate to **ECS (Elastic Container Service)**.
3. Delete services:
   - `frontend-service`
   - `backend-service`

4. Delete cluster if no longer used.

### Delete ECR images

#### Steps to follow

1. Access AWS Management Console.
2. Navigate to **ECR (Elastic Container Registry)**.
3. Delete images in repositories:
   - `frontend-app`
   - `backend-app`

### Delete logs on CloudWatch

#### Steps to follow

1. Access AWS Management Console.
2. Navigate to **CloudWatch > Logs**.
3. Delete log groups:
   - `/aws/ecs/workshop-cluster/frontend-app`
   - `/aws/ecs/workshop-cluster/backend-app`

> **Warning**: Make sure you don't need the data before deleting.

## Summary

### Knowledge learned

- Basic CI/CD process.
- Dockerizing frontend and backend applications.
- Using AWS services like ECR, ECS Fargate, and CloudWatch.
- Automating the entire process with GitHub Actions.

### Next steps

- Apply knowledge to real projects.
- Learn more about other AWS services like Lambda, API Gateway.
- Improve DevOps skills by deploying more complex systems.

> **Tip**: Always check AWS resources to avoid unnecessary costs.
