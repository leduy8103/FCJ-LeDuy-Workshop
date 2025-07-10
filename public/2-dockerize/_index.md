---
title: "🔧 Chương 2: Chuẩn bị dự án & Docker hóa ứng dụng"
weight: 2
---

## Giả định ban đầu

- Bạn đã có sẵn mã nguồn cho ứng dụng **frontend** (React) và **backend** (Node.js hoặc Flask) trong cùng một repository.
- Clone repository từ GitHub:

```bash
# Clone repository chứa cả frontend và backend
git clone https://github.com/leduy8103/ecs-cicd-workshop.git
```

## Kiểm tra Dockerfile

### Dockerfile cho Frontend (React)

```dockerfile
# Sử dụng image Node.js
FROM node:16

# Thiết lập thư mục làm việc
WORKDIR /app

# Copy package.json và cài đặt dependencies
COPY package.json ./
RUN npm install

# Copy toàn bộ mã nguồn
COPY . .

# Build ứng dụng
RUN npm run build

# Sử dụng image Nginx để phục vụ ứng dụng
FROM nginx:alpine
COPY --from=0 /app/build /usr/share/nginx/html

# Expose port
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Dockerfile cho Backend (Node.js)

```dockerfile
# Sử dụng image Node.js
FROM node:16

# Thiết lập thư mục làm việc
WORKDIR /app

# Copy package.json và cài đặt dependencies
COPY package.json ./
RUN npm install

# Copy toàn bộ mã nguồn
COPY . .

# Expose port
EXPOSE 3000
CMD ["node", "index.js"]
```

> **Tip**: Đảm bảo Dockerfile đã được kiểm tra và hoạt động đúng trước khi tiếp tục.
