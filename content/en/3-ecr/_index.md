---
title: "Chapter 3: Create ECR repositories and push images"
weight: 3
---

## Create repository in Amazon ECR

### Steps to follow

1. Access AWS Management Console.
2. Navigate to **ECR (Elastic Container Registry)**.

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/find-ecr.png)

3. Create 2 repositories:
   - `frontend-app`
   ![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/create-ecr-repo.png)
   - `backend-app`
   ![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/create-ecr-repo-be.png)

> **Tip**: Name repositories clearly for easy management.

## Build and push images to ECR

### Using GitHub Actions

#### Create file `.github/workflows/deploy.yml`

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

### Set up secrets on GitHub

To use secrets in GitHub Actions, you need to set up secrets in your repository.

#### Steps to follow

1. Access your repository on GitHub.
2. Navigate to **Settings** > **Secrets and variables** > **Actions**.

![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/setup-secret-github.png)

3. Click **New repository secret** to add new secrets.
4. Add the necessary secrets:
   - `AWS_REGION`: Your AWS region (e.g., `ap-southeast-1`).
   - `AWS_ACCOUNT_ID`: Your AWS account ID.
   - `AWS_ACCESS_KEY_ID`: Access key ID of IAM user or role.
   - `AWS_SECRET_ACCESS_KEY`: Secret access key of IAM user or role.

   ![Elastic Container Registry](/FCJ-LeDuy-Workshop/images/secret-env.png)

> **Warning**: Make sure these secrets are secure and not shared publicly.

#### Using secrets in workflow

In the workflow file `.github/workflows/deploy.yml`, you can access secrets using the syntax `secrets.<SECRET_NAME>`.

Example:

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


### Check results

- Access AWS Management Console to verify images have been pushed to repositories.
- Ensure images have `latest` tag and correct format.
