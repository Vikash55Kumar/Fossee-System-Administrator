
# 3️⃣ Drupal 10 Deployment & Keycloak SSO Integration

This section details the installation, secure configuration (including SSL), and centralized authentication of Drupal 10 with Keycloak SSO.

***

## 🚀 3.1 Drupal Installation & Environment Setup

- **Create MariaDB database and user:**

```sql
sudo mysql -u root 
CREATE DATABASE drupaldb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'drupaluser'@'localhost' IDENTIFIED BY 'secure_password_here';
GRANT ALL PRIVILEGES ON drupaldb.* TO 'drupaluser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

- **Install Composer (if needed):**

```bash
sudo dnf install composer -y
```

- **Install Drupal 10 via Composer:**

```bash
cd /var/www/
composer create-project drupal/recommended-project drupal
sudo chown -R apache:apache drupal
sudo chmod -R 755 drupal/web
```

***

## ⚙️ 3.2 Apache Configuration

- **Create Apache virtual host `/etc/httpd/conf.d/drupal.conf`:**

```apache
<VirtualHost *:80>
    ServerName drupal.ecovix.online
    DocumentRoot /var/www/drupal/web
    <Directory /var/www/drupal/web>
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog /var/log/httpd/drupal_error.log
    CustomLog /var/log/httpd/drupal_access.log combined
</VirtualHost>
```

***

## 🔐 3.3 SSL/TLS Certification (Let's Encrypt)

- **Install Certbot if not done:**

```bash
sudo dnf install certbot python3-certbot-apache -y
```

- **Obtain and Install Certificate:**

```bash
sudo certbot --apache -d drupal.ecovix.online
```

- **Apache SSL vhost example:**

```apache
<VirtualHost *:443>
    ServerName drupal.ecovix.online
    DocumentRoot /var/www/drupal/web
    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/drupal.ecovix.online/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/drupal.ecovix.online/privkey.pem
    <Directory /var/www/drupal/web>
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog /var/log/httpd/drupal_ssl_error.log
    CustomLog /var/log/httpd/drupal_ssl_access.log combined
</VirtualHost>
```

- **Reload Apache:**

```bash
sudo systemctl restart httpd
```

***

## 🗝️ 3.4 Keycloak Module Installation & Setup

- **Install Keycloak Drupal module:**

```bash
cd /var/www/drupal
composer require drupal/keycloak
```

- **Enable and configure in Drupal UI:**  
  - Go to **Extend**, enable **Keycloak** module.
  - Navigate to **Configuration > Web services > Keycloak** and set Keycloak URL, realm, client ID, and client secret.

***

## 🔄 3.5 Keycloak Client Setup for Drupal

- **In Keycloak Admin Console:**  
  - Create client with:
    - Client ID: `drupal`
    - Valid Redirect URIs: `https://drupal.ecovix.online/keycloak/oauth2/callback`
    - Web Origins: `https://drupal.ecovix.online`
    - Client Auth: Enabled

***

## 🔐 3.6 Testing SSO Flow

- Visit `https://drupal.ecovix.online`
- Use “Login via Keycloak”; sign in with your Keycloak account.
- Validate user mapping and role assignment in Drupal.

***

## 📸 3.7 Screenshots

- **Drupal welcome page:**
<img width="2880" height="1412" alt="d1" src="https://github.com/user-attachments/assets/ff16b7fb-3b56-4fed-97e4-541be84f8248" />

- **Keycloak-authenticated user profile ("vikash9006" view/edit):**  
<img width="2600" height="1214" alt="d2" src="https://github.com/user-attachments/assets/d5e90b45-1e6f-44d9-8afb-4aed58057ce8" />
<img width="1656" height="1486" alt="d3" src="https://github.com/user-attachments/assets/0bfe15c8-7aa9-4c9a-9cd7-eac5fb9fc4c6" />
***

## 🛡️ 3.8 Notes & Best Practices

- SSL certificates auto-renew via Certbot’s built-in renewal cron job.
- All traffic to Drupal is HTTPS-encrypted.
- User, group, and permissions locked down for Apache/Drupal directories.
- Keycloak secrets remain confidential—not committed to any public place.

***

## 🔜 Next Step

Continue to:  
[04-django-keycloak.md](./04-django-keycloak.md) — Django Deployment & Keycloak SSO Integration

***

