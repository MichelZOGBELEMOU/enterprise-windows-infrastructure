# Active Directory Overview

## Overview

This document describes the **Active Directory infrastructure** implemented in the **Enterprise Windows Infrastructure** lab environment.

Active Directory provides **centralized identity management**, **authentication**, **authorization**, and **policy enforcement** for all systems connected to the domain.

The environment simulates a **realistic enterprise identity infrastructure**, allowing administrators to manage users, computers, permissions, and security policies from a centralized platform.

This documentation explains:

- the Active Directory domain architecture
- domain controller roles
- identity management structure
- integration with infrastructure services
- the relationship with Group Policy and enterprise services

---

# Role of Active Directory in the Infrastructure

Active Directory is the **core identity platform** of the environment.

All infrastructure components rely on Active Directory for authentication and access control.

Active Directory enables:

- centralized authentication
- centralized user and computer management
- identity-based access control
- Group Policy configuration
- resource authorization
- service discovery through DNS

All servers and workstations in the lab join the **corp.lab domain** and authenticate against the domain controllers.

---

# Domain Information

| Parameter | Value |
|----------|------|
| Domain Name | corp.lab |
| Forest | corp.lab |
| Directory Service | Active Directory Domain Services |
| Authentication Protocol | Kerberos |
| DNS Integration | Active Directory Integrated DNS |
| Domain Controllers | DC1, DC2 |

The environment uses a **single forest, single domain architecture**, which is common in small and medium enterprise environments.

![alt text](../screenshots/identity/ad-domain.png)

---

# Active Directory Architecture

The Active Directory deployment uses a **single forest structure**.

```
Active Directory Forest
│
└── corp.lab
    │
    ├── Domain Controllers
    │   ├── DC1
    │   └── DC2
    │
    ├── Organizational Units
    │   ├── Users
    │   ├── Computers
    │   └── Servers
    │
    └── Security Groups
```

This architecture simplifies:

- administration
- security management
- Group Policy deployment
- infrastructure scalability

![alt text](../screenshots/identity/ad-ou-structure.png)

---

# Domain Controllers

Two domain controllers are deployed to ensure **redundancy and directory replication**.

| Hostname | Role | Operating System | Services |
|---------|------|------------------|---------|
| DC1 | Primary Domain Controller | Windows Server 2022 | AD DS, DNS |
| DC2 | Secondary Domain Controller | Windows Server 2022 | AD DS, DNS |

Multiple domain controllers provide:

- high availability
- directory replication
- authentication redundancy
- fault tolerance

Active Directory automatically **replicates directory information between DC1 and DC2** using multi-master replication.

Replication ensures that:

- directory objects remain synchronized
- authentication data is consistent
- DNS records remain updated
- Group Policy objects replicate across controllers

![alt text](../screenshots/identity/ad-domain.png)

---

# Functional Levels

The Active Directory deployment uses the following functional levels.

| Level | Version |
|------|------|
| Forest Functional Level | Windows Server 2016 |
| Domain Functional Level | Windows Server 2016 |

This configuration ensures compatibility with legacy systems while enabling modern Active Directory features.

Functional levels determine which Active Directory features are available within the environment.

---

# FSMO Roles

Active Directory uses **Flexible Single Master Operations (FSMO)** roles to manage specific directory tasks.

All FSMO roles are initially hosted on **DC1**.

| FSMO Role | Hosted On |
|----------|-----------|
| Schema Master | DC1 |
| Domain Naming Master | DC1 |
| RID Master | DC1 |
| PDC Emulator | DC1 |
| Infrastructure Master | DC1 |

These roles ensure proper coordination of operations that cannot be handled through multi-master replication.

---

# DNS Integration

Active Directory relies heavily on DNS for service discovery.

DNS services run on both domain controllers.

DNS responsibilities include:

- locating domain controllers
- resolving internal infrastructure hostnames
- supporting domain join operations
- enabling Kerberos authentication

Examples of important Active Directory DNS records:

- `_ldap._tcp.dc._msdcs.corp.lab`
- `_kerberos._tcp.corp.lab`
- host records for infrastructure servers

Active Directory uses **AD-integrated DNS zones**, allowing DNS records to replicate automatically between domain controllers.

![alt text](../screenshots/identity/ad-dns-zones.png)

---

# Organizational Unit Structure

Organizational Units are used to organize directory objects and apply **Group Policy**.

Example OU design implemented in the lab:

```
corp.lab
│
├── Users
│   ├── HR
│   ├── Engineering
│   └── IT
│
├── Computers
│   ├── Workstations
│   └── Laptops

```

This structure allows administrators to:

- apply targeted Group Policies
- separate infrastructure resources
- manage users by department
- organize server systems logically

---

# Authentication Process

When a user logs into a domain computer:

1. The client queries DNS to locate a domain controller.
2. The client contacts the domain controller using **Kerberos authentication**.
3. The domain controller validates the user credentials.
4. An authentication token is issued.
5. Group Policies are applied.
6. The user gains access to authorized resources.

This process ensures **secure centralized authentication across the infrastructure**.

---

# Integration with Infrastructure Services

Active Directory integrates with multiple services deployed in the environment.

| Service | Integration |
|------|-------------|
| File Server (FS1) | File access controlled via AD security groups |
| Print Server (PS1) | Printer deployment via Group Policy |
| WSUS (WSUS1) | Update configuration through Group Policy |
| Application Server (APP1) | Domain authentication |
| HELPDESK1 | User identity reference |
| LOG1 | Event log collection from domain systems |
| BACKUP1 | Backup service authentication |

This integration demonstrates how identity infrastructure supports core enterprise services.

---

# Group Policy Integration

Group Policy enables centralized configuration management across domain systems.

Policies implemented in the lab include:

- password policy
- account lockout policy
- workstation security policy
- printer deployment
- network drive mapping
- Windows Update configuration
- firewall configuration

Group Policy allows administrators to enforce **consistent security and configuration standards** across the infrastructure.

---

# Hybrid Identity Integration

The environment integrates with **Microsoft 365** using **Azure AD Connect**.

Hybrid identity enables:

- synchronization of on-premise users to Azure Active Directory
- access to Microsoft cloud services
- integration with Exchange Online
- modern enterprise identity architecture

This reflects how many modern enterprises combine **on-premise identity infrastructure with cloud identity services**.

---

# Verification

Administrators can verify the health of the Active Directory environment using the following tools.

### Domain Controller Health Check

```
dcdiag /v
```

### Replication Health

```
repadmin /replsummary
repadmin /showrepl
```

### FSMO Role Verification

```
netdom query fsmo
```

These tools help diagnose replication issues, directory service failures, and infrastructure misconfigurations.

---

# Summary

Active Directory is the **central identity system** of the Enterprise Windows Infrastructure lab.

The deployment includes:

- a single forest and single domain architecture
- two domain controllers for redundancy
- Active Directory integrated DNS
- structured Organizational Units
- centralized authentication using Kerberos
- Group Policy configuration management
- hybrid identity integration with Microsoft 365

This infrastructure simulates a **realistic enterprise identity environment** and demonstrates core **Active Directory administration capabilities**.
