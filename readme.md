
# Jenkins CI/CD Documentation 🚀

This repository contains step-by-step Jenkins setup instructions, agent configuration, and a working Jenkins pipeline example using Docker Compose.

---

## 📌 Prerequisites

- AWS EC2 instance (Ubuntu recommended)
- Java (OpenJDK 11+)
- Jenkins installed (`sudo apt install jenkins`)
- Docker & Docker Compose installed
- Git installed

---

## 🖥️ Jenkins Setup on EC2

1. Launch an EC2 instance with:
   - Port **22** open (SSH)
   - Port **8080** open (Jenkins Web UI)

2. Connect via SSH:
   ```bash
   ssh -i your-key.pem ubuntu@<your-ec2-public-ip>
   ```

3. Install Java & Jenkins:
   ```bash
   sudo apt update
   sudo apt install -y openjdk-11-jdk jenkins
   sudo systemctl enable jenkins
   sudo systemctl start jenkins
   ```

4. Open browser:
   ```
   http://<your-ec2-ip>:8080
   ```
   Follow the setup instructions and install suggested plugins.

---

## 🤝 Jenkins Agent Setup (via SSH)

1. On Jenkins master:
   ```bash
   sudo su - jenkins
   ssh-keygen -t rsa -b 4096 -C "jenkins-agent"
   ```

2. Copy **public key** (`id_rsa.pub`) to the agent machine:
   ```bash
   ~/.ssh/authorized_keys
   ```

3. Add **private key** to Jenkins:
   - Jenkins → Credentials → Global → Add Credentials
   - Type: SSH Username with private key

4. Jenkins UI:
   - Manage Jenkins → Nodes → New Node → `jenkins-agent`
   - Remote root directory: `/home/jenkins`
   - Launch method: SSH → select added SSH credentials

---

## 📄 Sample Jenkinsfile (Declarative Pipeline)

```groovy
pipeline {
  agent { label "jenkins-agent" }

  stages {
    stage('Cloning Code') {
      steps {
        echo 'Code clonning'
        git url: "https://github.com/Devansh-Chauhan-GitHub/react_django_demo_app.git", branch: "main"
        echo 'Code cloned'
      }
    }

    stage('Dockerfile') {
      steps {
        sh "docker-compose down"
      }
    }

    stage('Deployed') {
      steps {
        echo 'Deploying Code'
        sh "docker-compose up --build -d"
      }
    }
  }
}
```

### 🔍 What it does:

| Stage            | Action                                                                 |
|------------------|------------------------------------------------------------------------|
| Cloning Code     | Clones the GitHub repo                                                 |
| Dockerfile       | Shuts down any previously running containers                           |
| Deployed         | Builds and runs containers in detached mode using Docker Compose       |

---

## 🌐 Access Your App

After deployment, access your app via:
```
http://<your-agent-ec2-ip>:8000
```

Make sure your app/service in `docker-compose.yml` exposes port 8000.

---

## 🧪 Test It

- Check logs with:
  ```bash
  docker-compose logs
  ```
- Visit Jenkins console output to see build progress.

---

## ✅ Summary

This repo documents:
- Jenkins EC2 setup
- SSH-based agent configuration
- Jenkinsfile example using Git + Docker Compose

---

Happy CI/CD-ing! 🎉
