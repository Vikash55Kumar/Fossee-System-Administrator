Here’s an **optimized version of your `01-server-setup.md`**, fully aligned with your README style, commands, and workflow, integrating your attached images for evaluator-ready proof:

***

# 1️⃣ Server & Security Setup

This phase details launching, securing, and preparing your DigitalOcean droplet for a multi-app, SSO-driven environment.

***

## 🚦 1.1 Droplet Provisioning

- **Provider:** DigitalOcean
- **Image:** Rocky Linux 10 (latest)
- **Plan:** 4GB RAM (recommended 2GB+), IPv4/IPv6 enabled
- **SSH:** Imported public key, root password disabled for login

_Screenshot: DigitalOcean panel, active droplet, and resource details:_
<img width="2490" height="1122" alt="s1" src="https://github.com/user-attachments/assets/6164132e-337e-4df0-8d33-a9840d44fa59" />


***

## 🔐 1.2 User & Access Hardening

- Created `deploy` user and assigned sudo privileges  
- SSH key-based login for secure access  
- Disabled root login for SSH in `/etc/ssh/sshd_config`

```bash
ssh root@<your_droplet_ip>
adduser deploy
passwd deploy
usermod -aG wheel deploy
rsync --archive --chown=deploy:deploy ~/.ssh /home/deploy
# Edit /etc/ssh/sshd_config:
PermitRootLogin no
systemctl restart sshd
```

_Screenshot: Successful SSH login as user with public key:_

<img width="2856" height="380" alt="Screenshot_2025-09-15_12-47-37" src="https://github.com/user-attachments/assets/b370c12c-46e7-4a72-a064-bc4278995c21" />


***

## 🔥 1.3 Firewall & SELinux

Enabled `firewalld` and opened only necessary ports (`80`, `443`, `8080`, `22`):

```bash
systemctl enable --now firewalld
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --permanent --add-service=ssh
firewall-cmd --permanent --add-port=8080/tcp  # Keycloak UI
firewall-cmd --reload
```
SELinux remains in enforcing mode for security.

***

## 📦 1.4 Base Package Install

All required packages for web, database, SSO, and language runtimes:

```bash
dnf update -y
dnf install epel-release -y
dnf install https://rpms.remirepo.net/enterprise/remi-release-10.rpm -y
dnf module enable php:remi-8.3 -y
dnf install httpd php php-cli php-mysqlnd php-gd php-xml php-mbstring php-json php-fpm mariadb-server python3 python3-pip unzip wget java-17-openjdk-devel composer -y
```

***

## ⚙️ 1.5 Core Services

Enabled major services and hardened MariaDB’s root account:

```bash
systemctl enable --now httpd
systemctl enable --now php-fpm
systemctl enable --now mariadb
mysql_secure_installation
```

***

## 🏗️ 1.6 Directory Structure (Planning)

```
/var/www/drupal/         # Drupal app  
/var/www/django_project/ # Django app & venv  
/var/www/php_app/        # PHP app  
/opt/keycloak/           # Keycloak server  
```

***

## 🗝 1.7 Notes

- All admin/system operations after initial setup run as the non-root `deploy` user with sudo.
- Further permissions, web root assignments, and SSL/TLS configuration documented in subsequent `.md` guides.
- Firewall/Security settings restrict all unnecessary access.

***

## 📸 Screenshots

Below:  
- DigitalOcean droplet provisioning and status  
- SSH login as a sudo user with key (non-root)

[1]
[2]

***

**Next Step:**  
→ [02-keycloak.md](./02-keycloak.md) — Keycloak SSO Server Installation & Realm/Client Configuration

***

You can further expand each section with exact timestamps, command outputs, or add more screenshots as needed for full reproducibility and visual proof.

[1](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/63485613/ff69d85f-1c03-4536-8cba-ee12d304198e/s1.jpg)
[2](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/63485613/6c564b97-6c09-4230-ac4b-a3f032d36c3d/Screenshot_2025-09-15_12-47-37.jpg)
