# Firewall Configuration — Servers (GPO-Based)

## Overview

This document describes the design, implementation, and validation of the **server firewall architecture** in the **corp.lab** domain.

The objective is to enforce a **secure, role-based firewall model** using:

- Windows Defender Firewall with Advanced Security
- Group Policy Objects (GPO)
- Active Directory security groups

This implementation follows an **enterprise defense-in-depth approach**, combining:

- Network-level filtering (VyOS)
- Host-based firewall enforcement (Windows)

---

## Architecture Design

The firewall architecture is divided into two logical layers:

### 1. Baseline Policy (All Servers)

**GPO Name:** `SEC-Firewall-Policy-Servers`  
**Scope:** Servers OU  
**Applies To:** All domain-joined servers  

#### Configuration

| Setting                  | Value |
|--------------------------|------|
| Firewall State           | Enabled |
| Inbound Connections      | Block (default) |
| Outbound Connections     | Allow (default) |

#### Purpose

This baseline ensures:

- All inbound traffic is denied unless explicitly allowed
- Services must be deliberately exposed
- A consistent security posture across all servers

---

### 2. Role-Based Firewall Rules

Firewall rules are separated by **server role** and deployed using dedicated GPOs.

Each GPO is applied using **security group filtering**, ensuring only relevant servers receive the appropriate rules.

---

## Active Directory Security Groups

| Group Name   | Purpose |
|--------------|--------|
| SRV-DC       | Domain Controllers |
| SRV-FILE     | File Servers |
| SRV-WEB      | Web Servers (APP1, HELPDESK1) |
| SRV-WSUS     | WSUS Server |
| SRV-PS       | Print Server |

---

## Firewall Rule GPOs

### Domain Controllers

**GPO:** `SEC-Firewall-rules-DC`  
**Security Filtering:** `SRV-DC`

#### Allowed Services

| Service        | Protocol | Port |
|----------------|----------|------|
| DNS            | TCP/UDP  | 53   |
| Kerberos       | TCP/UDP  | 88   |
| LDAP           | TCP/UDP  | 389  |
| LDAPS          | TCP      | 636  |
| SMB            | TCP      | 445  |
| RPC Endpoint   | TCP      | 135  |
| RPC Dynamic    | TCP      | 49152–65535 |
| NTP            | UDP      | 123  |

#### Traffic Scope

| Source              | Destination        | Purpose |
|---------------------|-------------------|--------|
| 10.30.10.0/24       | Domain Controllers| Client authentication (Kerberos, LDAP, DNS) |
| 10.30.20.0/24       | Domain Controllers| Server-to-DC communication |
| Domain Controllers  | Domain Controllers| Replication |

#### Notes

- RPC dynamic ports are required for AD replication and Group Policy
- Missing these ports can cause:
  - Slow logon
  - GPO processing delays
  - RPC failures

---

### File Server

**GPO:** `SEC-Firewall-rules-FS`  
**Security Filtering:** `SRV-FILE`

#### Allowed Services

| Service | Protocol | Port |
|---------|----------|------|
| SMB     | TCP      | 445  |

#### Traffic Scope

| Source        | Destination | Purpose |
|--------------|------------|--------|
| 10.30.10.0/24 | File Server | File access (mapped drives, shares) |

---

### Web Servers (APP1 / HELPDESK1)

**GPO:** `SEC-Firewall-rules-WEBS`  
**Security Filtering:** `SRV-WEB`

#### Allowed Services

| Service | Protocol | Port |
|---------|----------|------|
| HTTP    | TCP      | 80   |
| HTTPS   | TCP      | 443  |

#### Traffic Scope

| Source        | Destination | Purpose |
|--------------|------------|--------|
| 10.30.10.0/24 | Web Servers | User access to applications and helpdesk |

---

### WSUS Server

**GPO:** `SEC-Firewall-rules-WSUS`  
**Security Filtering:** `SRV-WSUS`

#### Allowed Services

| Service    | Protocol | Port |
|------------|----------|------|
| WSUS HTTP  | TCP      | 8530 |

#### Traffic Scope

| Source        | Destination | Purpose |
|--------------|------------|--------|
| 10.30.10.0/24 | WSUS Server | Client update retrieval |

---

### Print Server

**GPO:** `SEC-Firewall-rules-PS`  
**Security Filtering:** `SRV-PS`

#### Allowed Services

| Service | Protocol | Port |
|---------|----------|------|
| SMB     | TCP      | 445  |
| RPC     | TCP      | 135  |

#### Traffic Scope

| Source        | Destination | Purpose |
|--------------|------------|--------|
| 10.30.10.0/24 | Print Server | Printer access and spooler communication |

---

## Example Traffic Flows

Client → Domain Controller:
- DNS (53)
- Kerberos (88)
- LDAP (389)

Client → File Server:
- SMB (445)

Client → WSUS:
- HTTP (8530)

Client → Web Servers:
- HTTP/HTTPS (80/443)

---

## Design Decisions

### Default Deny Model

All servers follow a **default deny inbound policy**, ensuring:

- Only required services are exposed
- Attack surface is minimized

---

### Role-Based Separation

- Each role has its own GPO
- Prevents rule sprawl
- Simplifies troubleshooting

---

### Security Group Filtering

- Decouples firewall policy from OU structure
- Enables flexible server assignment
- Reflects enterprise best practices

---

### Defense in Depth

| Layer   | Technology |
|--------|-----------|
| Network | VyOS Firewall |
| Host    | Windows Defender Firewall |

---

## Logging and Monitoring

Firewall logging is enabled to capture:

- Dropped packets
- Allowed connections (when needed)

Logs are analyzed using:

- Event Viewer (Windows Defender Firewall)
- Centralized logging server (LOG1)

Purpose:

- Troubleshooting connectivity issues
- Detecting misconfigurations
- Supporting incident response

---

## Validation

### Group Policy Verification

```powershell
gpresult /r