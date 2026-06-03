# react-quiz-app-cicd
# Firstly check your app are running in local browser or not (npm start)
<img width="1197" height="731" alt="image" src="https://github.com/user-attachments/assets/108e1cdf-7fc6-4c50-9f2b-2bc696ee9014" />

# 1.Production build
npm run build
it means if build folder created app is ready for deploy
<img width="841" height="172" alt="image" src="https://github.com/user-attachments/assets/4955b50a-c24f-485d-a85f-a641191c77bc" />
# 2.Docker file
In project root there should be 2 files 
# i Docker file
<img width="1195" height="725" alt="image" src="https://github.com/user-attachments/assets/1d2bf893-f8a6-47d2-8668-027b14fe454f" />
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
# ii docker-compose.yml
<img width="1202" height="732" alt="image" src="https://github.com/user-attachments/assets/ba67d870-8fa8-4eb9-b802-4470f21acefc" />
services:
  reactapp:
    build: .
    ports:
      - "8080:80"
    restart: always
  # 4. Local Docker test
  docker compose up -d --build
  docker ps
  // Open in browser(http://localhost:8080)
  <img width="1366" height="703" alt="image" src="https://github.com/user-attachments/assets/56c6d52c-1a6f-4203-90f4-a28c021128c5" />
  # 5. GitHub repo
  ** push your code in github
  git add .
git commit -m "dockerized react app"
git push origin main
# Before push
<img width="1366" height="683" alt="image" src="https://github.com/user-attachments/assets/f0fb4876-1523-40ad-a6b7-4d7de1d5dfd0" />

<img width="1193" height="732" alt="image" src="https://github.com/user-attachments/assets/0dcd5291-7625-4d48-82e6-6bb6389ca376" />
<img width="1366" height="690" alt="image" src="https://github.com/user-attachments/assets/d4a5fe40-4c96-4ac4-9356-c79a9cb1ea8e" />
# 6. EC2 setup
# EC2 Ubuntu instance me
sudo apt update
sudo apt install git
<img width="1161" height="345" alt="image" src="https://github.com/user-attachments/assets/58bf952a-1f9a-4e66-aa98-56a9ef43d8e2" />
Phir Docker install karo aur test karo:
sudo docker run hello-world
Agar hello-world chal gaya, Docker ready hai.
# 7.EC2 par repo clone
cd /home/ubuntu
git clone https://github.com/Lajpatgautam/react-quiz-app.git
cd react-quiz-app
sudo docker compose up -d --build
<img width="1365" height="723" alt="image" src="https://github.com/user-attachments/assets/d0a6f2bb-4daf-437e-ae9e-8d3df06b9b39" />
Browser me EC2 public IP ke saath port 8080 kholo:
http://13.201.84.142:8080
# 8. GitHub Secrets
Repo → Settings → Secrets and variables → Actions me 3 secrets chahiye:

SSH_HOST = EC2 public IP
SSH_USERNAME = ubuntu
SSH_KEY = .pem file ka full content( open .pem in vs code and copy all content)
# change some content in app.js and run command that are shown in below
# Final Output
<img width="1193" height="732" alt="image" src="https://github.com/user-attachments/assets/f216bc19-5902-47f4-88ae-cdada1734542" />
<img width="1366" height="690" alt="image" src="https://github.com/user-attachments/assets/edf51b30-c295-4f5f-8b4f-19d904c8c19f" />
# writer: lajpat Gautam
















