# React Quiz App CI/CD using Docker, AWS EC2 and GitHub Actions

## Project Overview

This project demonstrates how to deploy a Dockerized React application on AWS EC2 using GitHub Actions CI/CD pipeline.

Whenever code is pushed to the main branch, GitHub Actions automatically connects to the EC2 instance through SSH, pulls the latest code, rebuilds the Docker container, and deploys the updated application.

---

# Tech Stack

* React
* Docker
* Docker Compose
* AWS EC2 (Ubuntu)
* GitHub
* GitHub Actions
* GitHub Secrets
* Nginx

---

# Step 1: Run the Application Locally

Before deployment, verify that the React application is working correctly.

```bash
npm install
npm start
```

### Output

(Add Screenshot Here)

---

# Step 2: Create Production Build

Generate a production-ready build.

```bash
npm run build
```

If the build folder is generated successfully, the application is ready for deployment.

### Output

(Add Screenshot Here)

---

# Step 3: Create Dockerfile

Create a Dockerfile in the project root directory.

### Dockerfile

```dockerfile
FROM node:20 AS build
WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Screenshot

(Add Screenshot Here)

---

# Step 4: Create docker-compose.yml

Create a docker-compose.yml file in the project root.

### docker-compose.yml

```yaml
services:
  reactapp:
    build: .
    ports:
      - "8080:80"
    restart: always
```

### Screenshot

(Add Screenshot Here)

---

# Step 5: Test Docker Locally

Build and run the Docker container locally.

```bash
docker compose up -d --build
docker ps
```

Open:

```text
http://localhost:8080
```

### Output

(Add Screenshot Here)

---

# Step 6: Push Initial Project to GitHub

Push the complete project to GitHub.

```bash
git add .
git commit -m "dockerized react app"
git push origin main
```

### Output

(Add Screenshot Here)

---

# Step 7: Launch AWS EC2 Instance

Create an Ubuntu EC2 instance.

### Security Group Configuration

Add the following inbound rules:

| Type       | Port | Source    |
| ---------- | ---- | --------- |
| SSH        | 22   | My IP     |
| Custom TCP | 8080 | 0.0.0.0/0 |

---

# Step 8: Install Git on EC2

```bash
sudo apt update
sudo apt install git -y

git --version
```

### Screenshot

(Add Screenshot Here)

---

# Step 9: Install Docker on EC2

```bash
sudo apt update

sudo apt install docker.io -y

docker --version
```

### Start Docker Service

```bash
sudo systemctl start docker

sudo systemctl enable docker
```

### Verify Docker

```bash
sudo docker run hello-world
```

If the hello-world container runs successfully, Docker is working properly.

---

# Step 10: Install Docker Compose

Install Docker Compose Plugin.

```bash
sudo apt update

sudo apt install docker-compose-plugin -y
```

### Verify Installation

```bash
docker compose version
```

Expected Output:

```text
Docker Compose version v2.x.x
```

---

# Step 11: Configure Docker User Permission

Add the ubuntu user to Docker group.

```bash
sudo usermod -aG docker ubuntu

newgrp docker
```

Verify:

```bash
docker ps
```

---

# Step 12: Clone Repository on EC2

Move to home directory.

```bash
cd /home/ubuntu
```

Check whether the project already exists.

```bash
ls
```

If repository is not present:

```bash
git clone https://github.com/Lajpatgautam/react-quiz-app.git
```

Move into project directory.

```bash
cd react-quiz-app
```

---

# Step 13: Run Application on EC2

Build and start the container.

```bash
docker compose up -d --build
```

Verify container:

```bash
docker ps
```

---

# Step 14: Verify Application on EC2

Open the application in browser.

```text
http://YOUR-EC2-PUBLIC-IP:8080
```

If the application loads successfully, deployment on EC2 is completed.

---

# Step 15: Create GitHub Actions Workflow

Create the following directory:

```text
.github/workflows/
```

Create a file:

```text
deploy.yml
```

### deploy.yml

```yaml
name: Deploy React App

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy to EC2 via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USERNAME }}
          key: ${{ secrets.SSH_KEY }}
          script: |
            cd /home/ubuntu/react-quiz-app
            git pull origin main
            docker compose down || true
            docker compose up -d --build
```

---

# Step 16: Configure GitHub Secrets

Navigate to:

```text
Repository
→ Settings
→ Secrets and Variables
→ Actions
```

Create the following repository secrets.

### SSH_HOST

```text
EC2 Public IP
```

### SSH_USERNAME

```text
ubuntu
```

### SSH_KEY

Open your .pem file and copy the complete content.

Example:

```text
-----BEGIN RSA PRIVATE KEY-----
xxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxx
-----END RSA PRIVATE KEY-----
```

Paste the complete key into the SSH_KEY secret.

---

# Step 17: Remove Old Workflows

Delete old workflow files such as:

```text
main.yml
```

Keep only:

```text
deploy.yml
```

This prevents unwanted workflows from running.

---

# Step 18: Trigger CI/CD Deployment

Modify the application.

Example:

```jsx
<h1>Quiz App Update</h1>
```

Push the changes.

```bash
git add .
git commit -m "test cicd"
git push origin main
```

---

# Step 19: Verify GitHub Actions

Navigate to:

```text
Repository
→ Actions
```

Verify:

```text
Green Tick = Success

Red Cross = Failed
```

If successful:

* GitHub Actions starts automatically
* GitHub connects to EC2 using SSH
* EC2 pulls latest code
* Docker rebuilds the image
* Updated container starts automatically

---

# Final Output

The updated application is automatically deployed after every push to the main branch.

---

# CI/CD Flow

```text
Developer
    |
    v
Code Change
    |
    v
git push origin main
    |
    v
GitHub Repository
    |
    v
GitHub Actions Triggered
    |
    v
SSH Connection to EC2
    |
    v
git pull origin main
    |
    v
Docker Compose Rebuild
    |
    v
Updated Container Running
    |
    v
Application Live
```

---

# Key Learning Outcomes

* Dockerized a React application
* Built a multi-stage Docker image
* Deployed containers on AWS EC2
* Installed and configured Docker Compose
* Configured EC2 Security Groups
* Configured GitHub Secrets
* Implemented GitHub Actions CI/CD pipeline
* Automated deployment using SSH
* Performed container rebuild and deployment automatically
* Managed containerized applications using Docker Compose
