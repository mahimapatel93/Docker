# Docker Frontend-Backend Project - Step by Step Execution

This document explains the exact steps performed to run the frontend and backend Docker project with Nginx reverse proxy, based on your workflow. Ideal for GitHub documentation.

## 1. Install Prerequisites

Install Docker:

sudo yum update -y
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker

Install Git:

sudo yum install -y git

## 2. Clone Project Repository

git clone <REPO_URL>
cd 2nd10WeeksofCloudOps-main/

## 3. Project Folders

2nd10WeeksofCloudOps-main/
├── backend/
│   ├── Dockerfile
│   ├── index.js
│   ├── package.json
│   └── package-lock.json
├── client/
│   ├── Dockerfile
│   ├── proxy.conf
│   ├── package.json
│   ├── package-lock.json
│   └── src/
└── docker-compose.yaml (optional)

## 4. Docker Network Setup

Create a custom Docker network so frontend and backend can communicate:

docker network create app-network

## 5. Backend Steps

Folder: backend/

1. Build backend image:

docker build -t backend1 .

2. Remove old container if exists:

docker rm -f backend1 2>/dev/null

3. Run backend container:

docker run -d --name backend1 --network app-network -p 5000:5000 backend1

4. Verify backend:

docker ps

* Backend listens on port 5000
* Container name: backend1

## 6. Frontend Steps

Folder: client/

1. Update API URL in React app:

// src/config.js
const API_URL = "/api";

2. Dockerfile (Nginx + React):

# Stage 1: Build React
FROM node:18 AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
COPY proxy.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

3. proxy.conf (Nginx Reverse Proxy):

server {
    listen 80;
    server_name _;

    location ^~ /api/ {
        proxy_pass http://backend1:5000/;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}

4. Build frontend image:

docker build -t frontend1 .

5. Remove old container if exists:

docker rm -f frontend1 2>/dev/null

6. Run frontend container:

docker run -d --name frontend1 --network app-network -p 81:80 frontend1

7. Verify frontend:

docker ps

* Frontend accessible on port 81
* Nginx proxy forwards /api/... to backend1:5000

## 7. Testing

1. Open browser: http://<EC2-PUBLIC-IP>:81
2. React app should load successfully
3. API calls to /api/... are routed to backend container correctly

## 8. Useful Docker Commands

# Remove a container forcefully
docker rm -f <container_name> 2>/dev/null

# Build image
docker build -t <image_name> .

# Run container on a network
docker run -d --name <container_name> --network app-network -p <host_port>:<container_port> <image_name>

# Check running containers
docker ps

# View container logs
docker logs <container_name>

Result: Frontend and backend are running in Docker with internal communication via Docker network, and Nginx handles API reverse proxy. This workflow can be uploaded directly to GitHub as README.md.
