# 1️⃣ Server & Security Setup

This phase details launching, securing, and preparing your DigitalOcean droplet for a multi-app, SSO-driven environment.

***

## 🚦 1.1 Droplet Provisioning

- **Provider:** DigitalOcean
- **Image:** Rocky Linux 10 (latest release)
- **Plan:** 4GB+ RAM, IPv4/IPv6 enabled
- **SSH:** Imported public key, no root password login

***

## 🔐 1.2 User & Access Hardening

**Initial Steps:**
```bash
ssh root@206.189.138.145
adduser deploy
passwd deploy
usermod -aG wheel deploy
```

**SSH Key Forwarding to deploy:**
```bash
rsync --archive --chown=deploy:deploy ~/.ssh /home/deploy
```

**Restrict root SSH (in `/etc/ssh/sshd_config`):**
```
PermitRootLogin no
```
```bash
systemctl restart sshd
```

***

## 🔥 1.3 Firewall & SELinux

**Enable and lock services (firewalld):**
```bash
systemctl enable --now firewalld
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --permanent --add-service=ssh
firewall-cmd --permanent --add-port=8080/tcp     # Keycloak UI
firewall-cmd --reload
```

***

## 📦 1.4 Base Package Install

```bash
dnf update -y
dnf install epel-release -y
dnf install https://rpms.remirepo.net/enterprise/remi-release-10.rpm -y
dnf module enable php:remi-8.3 -y
dnf install httpd php php-cli php-mysqlnd php-gd php-xml php-mbstring php-json php-fpm mariadb-server python3 python3-pip unzip wget java-17-openjdk-devel composer -y
```

***

## ⚙️ 1.5 Core Services

```bash
systemctl enable --now httpd
systemctl enable --now php-fpm
systemctl enable --now mariadb
```
Run MariaDB hardening:
```bash
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

- All admin operations after initial setup are run as the non-root `deploy` user (with sudo).
- The default `firewalld`, SELinux, and SSH settings are locked down per best practice.
- Further permissions, web root assignments, and SSL will be documented in each component's step.

***

## 📸 Firewall/Service Status Screenshots

(Embed screenshots here showing firewalld config, enabled services, sshd and root/user login status.)

***

**Next Step:** [02-keycloak.md](./02-keycloak.md) — Keycloak SSO Server Installation & Realm/Client Configuration

***

Feel free to adjust directory naming or add more details per your own command log and screenshots! If you want next file intros and transitions or a sample Section 2 (Keycloak), just ask.
