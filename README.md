# Ubuntu DevOps Setup and CI/CD Documentation

This document describes the step by step setup of a development and deployment environment on Ubuntu, including SSH setup, Docker, Node.js, Nginx, Jenkins, Git configuration, and a working Jenkins CI/CD pipeline.

---

## 1. System Update

Update package lists and upgrade installed packages.

```bash
sudo apt update
sudo apt upgrade -y
```

---

## 2. SSH Setup

Install and enable SSH server.

```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

Allow SSH through firewall:

```bash
sudo ufw allow ssh
```

Verify connectivity:

```bash
ip a
ping google.com
```

---

## 3. Git Configuration

### Install Git

```bash
sudo apt install git openssh-client -y
git --version
```

### Configure Git Identity

```bash
git config --global user.name "spiicez21"
git config --global user.email "yuga.bharathijai2106@gmail.com"
git config --list
```

### Generate SSH Key

```bash
ssh-keygen -t ed25519 -C "yuga.bharathijai2106@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```

Add the public key to GitHub and verify:

```bash
ssh -T git@github.com
```

---

## 4. Docker Installation

### Install Dependencies

```bash
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
```

### Add Docker Repository

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Install Docker

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Verify Docker

```bash
sudo docker run hello-world
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world
```

---

## 5. Node.js Installation

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
sudo apt install -y build-essential
node -v
npm -v
```

---

## 6. Nginx Installation

```bash
sudo apt install -y nginx
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
```

Allow firewall:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

---

## 7. Build and Deploy Portfolio with Docker

### Clone Repository

```bash
git clone https://github.com/spiicez21/SPiceZ-Portfolio.git
cd SPiceZ-Portfolio
npm install
```

### Build Application

```bash
npm run build
```

### Deploy via Docker

```bash
docker rm -f portfolio

docker run -d -p 8080:80 \
  --name portfolio \
  -v $(pwd)/dist:/usr/share/nginx/html \
  nginx
```

Restart container:

```bash
docker restart portfolio
```

![output](./Images/1.png)

![output](./Images/12.png)

---

## 8. Jenkins Installation

### Install Java

```bash
sudo apt install openjdk-17-jdk -y
java -version
```

### Add Jenkins Repository

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | \
sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/" | \
sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### Install Jenkins

```bash
sudo apt update
sudo apt install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

### Get Initial Admin Password

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## 9. Jenkins Docker Permissions

Allow Jenkins to use Docker:

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

---

## 10. Jenkins CI/CD Pipeline Execution

The Jenkins pipeline performs:

1. Clean workspace
2. Clone GitHub repository
3. Verify Node and npm
4. Install dependencies
5. Build production bundle
6. Build Docker image
7. Deploy container

### Repository

```
https://github.com/spiicez21/SPiceZ-Portfolio.git
```

### Node Environment

```
Node v24.13.1
npm 11.8.0
```

### Build Output

* Production build completed successfully
* Assets generated under `dist/`
* No build errors
* 2 npm vulnerabilities reported (non-blocking)

### Docker Build

* Multi-stage Docker build
* Node Alpine builder stage
* Nginx Alpine runtime stage
* Image name: `portfolio-image`

### Container Deployment

```bash
docker rm -f portfolio-container
docker run -d -p 3000:80 --name portfolio-container portfolio-image
```

Access application:

```
http://server-ip:3000
```

### Pipeline Result

```
Finished: SUCCESS
```

Deployment completed successfully.


![output](./Images/2.png)

![output](./Images/21.png)

---

## 11. Day 5 – Terraform Installation & Infrastructure Workflow

Day 5 focuses on installing Terraform, configuring AWS credentials, and executing the full Terraform lifecycle including initialization, validation, planning, deployment, and teardown.

---

### Install Terraform

Add HashiCorp repository and install Terraform.

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt install terraform
terraform --version
```

---

### Install AWS CLI

```bash
sudo apt install awscli
cd Downloads/
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

---

### Configure AWS Credentials

```bash
aws configure
aws sts get-caller-identity
```

This step links Terraform with your AWS account.

---

### Create Terraform Project

```bash
cd ~/Projects/devops
mkdir terraform
cd terraform
git init
git checkout -b main
terraform init
```

---

### Terraform Development Workflow

Create infrastructure file:

```bash
touch main.tf
code .
```

Format and validate configuration:

```bash
terraform fmt
terraform validate
```

Preview infrastructure changes:

```bash
terraform plan
```

Deploy infrastructure:

```bash
terraform apply
```

Inspect state:

```bash
terraform show
terraform state list
terraform output
```

Destroy infrastructure:

```bash
terraform plan -destroy
terraform destroy
```

Check Terraform version:

```bash
terraform version
```

---

## 12. Day 6 – Ansible Installation & Configuration Management

Day 6 automates server setup using Ansible over SSH.

---

### Install Ansible

```bash
sudo apt update
sudo apt install ansible -y
ansible --version
which ansible
```

---

### Create Ansible Project

```bash
cd ~/Projects/devops
mkdir ansible
cd ansible
```

---

### Create Inventory File

```bash
nano inventory.ini
```

Inventory with actual server IP:

```
[web]
server1 ansible_host=35.85.32.142 ansible_user=ubuntu ansible_ssh_private_key_file=~/Downloads/openssh.pem
```

Test connection:

```bash
ansible all -i inventory.ini -m ping
```

Expected:

```
server1 | SUCCESS => pong
```

---

### Create Playbook

```bash
nano setup.yml
```

```yaml
- name: Setup Web Server
  hosts: web
  become: true

  tasks:
    - name: Update packages
      apt:
        update_cache: yes

    - name: Install Docker
      apt:
        name: docker.io
        state: present

    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx
      service:
        name: nginx
        state: started
        enabled: true
```

---

### Run Playbook

```bash
ansible-playbook -i inventory.ini setup.yml
```

---

### Verify Services

```bash
ansible all -i inventory.ini -a "systemctl status nginx"
```

Access:

```
http://35.85.32.142
```

---

### Ad-hoc Commands

```bash
ansible all -i inventory.ini -a "uptime"
ansible all -i inventory.ini -m apt -a "name=git state=present" --become
ansible all -i inventory.ini -a "reboot" --become
```

---




