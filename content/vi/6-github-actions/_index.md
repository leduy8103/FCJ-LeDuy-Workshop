---
title: "Chương 6: Tự động hóa toàn bộ với GitHub Actions"
weight: 6
---

## Mục tiêu

Sau chương này, bạn sẽ thiết lập quy trình CI/CD tự động hóa toàn bộ khi push code lên GitHub:

1. Build image cho frontend và backend
2. Push image lên Amazon ECR
3. Triển khai container lên ECS Fargate
4. Ghi log và kiểm tra trạng thái
5. (Tuỳ chọn) Rollback nếu có lỗi

---

## 1. Thiết lập bí mật trong GitHub

Vào **GitHub repo → Settings → Secrets and variables → Actions**, thêm các biến:

| Name | Value |
|------|-------|
| `AWS_ACCESS_KEY_ID` | Access Key của IAM user/role |
| `AWS_SECRET_ACCESS_KEY` | Secret key tương ứng |
| `AWS_REGION` | `ap-southeast-1` |
| `AWS_ACCOUNT_ID` | ID tài khoản AWS |

> 🎯 IAM user nên có quyền: `AmazonEC2ContainerRegistryFullAccess`, `AmazonECS_FullAccess`, `CloudWatchLogsFullAccess`

---

## 2. Tạo file `.github/workflows/ci-cd.yml`

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

## 3. Logging và kiểm tra trạng thái

### Kiểm tra log container:
- Vào CloudWatch → Log groups → Xem log stream

### Kiểm tra trạng thái ECS:
```bash
aws ecs describe-services   --cluster workshop-cluster   --services frontend-service backend-service
```

---

## 4. Rollback (thủ công)

Khi deploy lỗi, bạn có thể rollback bằng cách:

- Revert code → Push lại
- Hoặc deploy image cũ từ ECR (nếu đã được tag và lưu trước đó)

> 📦 Gợi ý: Sử dụng tag `:v1`, `:v2`, `:rollback`,... để dễ kiểm soát rollback image

---

