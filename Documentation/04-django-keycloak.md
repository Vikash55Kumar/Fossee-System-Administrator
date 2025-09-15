# 4️⃣ Django Deployment & Keycloak SSO Integration

This section covers deploying the Django application securely with Keycloak SSO, utilizing Gunicorn, Apache as a reverse proxy, and SSL/TLS encryption.

***

## 🚀 4.1 Django Project Setup & Virtual Environment

- **Create project directory and virtual environment:**

```bash
sudo mkdir /var/www/django_project
sudo chown deploy:deploy /var/www/django_project
cd /var/www/django_project
python3 -m venv venv
source venv/bin/activate
```

- **Install required packages:**

```bash
pip install django gunicorn mozilla-django-oidc mysqlclient
```

- **Create Django project:**

```bash
django-admin startproject mysite .
```

- **Configure database settings in `mysite/settings.py` to use MariaDB.**

- **Run migrations and create superuser:**

```bash
python manage.py migrate
python manage.py createsuperuser
```

- **Deactivate virtual environment:**

```bash
deactivate
```

***

## ⚙️ 4.2 SSL/TLS Certification with Let’s Encrypt

- **Install Certbot and Apache plugin:**

```bash
sudo dnf install certbot python3-certbot-apache -y
```

- **Obtain and install SSL certificate:**

```bash
sudo certbot --apache -d django.ecovix.online
```

- **Apache SSL VirtualHost Configuration** (`/etc/httpd/conf.d/django-ssl.conf`):

```apache
<VirtualHost *:443>
    ServerName django.ecovix.online

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/django.ecovix.online/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/django.ecovix.online/privkey.pem

    ProxyPreserveHost On
    ProxyPass / http://unix:/var/www/django_project/gunicorn.sock|http://
    ProxyPassReverse / http://unix:/var/www/django_project/gunicorn.sock|http://

    ErrorLog /var/log/httpd/django_ssl_error.log
    CustomLog /var/log/httpd/django_ssl_access.log combined
</VirtualHost>
```

- **Reload Apache:**

```bash
sudo systemctl reload httpd
```

***

## 🔐 4.3 Custom OIDC Authentication Backend

- **Create `mysite/authentication.py`:**

```python
from mozilla_django_oidc.auth import OIDCAuthenticationBackend

class KeycloakOIDCBackend(OIDCAuthenticationBackend):
    def create_user(self, claims):
        user = super().create_user(claims)
        # Grant admin rights only to authorized emails
        if claims.get('email') in ['admin@example.com']:
            user.is_staff = True
            user.is_superuser = True
        user.save()
        return user
```

- **Update `mysite/settings.py`:**

```python
AUTHENTICATION_BACKENDS = [
    'mysite.authentication.KeycloakOIDCBackend',
    'django.contrib.auth.backends.ModelBackend',
]

INSTALLED_APPS += ['mozilla_django_oidc']

OIDC_RP_CLIENT_ID = "django"
OIDC_RP_CLIENT_SECRET = "<keycloak_client_secret>"

OIDC_OP_AUTHORIZATION_ENDPOINT = "http://secureid.ecovix.online:8080/realms/Keycloak/protocol/openid-connect/auth"
OIDC_OP_TOKEN_ENDPOINT = "http://secureid.ecovix.online:8080/realms/Keycloak/protocol/openid-connect/token"
OIDC_OP_USER_ENDPOINT = "http://secureid.ecovix.online:8080/realms/Keycloak/protocol/openid-connect/userinfo"
OIDC_OP_JWKS_ENDPOINT = "http://secureid.ecovix.online:8080/realms/Keycloak/protocol/openid-connect/certs"

OIDC_RP_SIGN_ALGO = "RS256"

LOGIN_REDIRECT_URL = "/admin/"
LOGOUT_REDIRECT_URL = "/"

CSRF_TRUSTED_ORIGINS = ['https://django.ecovix.online']
ALLOWED_HOSTS = ['django.ecovix.online']
```

***

## 🌐 4.4 Gunicorn & Apache Reverse Proxy Setup

- **Create Gunicorn systemd service** `/etc/systemd/system/gunicorn.service`:

```ini
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=deploy
Group=wheel
WorkingDirectory=/var/www/django_project
ExecStart=/var/www/django_project/venv/bin/gunicorn --workers 3 --bind unix:/var/www/django_project/gunicorn.sock mysite.wsgi:application

[Install]
WantedBy=multi-user.target
```

- **Enable and start Gunicorn:**

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now gunicorn
```

- **Apache virtual host for Gunicorn (SSL config above) proxies to socket**

***

## 🔎 4.5 Testing & Verification

- Visit https://django.ecovix.online/admin/ and login via Keycloak SSO.
- Ensure admin access is granted only to authorized users configured via the custom backend.

***

## 📸 4.6 Screenshots

- **SSO Login Prompt:**  
[1]

- **Django Admin Panel after SSO login:**  
[2]

***

## 🔜 Next Step

Proceed to:  
[05-php-keycloak.md](./05-php-keycloak.md) — PHP Application Deployment & Keycloak SSO Integration

***

This content is ready to be committed, giving a complete, clear, and secure deployment guide aligned with your project standards.

[1](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/63485613/0f786f01-f90a-4249-a788-9d03a7f014cf/s1.jpg)
[2](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/images/63485613/d051f305-e15e-476c-b4b4-2387f496dc95/s2.jpg)

