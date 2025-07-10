---
title: "Workshop: Triển khai ứng dụng với ECS Fargate và CI/CD"
weight: 1
---

# Workshop: Triển khai ứng dụng với ECS Fargate và CI/CD

Chào mừng bạn đến với workshop về triển khai ứng dụng multi-container lên AWS ECS Fargate với quy trình CI/CD tự động. Trong workshop này, bạn sẽ học cách:

- Dockerize ứng dụng frontend và backend
- Sử dụng Amazon ECR để lưu trữ container images
- Triển khai containers lên ECS Fargate
- Thiết lập monitoring với CloudWatch
- Tự động hóa toàn bộ quy trình với GitHub Actions

## Mục tiêu của workshop

Sau khi hoàn thành workshop này, bạn sẽ có khả năng:
- Hiểu được quy trình CI/CD cơ bản
- Thành thạo việc sử dụng Docker cho containerization
- Triển khai ứng dụng lên AWS ECS Fargate
- Thiết lập monitoring và logging
- Tự động hóa deployment với GitHub Actions

## Kiến thức yêu cầu

- Hiểu biết cơ bản về AWS
- Kinh nghiệm với Docker
- Hiểu về Git và GitHub
- Kiến thức cơ bản về CI/CD

## Thời gian dự kiến

Workshop này sẽ mất khoảng 2-3 giờ để hoàn thành.

## Kiến trúc hệ thống

![Workshop Architecture](images/workshop-architecture.png)

Hệ thống bao gồm:
- **Frontend**: React application
- **Backend**: Node.js/Flask application  
- **ECR**: Container registry
- **ECS Fargate**: Container orchestration
- **CloudWatch**: Monitoring và logging
- **GitHub Actions**: CI/CD pipeline
