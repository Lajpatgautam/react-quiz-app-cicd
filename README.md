# React Quiz App CI/CD using Docker, AWS EC2 and GitHub Actions

## Project Overview

This project demonstrates how to deploy a Dockerized React application on AWS EC2 using GitHub Actions CI/CD pipeline.

Whenever code is pushed to the main branch, GitHub Actions automatically connects to the EC2 instance through SSH, pulls the latest code, rebuilds the Docker container, and deploys the updated application.

---

# Tech Stack

- React
- Docker
- Docker Compose
- AWS EC2 (Ubuntu)
- GitHub
- GitHub Actions
- GitHub Secrets
- Nginx

---

# Step 1: Run the Application Locally

Before deployment, verify that the React application is working correctly.

```bash
npm install
npm start
```

### Output

<img width="1197" height="731" alt="image" src="https://github.com/user-attachments/assets/108e1cdf-7fc6-4c50-9f2b-2bc696ee9014" />

---

# Step 2: Create Production Build

Generate a production-ready build.

```bash
npm run build
```

If the build folder is generated successfully, the application is ready for deployment.

### Output

<img width="841" height="172" alt="image" src="https://github.com/user-attachments/assets/4955b50a-c24f-485d-a85f-a641191c77bc" />

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

<img width="1195" height="725" alt="image" src="https://github.com/user-attachments/assets/1d2bf893-f8a6-47d2-8668-027b14fe454f" />

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

<img width="1202" height="732" alt="image" src="https://github.com/user-attachments/assets/ba67d870-8fa8-4eb9-b802-4470f21acefc" />

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

<img width="1366" height="703" alt="image" src="https://github.com/user-attachments/assets/56c6d52c-1a6f-4203-90f4-a28c021128c5" />

---

# Step 6: Push Project to GitHub

Push the complete project to GitHub.

```bash
git add .
git commit -m "dockerized react app"
git push origin main
```

### Before Push

<img width="1366" height="683" alt="image" src="https://github.com/user-attachments/assets/f0fb4876-1523-40ad-a6b7-4d7de1d5dfd0" />

### Push Successful

<img width="1193" height="732" alt="image" src="https://github.com/user-attachments/assets/0dcd5291-7625-4d48-82e6-6bb6389ca376" />

<img width="1366" height="690" alt="image" src="https://github.com/user-attachments/assets/d4a5fe40-4c96-4ac4-9356-c79a9cb1ea8e" />

---

# Step 7: Launch AWS EC2 Instance

Create an Ubuntu EC2 instance.

Install Git:

```bash
sudo apt update
sudo apt install git -y
```

### Screenshot

<img width="1161" height="345" alt="image" src="https://github.com/user-attachments/assets/58bf952a-1f9a-4e66-aa98-56a9ef43d8e2" />

---

# Step 8: Install Docker on EC2

Install Docker and verify the installation.

```bash
sudo docker run hello-world
```

If the hello-world container runs successfully, Docker is working properly.

---

# Step 9: Clone Repository on EC2

Clone the repository and run the application.

```bash
cd /home/ubuntu

git clone https://github.com/Lajpatgautam/react-quiz-app.git

cd react-quiz-app

sudo docker compose up -d --build
```

### Screenshot

<img width="1365" height="723" alt="image" src="https://github.com/user-attachments/assets/d0a6f2bb-4daf-437e-ae9e-8d3df06b9b39" />

---

# Step 10: Verify Application on EC2

Open the application in the browser using EC2 Public IP.

```text
http://13.201.84.142:8080
```

If the application loads successfully, deployment on EC2 is completed.

---

# Step 11: Configure GitHub Secrets

Navigate to:

```text
Repository
→ Settings
→ Secrets and Variables
→ Actions
```

Create the following repository secrets:

### SSH_HOST

```text
EC2 Public IP
```

### SSH_USERNAME

```text
ubuntu
```

### SSH_KEY

Open your `.pem` file in VS Code and copy the complete content.

Example:

```text
-----BEGIN RSA PRIVATE KEY-----
xxxxxxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxxxxxx
-----END RSA PRIVATE KEY-----
```

Paste the entire key into the SSH_KEY secret.

---

# Step 12: Create GitHub Actions Workflow

Create the following directory:

```text
.github/workflows/
```

Create a file named:

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
            sudo docker compose down || true
            sudo docker compose up -d --build
```

---

# Step 13: Remove Old Workflow

Initially, an S3 deployment workflow was configured and generated errors.

Delete old workflow files such as:

```text
main.yml
```

Keep only:

```text
deploy.yml
```

This ensures that only the EC2 deployment workflow runs.

---

# Step 14: Test CI/CD Pipeline

Modify the application.

Example:

```jsx
<h1>Quiz App Update</h1>
```

Then push the changes.

```bash
git add .
git commit -m "test cicd"
git push origin main
```

After pushing:

- GitHub Actions starts automatically
- GitHub connects to EC2 using SSH
- EC2 pulls the latest code
- Docker rebuilds the container
- Updated application becomes live

No manual deployment is required.

---

# GitHub Actions Success

### Workflow Success

<img width="1193" height="732" alt="image" src="https://github.com/user-attachments/assets/f216bc19-5902-47f4-88ae-cdada1734542" />

---

# Final Output

The updated application is automatically deployed after code push.

<img width="1366" height="690" alt="image" src="https://github.com/user-attachments/assets/edf51b30-c295-4f5f-8b4f-19d904c8c19f" />

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

- Dockerized a React application
- Built a multi-stage Docker image
- Deployed containers on AWS EC2
- Configured GitHub Secrets
- Implemented GitHub Actions CI/CD pipeline
- Automated deployment using SSH
- Performed container rebuild and deployment automatically

---

# Author

**Lajpat Gautam**

GitHub: https://github.com/Lajpatgautam
