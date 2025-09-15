# 🌐 FOSSEE System Administrator

A production-ready setup for **Single Sign-On (SSO)** across multiple applications, powered by **Keycloak** on a secure **Rocky Linux 10** droplet.  
This project integrates **Drupal 10**, **Django**, and a **custom PHP app** with centralized identity management using **OpenID Connect**.

***

## 🚀 Project Introduction

Modern software ecosystems often suffer from the complexity and inconvenience of managing multiple user credentials for different applications. This project addresses this challenge by providing a **centralized Single Sign-On (SSO) system** that unifies user authentication across multiple independent applications.

At its core, the project implements **Keycloak**, a robust open-source identity and access management solution, deployed securely on a stable **Rocky Linux 10** server. Keycloak acts as the single source of truth for user identities, allowing users to authenticate once and gain seamless access to all connected applications without repeated logins.

This solution integrates three key applications:

- **Drupal 10** — a popular content management system for building websites.  
- **Django** — a powerful Python web framework for custom application development.  
- **PHP Custom App** — a bespoke PHP-based web application.

All three applications are configured as clients to the Keycloak SSO provider via **OpenID Connect (OIDC)**, enabling secure, standard-compliant authentication flows. This architecture improves user experience, enhances security by centralizing identity management, and simplifies administrative overhead.

### Why This Project?

- **Streamlined user experience:** Users authenticate once and access all apps effortlessly.  
- **Centralized administration:** User roles, permissions, and sessions controlled in one place.  
- **Industry standard protocols:** Uses OIDC for modern, secure authentication.  
- **Extensible & Scalable:** New applications can easily be added as Keycloak clients.  
- **Secure, production-ready:** Leveraging Rocky Linux security best practices and SSL encryption.

This project serves as a practical foundation for organizations looking to implement a unified authentication strategy across diverse enterprise or web applications, ensuring both usability and robust security.

***

## 🚀 Live Demo Links

- 🔑 **Keycloak Admin:** [secureid.ecovix.online](https://secureid.ecovix.online)  
  _Note: Keycloak admin panel is accessible only with master username and password, not with testing account._
- 📰 **Drupal 10:** [drupal.ecovix.online](https://drupal.ecovix.online)  
- 🐍 **Django App:** [django.ecovix.online](https://django.ecovix.online)  
- 🐘 **PHP App:** [php.ecovix.online](https://php.ecovix.online)  

***

## 🎯 Testing Credentials for Evaluation

| Application     | Username       | Password            | Notes                                 |
|-----------------|----------------|---------------------|-------------------------------------|
| Keycloak Admin  | admin-vikash   | (shared securely)    | Admin realm user for management     |
| Drupal          | vikash9006     | (shared securely)    | Valid user created via Keycloak SSO |
| Django          | vikash9006     | (shared securely)    | Admin user mapped via custom backend|
| PHP App         | vikash9006     | (shared securely)    | SSO user matching Keycloak identity |

_Evaluators can use the above credentials for seamless access to Drupal, Django, and PHP applications via Keycloak SSO._

***

## 📖 Overview

This setup ensures **secure, seamless authentication** for users across all integrated apps:  

- Centralized **Keycloak SSO Provider**  
- **Drupal 10** integration via Keycloak module  
- **Django** integration with `mozilla-django-oidc` & custom backend  
- **PHP App** integration using `jumbojett/openid-connect-php`  

🔐 **One Login, Multiple Apps — Unified Identity Management**

***

## 📂 Documentation Structure

| Step | Guide |
|------|-------|
| 1️⃣ Server & Security Setup | [`01-server-setup.md`](Documentation/01-server-setup.md) |
| 2️⃣ Keycloak SSO Configuration | [`02-keycloak.md`](Documentation/02-keycloak.md) |
| 3️⃣ Drupal 10 Deployment + SSO | [`03-drupal-keycloak.md`](Documentation/03-drupal-keycloak.md) |
| 4️⃣ Django Deployment + SSO | [`04-django-keycloak.md`](Documentation/04-django-keycloak.md) |
| 5️⃣ PHP App Deployment + SSO | [`05-php-keycloak.md`](Documentation/05-php-keycloak.md) |
| 6️⃣ Screenshots & Proofs | [`06-screenshots.md`](Documentation/06-screenshots.md) |

***

## 🛠️ Project Steps (Summary)

### 1. **Server Infrastructure & Security**

- Rocky Linux 10 droplet with IPv4 & IPv6  
- Hardened firewall (open: 80, 443, 8080, 22)  
- Disabled root SSH, enabled `deploy` user with sudo  
- Installed base stack: Apache, PHP, MariaDB, Python 3, Java 17, Composer  

📄 Details → [`01-server-setup.md`](Documentation/01-server-setup.md)

***

### 2. **Keycloak SSO Setup**

- Keycloak 24 installed as a systemd service  
- One realm + 3 clients (`drupal`, `django`, `php-app`)  
- Backed by **MariaDB** (not H2)  
- Strict redirect URIs & secured secrets  

📄 Details → [`02-keycloak.md`](Documentation/02-keycloak.md)

***

### 3. **Drupal 10 + Keycloak**

- Installed via Composer, Apache vhost configured  
- Separate MariaDB database  
- Integrated **Keycloak module**  
- Verified login/logout & role mapping  

📄 Guide → [`03-drupal-keycloak.md`](Documentation/03-drupal-keycloak.md)

***

### 4. **Django + Keycloak**

- Virtualenv setup with `mozilla-django-oidc`  
- Gunicorn + Apache reverse proxy  
- **Custom OIDC backend** (`KeycloakOIDCBackend`) for admin role mapping  
- CSRF, trusted origins & static files configured  

📄 Guide → [`04-django-keycloak.md`](Documentation/04-django-keycloak.md)

***

### 5. **PHP App + Keycloak**

- Deployed at `/var/www/php_app`  
- Composer setup with `jumbojett/openid-connect-php`  
- Apache vhost with login/callback/profile endpoints  
- Validated SSO flow  

📄 Guide → [`05-php-keycloak.md`](Documentation/05-php-keycloak.md)

***

### 6. **Screenshots & Proofs**

Includes:

- Firewall & package setup  
- Keycloak clients & admin panel  
- SSO flows across all 3 apps  
- User role-based admin access  

📄 Proofs → [`06-screenshots.md`](Documentation/06-screenshots.md)

***

## 🏆 Highlights

✔️ Centralized **Keycloak SSO**  
✔️ Works with **Drupal, Django, PHP**  
✔️ Fully documented & reproducible  
✔️ **Secure, production-ready** setup

***
