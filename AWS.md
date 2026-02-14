# AWS EC2 Ubuntu Instance Setup – SpiceZ (us-east-1 Virginia)

This document explains how to launch an Ubuntu EC2 instance in AWS (us-east-1 Virginia), configure HTTP access, and install Apache web server.

---

## 1. Create EC2 Instance

### Step 1: Open AWS Console

* Login to AWS
* Go to **EC2 Dashboard**
* Click **Launch Instance**

---

### Step 2: Instance Configuration

**Name**

```
spicez
```

**Region**

```
us-east-1 (N. Virginia)
```

**AMI**

```
Ubuntu Server 22.04 LTS
```

**Instance Type**

```
t2.micro (Free tier eligible)
```

---

### Step 3: Key Pair

* Create new key pair
* Name: `spicez-key`
* Type: RSA
* Format: .pem
* Download and store safely

---

### Step 4: Security Group (Allow HTTP + SSH)

Create security group rules:

| Type | Protocol | Port | Source    |
| ---- | -------- | ---- | --------- |
| SSH  | TCP      | 22   | My IP     |
| HTTP | TCP      | 80   | 0.0.0.0/0 |

This allows:

* SSH remote access
* Public web access

---

### Step 5: Launch Instance

Click:

```
Launch Instance
```

Wait until status = **Running**

Copy the **Public IPv4 address**

---

## 2. Connect to EC2 via SSH

On your local machine:

```bash
chmod 400 spicez-key.pem
ssh -i spicez-key.pem ubuntu@YOUR_PUBLIC_IP
```

Example:

```bash
ssh -i spicez-key.pem ubuntu@3.91.xxx.xxx
```

You are now inside the Ubuntu server.

---

## 3. Update Ubuntu Server

```bash
sudo apt update
sudo apt upgrade -y
```

---

## 4. Install Apache Web Server

```bash
sudo apt install apache2 -y
```

Start Apache:

```bash
sudo systemctl start apache2
sudo systemctl enable apache2
```

Check status:

```bash
sudo systemctl status apache2
```

---

## 5. Allow HTTP Through Firewall

```bash
sudo ufw allow 'Apache'
sudo ufw enable
```

---

## 6. Test Web Server

Open browser:

```
http://YOUR_PUBLIC_IP
```

You should see:

```
Apache2 Ubuntu Default Page
```

This confirms Apache is running.

---

## 7. Apache Basic Commands

Restart Apache:

```bash
sudo systemctl restart apache2
```

Stop Apache:

```bash
sudo systemctl stop apache2
```

Check status:

```bash
sudo systemctl status apache2
```

---

## 8. Default Web Directory

Apache serves files from:

```
/var/www/html
```

Edit default page:

```bash
sudo nano /var/www/html/index.html
```

Add your own HTML content.

---

## Result

* Ubuntu EC2 instance running in us-east-1
* SSH access working
* HTTP enabled
* Apache installed and serving web pages
