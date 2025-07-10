---
title: "Chapter 2: Project Preparation & Application Dockerization"
weight: 2
---

## Initial Assumptions

- You already have source code for **frontend** (React) and **backend** (Node.js or Flask) applications in the same repository.
- Clone repository from GitHub:

```bash
# Clone repository containing both frontend and backend
git clone https://github.com/leduy8103/ecs-cicd-workshop.git
```

## Check Dockerfile

### Dockerfile for Frontend (React)

```dockerfile
# Use Node.js image
FROM node:16

# Set working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json ./
RUN npm install

# Copy entire source code
COPY . .

# Build application
RUN npm run build

# Use Nginx image to serve the application
FROM nginx:alpine
COPY --from=0 /app/build /usr/share/nginx/html

# Expose port
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Dockerfile for Backend (Node.js)

```dockerfile
# Use Node.js image
FROM node:16

# Set working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json ./
RUN npm install

# Copy entire source code
COPY . .

# Expose port
EXPOSE 3000
CMD ["node", "index.js"]
```

> **Tip**: Make sure the Dockerfile has been tested and works properly before proceeding.
