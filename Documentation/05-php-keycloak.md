# 5️⃣ PHP Application Deployment & Keycloak SSO Integration

This section covers deploying a secure PHP application, integrating it with Keycloak SSO using the `jumbojett/openid-connect-php` library, and enabling SSL/TLS encryption.

***

## 🚀 5.1 PHP Application Deployment

- **Create web root and set proper permissions:**

```bash
sudo mkdir /var/www/php_app
sudo chown -R apache:apache /var/www/php_app
sudo chmod -R 755 /var/www/php_app
```
- **Deploy all PHP source code** to `/var/www/php_app`.

***

## ⚙️ 5.2 Apache Configuration

- **Create Apache virtual host `/etc/httpd/conf.d/php_app.conf`:**

```apache
<VirtualHost *:80>
    ServerName php.ecovix.online
    DocumentRoot /var/www/php_app
    Redirect permanent / https://php.ecovix.online/
</VirtualHost>
```

- **SSL Configuration `/etc/httpd/conf.d/php_app-ssl.conf`:**

```apache
<VirtualHost *:443>
    ServerName php.ecovix.online
    DocumentRoot /var/www/php_app

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/php.ecovix.online/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/php.ecovix.online/privkey.pem

    <Directory /var/www/php_app>
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog /var/log/httpd/php_app_ssl_error.log
    CustomLog /var/log/httpd/php_app_ssl_access.log combined
</VirtualHost>
```

***

## 🔐 5.3 SSL/TLS Setup with Let’s Encrypt

- **Install Certbot and Apache plugin (if not done):**

```bash
sudo dnf install certbot python3-certbot-apache -y
```

- **Obtain and install certificate:**

```bash
sudo certbot --apache -d php.ecovix.online
```

- All traffic now served via **HTTPS** (`https://php.ecovix.online`).

***

## 📦 5.4 PHP OpenID Connect SSO Implementation

- **Install Composer dependency:**

```bash
cd /var/www/php_app
sudo composer require jumbojett/openid-connect-php
```

- **Sample `login.php`:**

```php
<?php
require 'vendor/autoload.php';
use Jumbojett\OpenIDConnectClient;

session_start();

$oidc = new OpenIDConnectClient(
    'http://secureid.ecovix.online:8080/realms/Keycloak', // Keycloak URL/realm
    'php-app',                                            // Client ID
    '<your_client_secret>'                                // Client Secret
);

$oidc->authenticate();
$_SESSION['user_info'] = $oidc->requestUserInfo();
header('Location: /profile.php');
exit();
?>
```

- **Sample `profile.php`:**

```php
<?php
session_start();
if (empty($_SESSION['user_info'])) {
    header('Location: /login.php');
    exit();
}
$user = $_SESSION['user_info'];
echo "<h1>Welcome, " . htmlspecialchars($user->name) . "</h1>";
echo "<p>Email: " . htmlspecialchars($user->email) . "</p>";
?>
```

***

## 🔄 5.5 Keycloak Client Configuration

- **Keycloak Client settings for this app:**

| Option                 | Value                                         |
|------------------------|-----------------------------------------------|
| Client ID              | `php-app`                                     |
| Valid Redirect URIs    | `https://php.ecovix.online/callback.php`      |
| Web Origins            | `https://php.ecovix.online`                   |
| Client Authentication  | Enabled                                       |
| Secret                 | (copy and use in your PHP code)               |

***

## 🧪 5.6 Testing SSO Flow

- Visit `https://php.ecovix.online/login.php`
- Authenticate using your Keycloak account
- Confirm profile information is retrieved through SSO and displayed

***

## 📸 5.7 Screenshots

<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/7e8f9253-ce3d-4136-afa4-c1ae354ee563" alt="p1" width="500"/></td>
    <td><img src="https://github.com/user-attachments/assets/2d9914f3-f8a3-4f4f-914e-84e894fa5231" alt="p2" width="500"/></td>
  </tr>
  <tr>
    <td align="center">PHP App SSO Login page</td>
    <td align="center">Keycloak login screen for SSO</td>
  </tr>
</table>

<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/578ffec4-477a-49c0-b7e0-bd9ceb616b13" alt="p3" width="500"/></td>
    <td><img src="https://github.com/user-attachments/assets/0dcdee7e-82bb-4c14-bbde-f557cd3d618e" alt="p4" width="500"/></td>
  </tr>
  <tr>
    <td align="center">Post-login user profile page</td>
    <td align="center">Keycloak logout screen</td>
  </tr>
</table>

<img width="2470" height="1372" alt="php" src="https://github.com/user-attachments/assets/d84628d4-901f-4c87-8d6b-92a4522e91af" />

***

## 🛡️ 5.8 Security Best Practices

- All frontend/backoffice operations accessed only via HTTPS.
- SSL certificate auto-renews with Certbot; see [Let’s Encrypt renewal docs](https://certbot.eff.org/docs/using.html#renewal).
- Client secret not committed to code repositories.
- PHP app runs with minimal permissions owned by Apache user.

***

## 🔜 Next Step

Continue to:  
[06-screenshots.md](./06-screenshots.md) — Full Screenshots & Project Proofs

***


