**video link-** https://drive.google.com/drive/folders/1PTG5pkvtW6qDUqfMwOdmwO7SV-j5WHCA?usp=sharing


**DevOps003-AceCloud - Microservices with CI/CD**

A modernized microservices application demonstrating containerization, orchestration, automated deployment, and image size optimization using distroless images.
Live Demo: https://devops003.theacecloud.co
Table of Contents

Features
Prerequisites
Local Development
Production Deployment
Architecture
Static IP Implementation
Image Size Optimization with Distroless
CI/CD Pipeline
Documentation
License

**Features**

Containerized microservices: React frontend, Node.js backend, MongoDB
Docker Compose orchestration with static IP assignment
NGINX ingress with TLS termination for secure traffic
Automated CI/CD pipeline using GitHub Actions
Health monitoring endpoints for service reliability
Optimized Docker images using distroless for minimal size and enhanced security
Production-ready logging and error handling

**Prerequisites**

Docker: Version 20.10 or higher
Docker Compose: Version 2.0 or higher
Node.js: Version 16 or higher (for local development)
Certbot: For generating Let's Encrypt certificates in production
Git: For cloning the repository
Server Access: A cloud server with a public IP for production deployment
DNS Configuration: A domain with an A record pointing to the server IP

**Local Development**

Clone the Repository:
git clone https://github.com/bhavya-dabas/DevOps003-AceCloud.git
cd DevOps003-AceCloud


Set Up Environment Variables:

Copy the example environment file:cp .env.example .env


Update .env with appropriate values (e.g., MongoDB credentials, API keys).


Start Services:

Use the development compose file to build and run containers:docker-compose -f docker-compose.dev.yml up -d --build


The --build flag ensures images are built fresh.


**Access Services**:

Frontend: http://localhost:8080
Backend API: http://localhost:3000/api
MongoDB: mongodb://root:example@localhost:27017
Verify services are running:docker-compose ps




Stop Services:
docker-compose -f docker-compose.dev.yml down



 **Production Deployment**

**Set Up Server:**

Provision a cloud server (e.g., AWS EC2, DigitalOcean Droplet).
Install Docker, Docker Compose, and Certbot.


Configure DNS:

Create an A record for devops003.theacecloud.co pointing to your server’s public IP.


**Generate TLS Certificates:**

Use Certbot to obtain Let's Encrypt certificates:sudo certbot certonly --standalone -d devops003.theacecloud.co


Certificates are stored at /etc/letsencrypt/live/devops003.theacecloud.co.


**Set Up Environment Variables:**

Copy .env.example to .env on the server and configure production values.
Ensure sensitive values (e.g., MONGO_URI, JWT_SECRET) are secure.


**Deploy Services:**

Use the production compose file:docker-compose -f docker-compose.prod.yml up -d --build


Access the application at https://devops003.theacecloud.co.


Monitor Logs:

Check container logs for debugging:docker-compose logs backend
docker-compose logs frontend





**Architecture**
**Service Structure**
User → NGINX (TLS Termination, Port 443)
      ├─ Frontend (React, Port 80)
      └─ Backend (Node.js, Port 3000) → MongoDB (Port 27017)

**Network Configuration**

Docker Network: Custom bridge network (app_net) with subnet 172.20.0.0/16.
Communication: Services communicate internally via Docker network.
External Access: Only NGINX is exposed to the public (ports 80, 443).
Security: TLS termination at NGINX ensures encrypted traffic.

Static IP Implementation
The backend service uses a static IP (172.20.0.10) to ensure:

Predictable Service Discovery: Consistent IP for frontend-to-backend communication.
Stable Routing: Fixed IP simplifies NGINX proxy configuration.
Reliable Database Access: MongoDB connections remain stable.

Configured in docker-compose.yml:
networks:
  app_net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16

services:
  backend:
    networks:
      app_net:
        ipv4_address: 172.20.0.10

Image Size Optimization with Distroless
To reduce Docker image sizes and enhance security, the project uses distroless images for production:
Why Distroless?

Minimal Footprint: Distroless images contain only the application and its runtime dependencies, excluding shells, package managers, and unnecessary tools.
Security: Reduced attack surface by eliminating utilities that could be exploited.
Size Reduction:
Backend: Reduced from ~155MB (node:16-alpine) to ~60MB (gcr.io/distroless/nodejs:16).
Frontend: Uses nginx:alpine (~48MB) due to unavailability of gcr.io/distroless/nginx; still optimized from 51.5MB.



Implementation

Backend Dockerfile:

Uses multi-stage build with node:16-alpine for building and gcr.io/distroless/nodejs:16 for production.
Installs only production dependencies (npm ci --only=production).
Copies only essential files (e.g., app.js, node_modules, config, routes, models).


Frontend Dockerfile:

Uses node:16-alpine for building and nginx:alpine for production (distroless nginx unavailable).
Optimizes build with npm ci --only=production and NODE_ENV=production.


Production Dependencies: Ensures package.json separates devDependencies (e.g., testing libraries moved to devDependencies).
Build Optimization: Uses NODE_ENV=production for minified frontend builds.



**Results**

Backend: ~20MB, secure and minimal with distroless.
Security: No shells or package managers in production images.

CI/CD Pipeline
The CI/CD pipeline is implemented using GitHub Actions with three workflows:

build-and-push.yml: Builds Docker images, tags them, and pushes to a container registry.
ci-checks.yml: Runs linting, unit tests, and security scans on code changes.
deploy.yml: Deploys images to production via SSH, updating running containers.

**Workflow Details**

Trigger: Workflows run on push or pull request to the main branch.
Steps:
Build: Compiles images with optimized Dockerfiles.
Test: Executes tests and vulnerability scans (e.g., Trivy for container images).
Push: Stores images in a registry (e.g., Docker Hub, GitHub Container Registry).
Deploy: Pulls images on the production server and updates services.

**github action workflows**
build-and-push.yml
ci-checks.yml
deploy.yml

Rollback:git revert HEAD
git push origin main



Documentation

Environment Variables: Details on configuring .env files.
API Endpoints: Backend API specifications.
Architecture Decisions: Rationale for technical choices.
Troubleshooting Guide: Common issues and solutions.

License
MIT License - See LICENSE for details.
