# node-todo-cicd

Run these commands:


`sudo apt install nodejs`


`sudo apt install npm`


`npm install`

`node app.js`

or Run by docker compose

test

Great — since your pipeline is working now, here is a **complete end-to-end README (DevOps project documentation)** you can directly copy into GitHub.

This is written in a **real industry format** (good for resume + interviews).

---

# 🚀 Jenkins CI/CD Pipeline for Node.js App using Docker (AWS Ubuntu)

## 📌 Project Overview

This project demonstrates a complete **CI/CD pipeline** using:

* AWS EC2 (Ubuntu)
* Jenkins (Freestyle Job)
* GitHub Webhooks
* Docker & Docker Compose
* Node.js Application

Whenever code is pushed to GitHub, Jenkins automatically pulls the code, builds Docker images, and deploys the application using Docker Compose.

---

# 🏗️ Architecture

```text
Developer
   │
   │ git push
   ▼
GitHub Repository
   │
   │ Webhook Trigger
   ▼
AWS EC2 (Ubuntu Server)
   │
   ▼
Jenkins (Port 8080)
   │
   ▼
Docker Compose Build
   │
   ▼
Node.js Application Container
```

---

# ☁️ 1. AWS EC2 Setup

## Launch EC2 Instance

* OS: Ubuntu 22.04 / 24.04
* Instance type: t2.micro (free tier)
* Open ports in Security Group:

```text
22   - SSH
8080 - Jenkins
80   - HTTP (optional)
```

---

# 🔧 2. Connect to EC2

```bash
ssh -i your-key.pem ubuntu@<EC2-PUBLIC-IP>
```

---

# 🐳 3. Install Docker

```bash
sudo apt update -y
sudo apt install -y ca-certificates curl gnupg lsb-release

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## Add user to Docker group

```bash
sudo usermod -aG docker ubuntu
newgrp docker
```

---

# 🧩 4. Install Docker Compose

Docker Compose v2 comes built-in:

```bash
docker compose version
```

---

# ☕ 5. Install Java (Required for Jenkins)

```bash
sudo apt install fontconfig openjdk-21-jre -y
java -version
```

---

# 🧠 6. Install Jenkins

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | \
sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | \
sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update -y
sudo apt install jenkins -y
```

---

## Start Jenkins

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

Access:

```text
http://<EC2-PUBLIC-IP>:8080
```

Get password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

# 🔌 7. Install Git

```bash
sudo apt install git -y
git --version
```

---

# 🔐 8. Jenkins Configuration

Install plugins:

* Git Plugin
* GitHub Plugin
* Docker Plugin
* Pipeline Plugin

---

# 🔑 9. GitHub Setup

Create GitHub repository:

```text
node-todo-app-deployment
```

Add webhook:

```text
http://<EC2-IP>:8080/github-webhook/
```

Event:

* Push events only

---

# 🔐 10. Jenkins Credentials

Add GitHub token:

* Kind: Username + Password
* Username: GitHub username
* Password: GitHub PAT token
* ID: github-token

---

# 📦 11. Docker Compose File (Project)

Example `docker-compose.yaml`:

```yaml
services:
  web:
    build: .
    ports:
      - "3000:3000"
```

---

# ⚙️ 12. Jenkins Freestyle Job

Create:

```text
New Item → Freestyle Project
```

Configure:

### Source Code Management

* Git repo URL
* Branch: `main`
* Credentials: GitHub token

---

### Build Trigger

✔ GitHub hook trigger for GITScm polling

---

### Build Steps → Execute Shell

```bash
echo "Stopping containers"
docker compose down

echo "Building and deploying"
docker compose up -d --build

echo "Deployment successful"
docker ps
```

---

# 🚀 13. CI/CD Flow

```text
Git Push → GitHub Webhook → Jenkins Trigger → Docker Build → Container Deployment
```

---

# 🧪 14. Testing Deployment

Push code:

```bash
git add .
git commit -m "test deployment"
git push origin main
```

Jenkins automatically:

* Pulls code
* Builds Docker image
* Deploys container

---

# 📊 15. Verify Deployment

Check:

```bash
docker ps
```

Check logs:

```bash
docker compose logs -f
```

Open browser:

```text
http://<EC2-IP>:3000
```

---

# 🛠️ Common Issues & Fixes

### ❌ Docker permission error

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

---

### ❌ compose not found

Use:

```bash
docker compose
```

NOT:

```bash
docker-compose
```

---

### ❌ Webhook not triggering

* Check Jenkins URL
* Check port 8080 open in AWS SG
* Verify GitHub webhook delivery status

---

# 🎯 Outcome

You now have:

✔ Fully automated CI/CD pipeline
✔ GitHub → Jenkins integration
✔ Docker-based deployment
✔ AWS EC2 production setup

