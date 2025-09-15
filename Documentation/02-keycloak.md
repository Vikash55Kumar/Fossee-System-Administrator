
# 2️⃣ Keycloak SSO Server Installation & Configuration

This section documents the installation, configuration, and validation of Keycloak 24 as the SSO provider for all apps, running securely on Rocky Linux 10.

***

## 🚀 2.1 Keycloak Installation

- **Install Java:**  
  ```bash
  sudo dnf install java-17-openjdk-devel -y
  ```

- **Download and extract Keycloak:**  
  ```bash
  cd /opt
  sudo wget https://github.com/keycloak/keycloak/releases/download/24.0.4/keycloak-24.0.4.zip
  sudo unzip keycloak-24.0.4.zip
  sudo mv keycloak-24.0.4 keycloak
  ```

- **Create dedicated user/group and set permissions:**  
  ```bash
  sudo groupadd keycloak
  sudo useradd -r -g keycloak -d /opt/keycloak -s /sbin/nologin keycloak
  sudo chown -R keycloak:keycloak /opt/keycloak
  ```

***

## ⚙️ 2.2 Keycloak as a Systemd Service

- **Create `/etc/systemd/system/keycloak.service`:**
  ```ini
  [Unit]
  Description=Keycloak Authorization Server
  After=network.target

  [Service]
  Type=idle
  User=keycloak
  Group=keycloak
  ExecStart=/opt/keycloak/bin/kc.sh start --optimized --http-port=8080
  LimitNOFILE=102400
  LimitNPROC=102400
  TimeoutStartSec=600
  Restart=on-failure
  RestartSec=30

  [Install]
  WantedBy=multi-user.target
  ```

- **Enable and start the service:**
  ```bash
  sudo systemctl daemon-reload
  sudo systemctl enable --now keycloak
  sudo systemctl status keycloak
  ```

***

## 🔒 2.3 Initial Admin Setup & Access

- Access admin console at:  
  `http://secureid.ecovix.online`

- Created initial admin user (`admin-vikash`).

**Figure 1: Keycloak Login as Admin User**  
<img width="1218" height="892" alt="k6" src="https://github.com/user-attachments/assets/77f5b762-e99b-4192-a502-8388b46d2c33" />

## 🏢 2.4 Realm, Users, and Client Registration

- **Realm:** Created a new production realm named "Keycloak".

- **Users:**  
  - `admin-vikash` (admin user)  
  - `vikash9006` (test SSO user)

**Figure 2: Keycloak Users in Realm**  
<img width="2880" height="1156" alt="k5" src="https://github.com/user-attachments/assets/7707db36-b8f6-4aaa-b3d3-4818493ead67" />
  - Registered for Drupal, Django, and PHP apps as OAuth clients for OpenID Connect SSO.  
  - Configured with strict redirect URIs, client secrets, and allowed web origins.

**Figure 3: Keycloak Clients for SSO Applications**  
<img width="2880" height="1290" alt="k7" src="https://github.com/user-attachments/assets/22d06c5d-9712-4c50-b547-addc7317ff93" />

## ⚙️ 2.5 Production & Security Checks

- Keycloak uses MariaDB as its database backend (configured in `01-server-setup.md`).  
- HTTPS SSL termination is handled by Apache reverse proxies for each app; Keycloak can also be configured accordingly.  
- Admin and client secrets are kept confidential; default/demo users removed prior to production deployment.
***

## 🔜 Next Step

Proceed to:  
[03-drupal-keycloak.md](./03-drupal-keycloak.md) — Drupal 10 Deployment & Keycloak Integration

***
