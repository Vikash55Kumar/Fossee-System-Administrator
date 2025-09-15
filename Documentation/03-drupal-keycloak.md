Here’s your updated `03-drupal-keycloak.md`, now **including a section for SSL/TLS certification** alongside all core steps and embedded screenshots. This ensures security and evaluator clarity.

***

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

This structure meets security, proof, and evaluation needs, and is ready for direct inclusion with screenshot references.

[1](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/63485613/718209b3-95ce-4d7a-8eea-d6906c2a8777/d1.jpg?AWSAccessKeyId=ASIA2F3EMEYE5PIA7OZD&Signature=EKVbqkVHl0%2FNEX78rW9hoLQNp9E%3D&x-amz-security-token=IQoJb3JpZ2luX2VjEPr%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJHMEUCIQD9Kgz5OK3Br9B5clA5hQPdtIYolHik5mByhJ0Yow35DQIgf0J8xkqNBk2OfRGJ3ZviejOcVDDLjJ4Bu6OLmsCItDgq8QQIcxABGgw2OTk3NTMzMDk3MDUiDMrDKnPjlXZR6jgjjyrOBOk92ck1GklLFUagflp5AB0ok6haMKFWLe%2BAeM1AIX7XQOVTUtDYDJXq3s8kw6GH%2B4Xidax4tQqqIm9dXsgwr2FGCS4JoaI1iN%2FAidqglvS3d1v%2FVSANJ33j0FrO%2BudlcI7uF494ytUEC%2FJWm6%2B0pyGJk4IXsWW1A8RSDN9V6xjO3tebC%2BKg%2FR7dVs0rhr4VWR8Dyqz84CAYouv%2BBKBORnaQxqMtPEuovTAlaWPxluCwWsvmU9909llDpHys07Wzsl9qb4rVmnwaxpMM00GQp%2BxJE97aoXBkS2BmYH01%2BkotLEFeAu3h1H6VdAuEF5i0cvIDtQXJatTFkT6l3xkLgjL6qxheDAsSEeK1S4UGqXENxHgaNxwtoX4gwj0Dm4DRx3gCOHsRzYXV6eDoxdVeburQTGjksTSR6sfc9%2FfShc0jCKRrDKMS7cG2u7EHVSwl3kCZ%2BT0c1FoXR3wqY9pJ0RPh%2FgKCJDvMHAmRDRkjuo75hpjLucbESnlWRwhKHW1pmevJaupwi8awt3A157W1EAu2llzj%2FNBRoPwb6xRixQd129kBmtgHwDovNgQvREFc5RsRfcDxf4VzjbGOmTXJdO4d0AWFyGGj7cqaql86gjKIPhr6oyRLfvQWwo5cnFcNaidSlX82TpMPFcttZGor72jQIjDCJ531CgJFKwkg5OkcBkTS54Et1Cei7EajHrKL7mD%2BsHVQ1ix%2FqoJMTLKOofyQFYpXsDk%2FTf5vHCcTtNdB5VqCyxcKdepW%2FA1OQG0pAdD1o25zt%2F6K6BnRaWOUMOa1n8YGOpoBMAADy0eIsM%2Bk5aCLVP5PF1YR25ePuhlnv%2FCjHg6nF7pUXQCQ0VevRrh91ElPcULM7GOzw2KpInPZrNhdGUOgd5cI1lMhdimerVXJ9EjTeEA%2FhUcrsmkFfxAtSEWHaotDRFW4ifaJcAB74ZFygm7VOUWMaConhmbFRNSM3hiHsjzopOaXhhIo3JB%2FBRzfKpGUFvWgYVLdIGMnew%3D%3D&Expires=1757929726)
[2](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/63485613/b1247210-b57d-4342-9821-1dae9b3276c3/d2.jpg?AWSAccessKeyId=ASIA2F3EMEYE5PIA7OZD&Signature=J7PlWIoQ4uhGqnrlhTXRYSqgPWI%3D&x-amz-security-token=IQoJb3JpZ2luX2VjEPr%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJHMEUCIQD9Kgz5OK3Br9B5clA5hQPdtIYolHik5mByhJ0Yow35DQIgf0J8xkqNBk2OfRGJ3ZviejOcVDDLjJ4Bu6OLmsCItDgq8QQIcxABGgw2OTk3NTMzMDk3MDUiDMrDKnPjlXZR6jgjjyrOBOk92ck1GklLFUagflp5AB0ok6haMKFWLe%2BAeM1AIX7XQOVTUtDYDJXq3s8kw6GH%2B4Xidax4tQqqIm9dXsgwr2FGCS4JoaI1iN%2FAidqglvS3d1v%2FVSANJ33j0FrO%2BudlcI7uF494ytUEC%2FJWm6%2B0pyGJk4IXsWW1A8RSDN9V6xjO3tebC%2BKg%2FR7dVs0rhr4VWR8Dyqz84CAYouv%2BBKBORnaQxqMtPEuovTAlaWPxluCwWsvmU9909llDpHys07Wzsl9qb4rVmnwaxpMM00GQp%2BxJE97aoXBkS2BmYH01%2BkotLEFeAu3h1H6VdAuEF5i0cvIDtQXJatTFkT6l3xkLgjL6qxheDAsSEeK1S4UGqXENxHgaNxwtoX4gwj0Dm4DRx3gCOHsRzYXV6eDoxdVeburQTGjksTSR6sfc9%2FfShc0jCKRrDKMS7cG2u7EHVSwl3kCZ%2BT0c1FoXR3wqY9pJ0RPh%2FgKCJDvMHAmRDRkjuo75hpjLucbESnlWRwhKHW1pmevJaupwi8awt3A157W1EAu2llzj%2FNBRoPwb6xRixQd129kBmtgHwDovNgQvREFc5RsRfcDxf4VzjbGOmTXJdO4d0AWFyGGj7cqaql86gjKIPhr6oyRLfvQWwo5cnFcNaidSlX82TpMPFcttZGor72jQIjDCJ531CgJFKwkg5OkcBkTS54Et1Cei7EajHrKL7mD%2BsHVQ1ix%2FqoJMTLKOofyQFYpXsDk%2FTf5vHCcTtNdB5VqCyxcKdepW%2FA1OQG0pAdD1o25zt%2F6K6BnRaWOUMOa1n8YGOpoBMAADy0eIsM%2Bk5aCLVP5PF1YR25ePuhlnv%2FCjHg6nF7pUXQCQ0VevRrh91ElPcULM7GOzw2KpInPZrNhdGUOgd5cI1lMhdimerVXJ9EjTeEA%2FhUcrsmkFfxAtSEWHaotDRFW4ifaJcAB74ZFygm7VOUWMaConhmbFRNSM3hiHsjzopOaXhhIo3JB%2FBRzfKpGUFvWgYVLdIGMnew%3D%3D&Expires=1757929726)
[3](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/63485613/41747a1d-6547-4ac8-9381-c48aef54dc6c/d3.jpg?AWSAccessKeyId=ASIA2F3EMEYE5PIA7OZD&Signature=nqks1Qroq1gvzS9Do7yXkcb2xso%3D&x-amz-security-token=IQoJb3JpZ2luX2VjEPr%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJHMEUCIQD9Kgz5OK3Br9B5clA5hQPdtIYolHik5mByhJ0Yow35DQIgf0J8xkqNBk2OfRGJ3ZviejOcVDDLjJ4Bu6OLmsCItDgq8QQIcxABGgw2OTk3NTMzMDk3MDUiDMrDKnPjlXZR6jgjjyrOBOk92ck1GklLFUagflp5AB0ok6haMKFWLe%2BAeM1AIX7XQOVTUtDYDJXq3s8kw6GH%2B4Xidax4tQqqIm9dXsgwr2FGCS4JoaI1iN%2FAidqglvS3d1v%2FVSANJ33j0FrO%2BudlcI7uF494ytUEC%2FJWm6%2B0pyGJk4IXsWW1A8RSDN9V6xjO3tebC%2BKg%2FR7dVs0rhr4VWR8Dyqz84CAYouv%2BBKBORnaQxqMtPEuovTAlaWPxluCwWsvmU9909llDpHys07Wzsl9qb4rVmnwaxpMM00GQp%2BxJE97aoXBkS2BmYH01%2BkotLEFeAu3h1H6VdAuEF5i0cvIDtQXJatTFkT6l3xkLgjL6qxheDAsSEeK1S4UGqXENxHgaNxwtoX4gwj0Dm4DRx3gCOHsRzYXV6eDoxdVeburQTGjksTSR6sfc9%2FfShc0jCKRrDKMS7cG2u7EHVSwl3kCZ%2BT0c1FoXR3wqY9pJ0RPh%2FgKCJDvMHAmRDRkjuo75hpjLucbESnlWRwhKHW1pmevJaupwi8awt3A157W1EAu2llzj%2FNBRoPwb6xRixQd129kBmtgHwDovNgQvREFc5RsRfcDxf4VzjbGOmTXJdO4d0AWFyGGj7cqaql86gjKIPhr6oyRLfvQWwo5cnFcNaidSlX82TpMPFcttZGor72jQIjDCJ531CgJFKwkg5OkcBkTS54Et1Cei7EajHrKL7mD%2BsHVQ1ix%2FqoJMTLKOofyQFYpXsDk%2FTf5vHCcTtNdB5VqCyxcKdepW%2FA1OQG0pAdD1o25zt%2F6K6BnRaWOUMOa1n8YGOpoBMAADy0eIsM%2Bk5aCLVP5PF1YR25ePuhlnv%2FCjHg6nF7pUXQCQ0VevRrh91ElPcULM7GOzw2KpInPZrNhdGUOgd5cI1lMhdimerVXJ9EjTeEA%2FhUcrsmkFfxAtSEWHaotDRFW4ifaJcAB74ZFygm7VOUWMaConhmbFRNSM3hiHsjzopOaXhhIo3JB%2FBRzfKpGUFvWgYVLdIGMnew%3D%3D&Expires=1757929726)
