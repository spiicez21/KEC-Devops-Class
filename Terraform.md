# Terraform Installation & Infrastructure Workflow (AWS)

This document explains how to install Terraform, configure AWS credentials, and execute the full Terraform lifecycle to provision and destroy infrastructure.

Terraform lets you define infrastructure as code and manage it safely, repeatably, and version-controlled.

---

## 1. Install Terraform on Ubuntu

Add HashiCorp repository:

```bash
sudo apt-get update
sudo apt-get install -y gnupg software-properties-common
```

Add GPG key:

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```

Verify key:

```bash
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint
```

Add repository:

```bash
echo "deb [arch=$(dpkg --print-architecture) \
signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

Install Terraform:

```bash
sudo apt update
sudo apt install terraform -y
terraform --version
```

---

## 2. Install AWS CLI

```bash
sudo apt install awscli -y
aws --version
```

Or latest version:

```bash
cd ~/Downloads
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
unzip awscliv2.zip
sudo ./aws/install
```

---

## 3. Configure AWS Credentials

```bash
aws configure
```

Enter:

```
AWS Access Key ID
AWS Secret Access Key
Region (example: us-east-1)
Output format: json
```

Verify identity:

```bash
aws sts get-caller-identity
```

This confirms Terraform can talk to your AWS account.

---

## 4. Create Terraform Project

```bash
mkdir -p ~/Projects/devops/terraform
cd ~/Projects/devops/terraform
git init
git checkout -b main
```

Initialize Terraform:

```bash
terraform init
```

---

## 5. Create Infrastructure File

```bash
nano main.tf
```

Example: Create an EC2 instance

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"

  tags = {
    Name = "terraform-web-server"
  }
}
```

Save file.

---

## 6. Terraform Development Workflow

Format config:

```bash
terraform fmt
```

Validate:

```bash
terraform validate
```

Preview changes:

```bash
terraform plan
```

Deploy infrastructure:

```bash
terraform apply
```

Type:

```
yes
```

Terraform creates the EC2 instance.

---

## 7. Inspect Infrastructure

Show state:

```bash
terraform show
```

List resources:

```bash
terraform state list
```

Outputs:

```bash
terraform output
```

---

## 8. Destroy Infrastructure

Preview destruction:

```bash
terraform plan -destroy
```

Destroy everything:

```bash
terraform destroy
```

Confirm:

```
yes
```

AWS resources are removed safely.

---

## 9. Check Terraform Version

```bash
terraform version
```

---

## 10. Terraform Lifecycle Summary

Write code → init → plan → apply → inspect → destroy

Infrastructure becomes reproducible, versioned, and automated.

