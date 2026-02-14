# Jenkins CI/CD Setup and Pipeline Documentation

This document explains the installation, configuration, and execution of Jenkins for building and deploying a Dockerized Node.js application using a CI/CD pipeline on Ubuntu.

---

## 1. Jenkins Overview

Jenkins is an open-source automation server used to build, test, and deploy applications automatically. In this setup, Jenkins performs:

* Source code retrieval from GitHub
* Node.js dependency installation
* Application build
* Docker image creation
* Container deployment

---

## 2. Install Java (Jenkins Requirement)

Jenkins requires Java to run.

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

---

## 3. Install Jenkins

### Add Jenkins Repository Key

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | \
sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

### Add Jenkins Repository

```bash
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/" | \
sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### Install Jenkins

```bash
sudo apt update
sudo apt install jenkins -y
```

### Start and Enable Jenkins

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

---

## 4. Access Jenkins Web Interface

Open browser:

```
http://192.168.154.128:8080
```

Retrieve initial admin password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Paste into Jenkins setup page.

---

## 5. Jenkins Initial Setup

1. Select **Install Suggested Plugins**
2. Create admin user
3. Save configuration
4. Finish setup

---

## 6. Allow Jenkins to Use Docker

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
groups jenkins
```

Docker group should appear.

---

## 7. Install Required Plugins

Manage Jenkins → Plugins → Available

Install:

* Git Plugin
* Docker Pipeline
* Pipeline
* NodeJS Plugin

Restart Jenkins after install.

---

## 8. Configure Node.js in Jenkins

Manage Jenkins → Tools → NodeJS

Add:

Name: Node 24
Version: 24.x
Enable automatic installation.

---

## 9. Create Pipeline Job

New Item → Name: `portfolio-ci` → Pipeline → OK

---

## 10. Jenkins Pipeline Script

```groovy
pipeline {
    agent any

    tools {
        nodejs "Node 24"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Repository') {
            steps {
                git 'https://github.com/spiicez21/SPiceZ-Portfolio.git'
            }
        }

        stage('Verify Environment') {
            steps {
                sh 'node -v'
                sh 'npm -v'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Project') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t portfolio-image .'
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker rm -f portfolio-container || true'
                sh 'docker run -d -p 3000:80 --name portfolio-container portfolio-image'
            }
        }
    }
}
```

Save pipeline.

---

## 11. Run Pipeline

Click **Build Now**

Pipeline will:

* Clean workspace
* Clone repo
* Install dependencies
* Build project
* Build Docker image
* Deploy container

---

## 12. Verify Deployment

Access app:

```
http://192.168.154.128:3000
```

Check container:

```bash
docker ps
```

Logs:

```bash
docker logs portfolio-container
```

---

## 13. Expected Result

```
Finished: SUCCESS
```

Application running at:

```
http://192.168.154.128:3000
```

---

## 14. Troubleshooting

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

Check logs:

```bash
sudo journalctl -u jenkins
```

Docker permission issue:

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

Port conflict:

```bash
sudo lsof -i :3000
```

---

## 15. CI/CD Flow Summary

Code push → Jenkins build → Node build → Docker image → Container deploy → Live update

