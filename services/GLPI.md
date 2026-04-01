# GLPI — Application Configuration (HELPDESK1)

## Overview

This document describes the configuration of the **GLPI application** deployed on the **HELPDESK1** server within the **corp.lab** environment.

The objective is to implement a **centralized IT Service Management (ITSM) platform** supporting:

- Incident management
- User self-service portal
- Role-Based Access Control (RBAC)
- Integration with Active Directory (LDAP)

This document focuses on the **application layer configuration**, following the infrastructure deployment.

---

## Infrastructure Context

| Parameter   | Value            |
| ----------- | ---------------- |
| Application | GLPI             |
| Server      | HELPDESK1        |
| Domain      | corp.lab         |
| Web Stack   | Nginx + PHP-FPM  |
| Database    | MariaDB          |
| Access URL  | http://helpdesk1 |

---

## Application Architecture

```text
User (Domain Account)
        │
        ▼
   GLPI Web Interface
        │
        ▼
 Authentication (LDAP / AD)
        │
        ▼
 Authorization (Profiles / RBAC)
        │
        ▼
 Ticketing System (GLPI Core)
```

---

## Initial Installation

### 1. Web Installation Wizard

- Accessed GLPI via web browser
- Followed installation wizard
- Connected to MariaDB backend
- Initialized database schema

📸 Screenshot Placeholder:
- `assets/images/glpi/install-wizard.png`
- `assets/images/glpi/database-connection.png`

---

### 2. Default Accounts Hardening

GLPI provides default accounts:

| Username  | Role         |
| --------- | ------------ |
| glpi      | Administrator |
| tech      | Technician   |
| normal    | User         |
| post-only | Self-service |

Security actions performed:

- Passwords changed
- Unused accounts disabled or removed
- Default credentials eliminated

📸 Screenshot Placeholder:
- `assets/images/glpi/default-accounts.png`

---

## Entity Design

### 3. Organizational Structure

Entities configured to reflect enterprise hierarchy:

```text
corp.lab (Root Entity)
├── HR
├── Engineering
└── IT
```

- Root entity: global configuration scope
- Child entities: department-level isolation
- Enables delegation and segmentation of IT operations

📸 Screenshot Placeholder:
- `assets/images/glpi/entities-structure.png`

---

## Active Directory Integration (LDAP)

### 4. LDAP Configuration

GLPI configured to authenticate users against **corp.lab Active Directory**.

Configuration details:

- LDAP Server: `dc1.corp.lab`
- Base DN: domain root
- Bind Account: `glpi_sync`
- Authentication method: LDAP bind
- Secure connection: enabled (if applicable)

Purpose:

- Centralized authentication
- Single source of identity (AD)
- Elimination of local account management

📸 Screenshot Placeholder:
- `assets/images/glpi/ldap-config.png`

---

### 5. User Import & Synchronization

- Users imported from Active Directory:
  - `mpark`
  - `jkim`
  - `hrlee`

- Automatic provisioning enabled
- User attributes synchronized from AD

📸 Screenshot Placeholder:
- `assets/images/glpi/user-import.png`

---

## Profiles (RBAC)

### 6. Profile Design

Custom profiles implemented:

| Profile Name     | Description                |
| ---------------- | -------------------------- |
| HR-User          | HR department users        |
| Engineering-User | Engineering users          |
| IT-Technician    | IT support staff           |
| IT-Admin         | Full administrative access |

---

### 7. Permission Model

Access is controlled using **role-based access control (RBAC)**.

**HR / Engineering Users:**
- Create tickets
- View own tickets only

**IT-Technician:**
- View all tickets
- Assign tickets
- Resolve incidents

**IT-Admin:**
- Full system access
- Configuration management

📸 Screenshot Placeholder:
- `assets/images/glpi/profile-permissions.png`

---

## Access Model

- Users authenticate via **Active Directory credentials**
- Authorization handled by **GLPI profiles**
- Entity assignment determines data visibility
- IT staff operate across entities based on permissions

---

## Self-Service Portal Validation

### 8. User Workflow Test

Validation performed using a standard domain user:

- Logged in as `mpark`
- Accessed self-service portal
- Created incident ticket successfully

📸 Screenshot Placeholder:
- `assets/images/glpi/self-service-portal.png`
- `assets/images/glpi/ticket-creation.png`

---

## Notification System

### 9. Notification Configuration

GLPI notification system configured:

- Event-based notifications enabled
- Follow-up alerts configured

SMTP configuration:

- Defined but requires validation for full functionality

📸 Screenshot Placeholder:
- `assets/images/glpi/notifications-config.png`

---

## Functional Validation

### 10. Test Results

| Test Case            | Result   |
| -------------------- | -------- |
| LDAP Authentication  | Success  |
| User Import          | Success  |
| Ticket Creation      | Success  |
| Entity Assignment    | Success  |
| Profile Enforcement  | Partial  |
| Notifications        | Partial  |

---

## Logging & Auditing

- Application logs available within GLPI interface
- Web server logs: `/var/log/nginx/`
- System logs: `journalctl`

Supports:

- Audit of user actions
- Troubleshooting authentication issues
- Monitoring application behavior

---

## Security Considerations

- Default accounts secured or removed
- LDAP bind account with least privilege
- RBAC enforced through profiles
- Administrative access restricted to IT-Admin
- Sensitive configuration protected

---

## Operational Notes

- GLPI fully integrated with Active Directory
- Organizational structure mapped via entities
- RBAC model aligned with enterprise roles
- Platform ready for production-like IT workflows

---

## Next Phase

The platform is ready for:

- Incident lifecycle implementation
- SLA definition
- Ticket categorization
- Escalation workflows
- Monitoring and reporting integration

---

## Conclusion

The GLPI application has been successfully configured as a **centralized IT Service Management platform** within the **corp.lab** environment.

This implementation demonstrates:

- Enterprise authentication via LDAP
- Structured organization using entities
- Role-based access control
- Functional IT service workflow foundation

The next step is to simulate **real-world incidents and operational scenarios**.