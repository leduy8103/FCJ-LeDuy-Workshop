---
title: "📦 Chương 3: Tạo ECR repositories và đẩy image"
weight: 3
---

## Tạo repository trong Amazon ECR

### Các bước thực hiện

1. Truy cập AWS Management Console.
2. Điều hướng đến **ECR (Elastic Container Registry)**.

![Elastic Container Registry](/images/find-ecr.png)

3. Tạo 2 repository:
   - `frontend-app`
   ![Elastic Container Registry](/images/create-ecr-repo.png)
   - `backend-app`
   ![Elastic Container Registry](/images/create-ecr-repo-be.png)

> **Tip**: Đặt tên repository rõ ràng để dễ quản lý.

## Build và push image lên ECR

### Sử dụng GitHub Actions

#### Tạo file `.github/workflows/deploy.yml`

```yaml
name: Build and Push to ECR

on:
  push:
    branches:
      - main

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    env:
      AWS_REGION: ${{ secrets.AWS_REGION }}
      AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1
        with:
          aws-region: ${{ env.AWS_REGION }}

      - name: Build frontend image
        run: |
          docker build -t frontend-app ./frontend-app
          docker tag frontend-app:latest ${{ env.AWS_ACCOUNT_ID }}.dkr.ecr.${{ env.AWS_REGION }}.amazonaws.com/frontend-app:latest

      - name: Push frontend image
        run: |
          docker push ${{ env.AWS_ACCOUNT_ID }}.dkr.ecr.${{ env.AWS_REGION }}.amazonaws.com/frontend-app:latest

      - name: Build backend image
        run: |
          docker build -t backend-app ./backend-app
          docker tag backend-app:latest ${{ env.AWS_ACCOUNT_ID }}.dkr.ecr.${{ env.AWS_REGION }}.amazonaws.com/backend-app:latest

      - name: Push backend image
        run: |
          docker push ${{ env.AWS_ACCOUNT_ID }}.dkr.ecr.${{ env.AWS_REGION }}.amazonaws.com/backend-app:latest

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          role-to-assume: arn:aws:iam::${{ env.AWS_ACCOUNT_ID }}:role/GitHubActionsRole
          aws-region: ${{ env.AWS_REGION }}
```

### Thiết lập secrets trên GitHub

Để sử dụng secrets trong GitHub Actions, bạn cần thiết lập các secrets trong repository của mình.

#### Các bước thực hiện

1. Truy cập repository của bạn trên GitHub.
2. Điều hướng đến **Settings** > **Secrets and variables** > **Actions**.

![Elastic Container Registry](/images/setup-secret-github.png)

3. Nhấn **New repository secret** để thêm secrets mới.
4. Thêm các secrets cần thiết:
   - `AWS_REGION`: Vùng AWS của bạn (ví dụ: `ap-southeast-1`).
   - `AWS_ACCOUNT_ID`: ID tài khoản AWS của bạn.
   - `AWS_ACCESS_KEY_ID`: Access key ID của IAM user hoặc role.
   - `AWS_SECRET_ACCESS_KEY`: Secret access key của IAM user hoặc role.

   ![Elastic Container Registry](/images/secret-env.png)

> **Cảnh báo**: Đảm bảo rằng các secrets này được bảo mật và không chia sẻ công khai.

#### Sử dụng secrets trong workflow

Trong file workflow `.github/workflows/deploy.yml`, bạn có thể truy cập các secrets bằng cú pháp `secrets.<SECRET_NAME>`.

Ví dụ:

```yaml
env:
  AWS_REGION: ${{ secrets.AWS_REGION }}
  AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}

steps:
  - name: Configure AWS credentials
    uses: aws-actions/configure-aws-credentials@v2
    with:
      aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
      aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      aws-region: ${{ secrets.AWS_REGION }}
```

### Sử dụng AWS CLI

#### Đăng nhập vào ECR

```bash
aws ecr get-login-password --region <AWS_REGION> | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com
```

#### Build và push image

```bash
# Frontend
docker build -t frontend-app ./frontend-app

docker tag frontend-app:latest <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/frontend-app:latest

docker push <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/frontend-app:latest

# Backend
docker build -t backend-app ./backend-app

docker tag backend-app:latest <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/backend-app:latest

docker push <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/backend-app:latest
```

### Kiểm tra kết quả

- Truy cập AWS Management Console để kiểm tra các image đã được đẩy lên repository.
- Đảm bảo các image có tag `latest` và đúng định dạng.
