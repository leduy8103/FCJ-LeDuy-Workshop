---
title: "Chapter 4: Deploy ECS Fargate"
weight: 4
---

## Chapter Objectives

In this chapter, you will learn how to deploy dockerized frontend and backend applications pushed to ECR using **Amazon ECS Fargate** – a serverless container service on AWS.

---

## Step 1: Create ECS Cluster

1. Access AWS Console → find **ECS**
![Elastic Container Registry](/images/find-ecs.png)
2. Select **Clusters** → click **Create Cluster**
3. Choose cluster type: `Networking only (Fargate)`
4. Name: `workshop-cluster-81`
5. Click **Create**

![Elastic Container Registry](/images/create-ecs-cluster.png)
> ✅ The cluster will have no EC2 instances because you're using Fargate (serverless).

---

## Step 2: Create Task Definition

You need to create **2 task definitions** – one for frontend and one for backend.

### 2.1. Frontend Task

1. ECS → **Task Definitions** → **Create new Task Definition**

![Elastic Container Registry](/images/choose-create-task.png)

2. Launch type: `Fargate`
3. Task name: `frontend-task`
4. CPU: `0.5 vCPU`, Memory: `1 GB`

![Elastic Container Registry](/images/config-task-fe-1.png)

5. Click **Add container**:
   - Container name: `frontend`
   - Image URI:  
     ```
     <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/frontend-app:latest
     ```
   - Port mappings: `80`

   ![Elastic Container Registry](/images/config-task-fe-2.png)

6. Click **Create**

### 2.2. Backend Task

Repeat the above steps with:

- Task name: `backend-task`
- Container name: `backend`
- Image URI: <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com/backend-app:latest
- Port mappings: `3000`

> ✅ Successfully created backend-task and frontend-task.
![Elastic Container Registry](/images/created-task-fe-be.png)

---

## Step 3: Create VPC and Networking

If you don't have a suitable VPC, create one following these steps:

1. Access **VPC** → **Create VPC**
2. Name: `workshop-vpc`, CIDR block: `10.0.0.0/16`
3. Create 2 subnets:
 - Subnet 1: `10.0.1.0/24` – Public
 - Subnet 2: `10.0.2.0/24` – Private
4. Create **Internet Gateway** → attach to `workshop-vpc`
5. Update **Route Table**:
 - Public Route Table: route 0.0.0.0/0 to Internet Gateway
 - Assign this route table to Subnet 1

> ⚠️ Only frontend needs internet access (public subnet). Backend can be placed in private subnet.

![Elastic Container Registry](/images/vpc.png)

---

## Step 4: Create Security Group

1. Go to **EC2 → Security Groups** → **Create Security Group**
2. Name: `frontend-sg`, description: `Allow HTTP`
3. Inbound rules:
 - Type: HTTP
 - Port: 80
 - Source: 0.0.0.0/0
4. Outbound: keep default (Allow all)

![Elastic Container Registry](/images/frontend-sg.png)

Create additional `backend-sg`:
- Inbound:
- Type: Custom TCP, Port 3000
- Source: `frontend-sg` (select from group list)
- Outbound: default

![Elastic Container Registry](/images/backend-sg.png)

---

## Step 5: Create ECS Service (run containers)

### 5.1. Create Frontend Service

1. Go to ECS → `workshop-cluster`
2. Select **Services** tab → **Create**
3. Fill information:
   - Launch type: `Fargate`
   - Task definition: `frontend-task`
   - Service name: `frontend-service`
   - Number of tasks: `1`
   - Cluster VPC: select `workshop-vpc`
   - Subnet: select public subnet
   - Security Group: select `frontend-sg`
   - Check **Enable public IP**
4. Click **Create Service**

![Elastic Container Registry](/images/fe-service-detail.png)

![Elastic Container Registry](/images/fe-service-networking.png)

![Elastic Container Registry](/images/create-fe-service-success.png)

### 5.2. Create Backend Service

Repeat the above steps with:

- Task: `backend-task`
- Subnet: select private subnet (or public if needed for simple demo)
- SG: `backend-sg`
- No public IP needed if not calling directly from browser

![Elastic Container Registry](/images/be-service-detail.png)

![Elastic Container Registry](/images/be-service-networking.png)

![Elastic Container Registry](/images/create-be-service-success.png)

---

## Check deployment

After creation:

- ECS will run 2 tasks in 2 services.
- Go to **ECS > Clusters > Tasks** to check RUNNING status

![Elastic Container Registry](/images/review-fe-service.png)

> If not visible, check Security Group and ECR image push again.

---
