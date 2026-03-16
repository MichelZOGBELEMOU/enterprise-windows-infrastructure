# Enterprise Windows Infrastructure

## Overview

This repository documents a simulated enterprise Windows infrastructure designed to demonstrate practical system administration and enterprise IT operations.

The environment includes Active Directory, enterprise infrastructure services, and operational IT systems deployed in a segmented network architecture.

The project demonstrates the skills expected from a Windows System Administrator or Infrastructure Engineer.

---

## Architecture Overview

The environment simulates a small company with approximately 50 employees and includes:

• Active Directory Domain Services  
• File and Print infrastructure  
• Patch management (WSUS)  
• Internal web services (IIS)  
• Centralized logging  
• Helpdesk ticketing system  
• Backup infrastructure  
• Hybrid identity integration with Microsoft 365  

---

## Infrastructure Diagram

![Infrastructure Architecture](diagrams/infrastructure-architecture.png)

---

## Technology Stack

| Category | Technology |
|--------|--------|
| Hypervisor | Proxmox VE |
| Router | VyOS |
| Identity | Active Directory |
| File Services | SMB / NTFS |
| Patch Management | WSUS |
| Web Services | IIS |
| Logging | Windows Event Forwarding |
| Ticketing | GLPI / osTicket / Zammad |
| Backup | Windows Server Backup |
| Hybrid Identity | Azure AD Connect |

---

## Skills Demonstrated

• Active Directory administration  
• Group Policy management  
• enterprise Windows server deployment  
• file share permission management  
• print server administration  
• patch management with WSUS  
• centralized logging  
• IT helpdesk workflow systems  
• network segmentation and routing  

---

## Documentation Structure

| Section | Description |
|------|------|
| architecture | Infrastructure design and diagrams |
| network | Network topology and routing |
| identity | Active Directory design |
| services | Enterprise services deployment |
| operations | IT operations infrastructure |
| clients | Endpoint systems |
| incidents | Troubleshooting and incident reports |

---

## Key Documentation

- architecture/infrastructure-overview.md
- identity/active-directory-overview.md
- network/network-architecture.md
- services/file-server.md
- operations/helpdesk.md