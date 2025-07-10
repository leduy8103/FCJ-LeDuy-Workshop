---
title: "Chapter 6: Automate everything with GitHub Actions"
weight: 6
---

## Objectives

After this chapter, you will set up a CI/CD process that automates everything when pushing code to GitHub:

1. Build images for frontend and backend
2. Push images to Amazon ECR
3. Deploy containers to ECS Fargate
4. Log and check status
5. (Optional) Rollback if errors occur

---

## 1. Set up secrets in GitHub

Go to **GitHub repo → Settings → Secrets and variables → Actions**, add variables:

| Name | Value |
|------|-------|
| `AWS_ACCESS_KEY_ID` | Access Key of IAM user/role |
| `AWS_SECRET_ACCESS_KEY` | Corresponding secret key |
| `AWS_REGION` | `ap-southeast-1` |
| `AWS_ACCOUNT_ID` | AWS account ID |

> 🎯 IAM user should have permissions: `AmazonEC2ContainerRegistryFullAccess`, `AmazonECS_FullAccess`, `CloudWatchLogsFullAccess`

---

## 2. Create file `.github/workflows/ci-cd.yml`

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

env:
  AWS_REGION: ${{ secrets.AWS_REGION }}
  AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source code
        uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Login to Amazon ECR
        uses: aws-actions/amazon-ecr-login@v1

      - name: Build and Push Frontend Image
        run: |
          docker build -t frontend-app ./frontend
          docker tag frontend-app:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/frontend-app:latest
          docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/frontend-app:latest

      - name: Build and Push Backend Image
        run: |
          docker build -t backend-app ./backend
          docker tag backend-app:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/backend-app:latest
          docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/backend-app:latest

      - name: Deploy to ECS
        run: |
          aws ecs update-service --cluster workshop-cluster --service frontend-service --force-new-deployment
          aws ecs update-service --cluster workshop-cluster --service backend-service --force-new-deployment

      - name: Check ECS Service Status
        run: |
          aws ecs describe-services --cluster workshop-cluster \ --services frontend-service backend-service

```

---

## 3. Logging and status checking

### Check container logs:
- Go to CloudWatch → Log groups → View log stream

### Check ECS status:
```bash
aws ecs describe-services   --cluster workshop-cluster   --services frontend-service backend-service
```

---

## 4. Rollback (manual)

When deployment fails, you can rollback by:

- Revert code → Push again
- Or deploy old image from ECR (if tagged and saved previously)

> 📦 Tip: Use tags `:v1`, `:v2`, `:rollback`,... for easy rollback image control

---
