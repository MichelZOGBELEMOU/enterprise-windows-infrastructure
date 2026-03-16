# Infrastructure Server Inventory

## Overview

This document provides the server inventory for the **Enterprise Windows Infrastructure** lab environment.

The purpose of this inventory is to present a clear and professional view of the systems deployed in the environment, including their roles, operating systems, network placement, and operational purpose.

This document is designed to resemble internal infrastructure documentation used by enterprise IT teams for:

- infrastructure asset tracking
- operational visibility
- support reference
- architecture documentation
- troubleshooting preparation

---

# Skills Demonstrated

This infrastructure environment demonstrates practical experience with:

- Active Directory infrastructure deployment
- Windows Server administration
- DNS architecture
- Group Policy management
- SMB file services
- Print server administration
- Patch management with WSUS
- centralized logging and event collection
- backup and recovery infrastructure
- enterprise network segmentation
- infrastructure documentation practices

---

# Infrastructure Architecture Context

The lab environment is organized into two primary network segments.

```
User Network
10.30.10.0/24

WIN10-01
WIN11-01

        │
        │
      VyOS1
Router / Firewall / Gateway
        │
        │
Server Network
10.30.20.0/24

DC1
DC2
FS1
PS1
WSUS1
APP1
HELPDESK1
LOG1
BACKUP1
```

---

## Recommended Screenshot

Place a network diagram showing the infrastructure topology.

Screenshot location:

screenshots/architecture/server-inventory/server-network-topology.png

---

# Infrastructure Server Inventory

| Asset ID | Hostname | Role | OS | Network | IP Address | Domain Joined | Criticality | Status | Notes |
|---------|----------|------|----|--------|------------|---------------|-------------|--------|------|
| SRV-01 | DC1 | Primary Domain Controller, DNS | Windows Server 2022 | Server Network | 10.30.20.x | Yes | Critical | Deployed | Hosts AD DS and DNS |
| SRV-02 | DC2 | Secondary Domain Controller | Windows Server 2022 | Server Network | 10.30.20.x | Yes | Critical | Planned | Directory redundancy |
| SRV-03 | FS1 | File Server | Windows Server 2022 | Server Network | 10.30.20.x | Yes | High | Planned | Departmental SMB shares |
| SRV-04 | PS1 | Print Server | Windows Server 2022 | Server Network | 10.30.20.x | Yes | Medium | Planned | Centralized printer deployment |
| SRV-05 | WSUS1 | Patch Management Server | Windows Server 2022 | Server Network | 10.30.20.x | Yes | High | Planned | Windows Server Update Services |
| SRV-06 | APP1 | Intranet / Application Server | Windows Server 2022 | Server Network | 10.30.20.x | Yes | Medium | Planned | Internal IIS application |
| SRV-07 | HELPDESK1 | Ticketing System | Windows Server or Linux | Server Network | 10.30.20.x | Optional | Medium | Planned | Helpdesk platform |
| SRV-08 | LOG1 | Central Logging Server | Windows Server 2022 | Server Network | 10.30.20.x | Yes | High | Planned | Windows Event Forwarding |
| SRV-09 | BACKUP1 | Backup Server | Windows Server 2022 | Server Network | 10.30.20.x | Yes | Critical | Planned | Backup repository |
| NET-01 | VyOS1 | Router / Firewall / VLAN Gateway | VyOS | Infrastructure Edge | 10.30.x.1 | No | Critical | Deployed | Routing, NAT, firewall |

---

# Client Systems

| Asset ID | Hostname | Role | Operating System | Network | IP Address | Domain Joined | Status |
|---------|----------|------|------------------|--------|------------|---------------|--------|
| CL-01 | WIN10-01 | User Workstation | Windows 10 | User Network | 10.30.10.x | Yes | Planned |
| CL-02 | WIN11-01 | User Workstation | Windows 11 | User Network | 10.30.10.x | Yes | Planned |

---

# Server Role Descriptions

## DC1 — Primary Domain Controller

Responsibilities:

- Active Directory Domain Services
- DNS name resolution
- authentication and authorization
- Group Policy foundation

DC1 is the most critical system in the infrastructure.

---

## DC2 — Secondary Domain Controller

Responsibilities:

- Active Directory replication partner
- directory service redundancy
- secondary DNS service
- fault tolerance simulation


---

## FS1 — File Server

Responsibilities:

- SMB file share hosting
- departmental storage
- NTFS permission management

Example shares:

\\FS1\Public  
\\FS1\HR  
\\FS1\Engineering  
 

---

## PS1 — Print Server

Responsibilities:

- centralized printer management
- printer queue hosting
- printer deployment through Group Policy


---

## WSUS1 — Patch Management Server

Responsibilities:

- centralized Windows Update management
- update approval workflow
- update compliance monitoring

---

## APP1 — Intranet / Application Server

Responsibilities:

- IIS web hosting
- internal service publishing
- DNS service integration

Example application:

- corporate intranet portal
- internal IT support page


---

## HELPDESK1 — Ticketing Server

Responsibilities:

- incident management
- ticket tracking
- helpdesk workflow simulation

Possible platforms:

- GLPI
- Zammad
- osTicket

---

## LOG1 — Central Logging Server

Responsibilities:

- Windows Event Forwarding
- centralized log analysis
- troubleshooting support

---

## BACKUP1 — Backup Infrastructure Server

Responsibilities:

- backup repository
- scheduled backups
- restore validation


---

## VyOS1 — Infrastructure Router

Responsibilities:

- inter-VLAN routing
- firewall enforcement
- NAT for outbound internet access
- network segmentation



# Status Definitions

| Status | Meaning |
|------|--------|
| Planned | Server exists in design but not yet deployed |
| Deployed | System installed and reachable |
| Configured | Core services installed |
| Operational | Fully validated and in use |
| Retired | System removed from environment |

---

# Asset Management Standards

All systems in this environment follow these standards:

- role-based hostname convention
- static IP addresses for infrastructure servers
- domain membership for Windows servers
- configuration changes documented
- role documentation maintained
- operational screenshots collected as evidence

---

# Related Documentation

architecture/infrastructure-overview.md  
network/ip-address-plan.md  
identity/active-directory-overview.md  
services/file-server.md  
services/print-server.md  
services/wsus.md  
operations/logging.md  
operations/backup.md  

---

# Conclusion

The infrastructure server inventory provides a centralized view of all systems deployed within the **Enterprise Windows Infrastructure** environment.

This documentation supports:

- infrastructure visibility
- operational awareness
- troubleshooting preparation
- infrastructure design understanding
- portfolio presentation quality

As the lab evolves, this document should be continuously updated to reflect the actual deployed infrastructure.
