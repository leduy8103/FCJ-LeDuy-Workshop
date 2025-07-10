---
title: "🚀 Chương 4: Deploy ECS Fargate"
weight: 4
---

## 🎯 Mục tiêu chương

Trong chương này, bạn sẽ học cách triển khai ứng dụng frontend và backend đã được Docker hóa và đẩy lên ECR, bằng cách sử dụng **Amazon ECS Fargate** – một dịch vụ serverless cho container trên AWS.

---

## 📦 Bước 1: Tạo ECS Cluster

1. Truy cập AWS Console → tìm **ECS**
![Elastic Container Registry](/images/find-ecs.png)
2. Chọn **Clusters** → nhấn **Create Cluster**
3. Chọn loại cluster: `Networking only (Fargate)`
4. Đặt tên: `workshop-cluster-81`
5. Nhấn **Create**

![Elastic Container Registry](/images/create-ecs-cluster.png)
> ✅ Cluster sẽ không có EC2 instance vì bạn đang dùng Fargate (serverless).

---

## 🧱 Bước 2: Tạo Task Definition

Bạn cần tạo **2 task definitions** – một cho frontend và một cho backend.

### ✨ 2.1. Frontend Task

1. ECS → **Task Definitions** → **Create new Task Definition**

![Elastic Container Registry](/images/choose-create-task.png)

2. Launch type: `Fargate`
3. Task name: `frontend-task`
4. CPU: `0.5 vCPU`, Memory: `1 GB`

![Elastic Container Registry](/images/config-task-fe-1.png)

5. Nhấn **Add container**:
   - Container name: `frontend`
   - Image URI:  
     ```
     <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/frontend-app:latest
     ```
   - Port mappings: `80`

   ![Elastic Container Registry](/images/config-task-fe-2.png)

6. Nhấn **Create**

### ✨ 2.2. Backend Task

Lặp lại các bước trên với:

- Task name: `backend-task`
- Container name: `backend`
- Image URI: <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/backend-app:latest
- Port mappings: `3000`


> ✅ Tạo thành công backend-task và frontend-task.
![Elastic Container Registry](/images/created-task-fe-be.png)

---

## 🌐 Bước 3: Tạo VPC và Networking

Nếu bạn chưa có VPC phù hợp, hãy tạo VPC theo các bước sau:

1. Truy cập **VPC** → **Create VPC**
2. Tên: `workshop-vpc`, CIDR block: `10.0.0.0/16`
3. Tạo 2 subnet:
 - Subnet 1: `10.0.1.0/24` – Public
 - Subnet 2: `10.0.2.0/24` – Private
4. Tạo **Internet Gateway** → gắn vào `workshop-vpc`
5. Cập nhật **Route Table**:
 - Public Route Table: trỏ 0.0.0.0/0 đến Internet Gateway
 - Gán route table này cho Subnet 1

> ⚠️ Chỉ frontend cần truy cập internet (public subnet). Backend có thể đặt trong private subnet.

![Elastic Container Registry](/images/vpc.png)

---

## 🔐 Bước 4: Tạo Security Group

1. Vào **EC2 → Security Groups** → **Create Security Group**
2. Tên: `frontend-sg`, mô tả: `Allow HTTP`
3. Inbound rules:
 - Type: HTTP
 - Port: 80
 - Source: 0.0.0.0/0
4. Outbound: giữ mặc định (Allow all)

![Elastic Container Registry](/images/frontend-sg.png)

Tạo thêm `backend-sg`:
- Inbound:
- Type: Custom TCP, Port 3000
- Source: `frontend-sg` (chọn từ group list)
- Outbound: mặc định

![Elastic Container Registry](/images/backend-sg.png)

---

## 🛠 Bước 5: Tạo ECS Service (chạy container)

### 5.1. Tạo Frontend Service

1. Vào ECS → `workshop-cluster`
2. Chọn tab **Services** → **Create**
3. Điền thông tin:
   - Launch type: `Fargate`
   - Task definition: `frontend-task`
   - Service name: `frontend-service`
   - Số lượng task: `1`
   - Cluster VPC: chọn `workshop-vpc`
   - Subnet: chọn subnet public
   - Security Group: chọn `frontend-sg`
   - Tích **Enable public IP**
4. Nhấn **Create Service**

![Elastic Container Registry](/images/fe-service-detail.png)

![Elastic Container Registry](/images/fe-service-networking.png)

![Elastic Container Registry](/images/create-fe-service-success.png)


### 5.2. Tạo Backend Service

Lặp lại các bước trên, với:

- Task: `backend-task`
- Subnet: chọn subnet private (hoặc public nếu cần demo đơn giản)
- SG: `backend-sg`
- Không cần public IP nếu không gọi trực tiếp từ trình duyệt

![Elastic Container Registry](/images/be-service-detail.png)

![Elastic Container Registry](/images/be-service-networking.png)

![Elastic Container Registry](/images/create-be-service-success.png)

---

## 🔍 Kiểm tra triển khai

Sau khi tạo xong:

- ECS sẽ chạy 2 task trong 2 service.
- Vào **ECS > Clusters > Tasks** để kiểm tra trạng thái RUNNING

![Elastic Container Registry](/images/review-fe-service.png)

> Nếu không thấy, kiểm tra lại Security Group và ECR image đã push đúng chưa.

---
