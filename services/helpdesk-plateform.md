# Helpdesk Platform — Infrastructure Deployment (HELPDESK1)

## Overview

This document describes the deployment of the **Helpdesk Platform infrastructure** in the **corp.lab** environment.

The objective is to provide a **reliable, centralized service platform** hosting the GLPI application using a modern Linux-based stack.

This document focuses strictly on the **infrastructure layer**, including system deployment, service stack, network integration, and operational readiness.

---

## Infrastructure Context

| Parameter   | Value                    |
| ----------- | ------------------------ |
| Server Name | HELPDESK1                |
| Domain      | corp.lab                 |
| Role        | Helpdesk Platform Server |
| OS          | Ubuntu Server            |
| Network     | 10.30.20.0/24            |
| Web Stack   | Nginx + PHP-FPM          |
| Database    | MariaDB                  |

HELPDESK1 is a **domain-joined Linux member server** responsible for hosting the enterprise helpdesk system.

---

## Architecture Overview

```text
Users (Windows Clients)
        │
        ▼
     Nginx (HTTP/HTTPS)
        │
        ▼
     PHP-FPM
        │
        ▼
      GLPI
        │
        ▼
    MariaDB (Local)
```

---

## Network Role

HELPDESK1 is deployed within the **Server VLAN (10.30.20.0/24)**.

- Routed via **VyOS**
- Accessible from domain-joined clients
- DNS handled by **Active Directory (DC1/DC2)**

---

## Access Model

- SSH access via AD users (SSSD)
- Admin access restricted to IT group
- Web access for domain users
- Centralized identity via corp.lab

---

## System Deployment

### 1. Operating System Installation

```bash
sudo apt update && sudo apt upgrade -y
```

📸 `assets/images/helpdesk/os-installation.png`

---

### 2. Network Configuration

```bash
ping dc1.corp.lab
nslookup corp.lab
```

📸 `assets/images/helpdesk/network-dns-validation.png`

---

### 3. Domain Integration

```bash
sudo realm join corp.lab
realm list
id administrator@corp.lab
```

📸 
- `assets/images/helpdesk/domain-join-success.png`
- `assets/images/helpdesk/ad-user-login.png`

---

## Web Stack Deployment

### 4. Nginx

```bash
sudo apt install nginx -y
```

📸 `assets/images/helpdesk/nginx-default-page.png`

---

### 5. PHP-FPM

```bash
sudo apt install php-fpm php-mysql php-xml php-mbstring php-curl php-zip php-gd -y
```

📸 `assets/images/helpdesk/php-fpm-status.png`

---

### 6. MariaDB

```bash
sudo apt install mariadb-server -y
sudo mysql_secure_installation
```

📸 
- `assets/images/helpdesk/mariadb-secure-install.png`
- `assets/images/helpdesk/database-created.png`

---

## Nginx Configuration

```nginx
server {
    listen 80;
    server_name helpdesk1.corp.lab;

    root /var/www/glpi/public;
    index index.php;

    location / {
        try_files $uri /index.php$is_args$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/glpi /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

📸 
- `assets/images/helpdesk/nginx-config.png`
- `assets/images/helpdesk/nginx-test-success.png`

---

## File System

```bash
sudo chown -R www-data:www-data /var/www/glpi
sudo chmod -R 755 /var/www/glpi
```

📸 `assets/images/helpdesk/glpi-permissions.png`

---

## Logging

- /var/log/nginx/
- /var/log/php*
- journalctl

```bash
journalctl -u nginx
journalctl -u php8.2-fpm
```

---

## Service Validation

```bash
systemctl status nginx
systemctl status php8.2-fpm
systemctl status mariadb
```

📸 `assets/images/helpdesk/services-status.png`

---

## Web Access

- http://helpdesk1
- http://helpdesk1.corp.lab

📸 `assets/images/helpdesk/glpi-install-page.png`

---

## Security

- Firewall: HTTP/HTTPS only
- DB bound to localhost
- Least privilege services
- AD-based access control

---

## Troubleshooting

| Issue | Cause | Fix |
|------|------|-----|
| Nginx fail | config | nginx -t |
| 502 | PHP down | restart php |
| DB error | creds | check config |
| DNS | AD issue | check resolv |

---

## Future Improvements

- HTTPS (PKI / Let's Encrypt)
- Monitoring (Prometheus)
- Logging (Loki)
- Backup strategy

---

## Conclusion

HELPDESK1 provides a **centralized helpdesk infrastructure** using a modern Linux stack integrated with Active Directory.

This demonstrates:

- Linux system administration
- AD integration
- Web stack deployment
- Operational readiness