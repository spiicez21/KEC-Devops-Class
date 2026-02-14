# Ansible Installation & Configuration Management (AWS)

This document explains how to install Ansible, configure SSH access to an AWS EC2 server, and automate server setup using playbooks.

Target server: AWS EC2 public IP (38.154.128.221)

---

## 1. Install Ansible on Ubuntu Control Machine

```bash
sudo apt update
sudo apt install ansible -y
ansible --version
which ansible
```

---

## 2. Create Ansible Project Directory

```bash
cd ~/Projects/devops
mkdir ansible
cd ansible
```

---

## 3. Create Inventory File

```bash
nano inventory.ini
```

Example inventory using AWS public IP:

```ini
[web]
server1 ansible_host=38.154.128.221 ansible_user=ubuntu ansible_ssh_private_key_file=~/Downloads/aws-key.pem
```

Replace:

* `38.154.128.221` → your EC2 public IP
* `aws-key.pem` → your actual AWS key pair file

Save file.

---

## 4. Test SSH Connectivity

```bash
ansible all -i inventory.ini -m ping
```

Expected output:

```
server1 | SUCCESS => pong
```

If it fails:

* Check security group allows port 22
* Check key permissions:

```bash
chmod 400 ~/Downloads/aws-key.pem
```

---

## 5. Create Ansible Playbook

```bash
nano setup.yml
```

Playbook:

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

## 6. Run Playbook

```bash
ansible-playbook -i inventory.ini setup.yml
```

This will:

* Update system
* Install Docker
* Install Nginx
* Start and enable Nginx

---

## 7. Verify Services

Check nginx status remotely:

```bash
ansible all -i inventory.ini -a "systemctl status nginx"
```

Access web server:

```
http://38.154.128.221
```

---

## 8. Useful Ad-hoc Commands

Check uptime:

```bash
ansible all -i inventory.ini -a "uptime"
```

Install git:

```bash
ansible all -i inventory.ini -m apt -a "name=git state=present" --become
```

Reboot server:

```bash
ansible all -i inventory.ini -a "reboot" --become
```

---

## 9. Expected Result

* SSH automation working
* Docker installed
* Nginx running
* AWS server configured via Ansible

---
