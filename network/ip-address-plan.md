# IP Address Plan

## Overview

This document defines the IP addressing strategy used in the **Enterprise Windows Infrastructure** lab environment.

A structured IP addressing plan ensures:

- consistent infrastructure deployment
- predictable service addressing
- simplified troubleshooting
- reliable DNS resolution
- professional infrastructure documentation

The addressing plan follows common enterprise practices where:

- **infrastructure servers use static IP addresses**
- **client systems obtain addresses through DHCP**

This approach provides operational stability while allowing scalable client device management.

---

# Network Architecture Context

The lab environment simulates a segmented enterprise network built around a central router.

The **VyOS router** acts as:

- network gateway
- routing device between networks
- firewall boundary
- NAT gateway for internet connectivity

All internal systems communicate through this router.

High-level architecture:

```
Internet
   │
   │
VyOS Router
   │
   ├──────── Infrastructure Network (Servers)
   │           10.30.10.0/24
   │
   └──────── Client Network (Workstations)
               10.30.20.0/24
```

---

# Addressing Design Principles

The addressing model follows several enterprise infrastructure design practices.

---

## 1. Server Static Addressing

All infrastructure servers use **static IP addresses**.

Benefits:

- stable service endpoints
- predictable DNS records
- simplified troubleshooting
- easier infrastructure documentation

---

## 2. Client Dynamic Addressing

Client workstations receive IP addresses dynamically through **DHCP**.

Advantages:

- simplified workstation deployment
- centralized address management
- automated configuration
- reduced administrative overhead

---

## 3. Logical Address Ranges

The network is divided into logical ranges to simplify identification of systems.

| Range | Purpose |
|------|--------|
| .1 | Network gateway |
| .2 – .9 | Reserved infrastructure |
| .10 – .99 | Static infrastructure servers |
| .100 – .200 | DHCP client pool |
| .201 – .254 | Reserved / future expansion |

---

# Infrastructure Server Network

This network hosts all **core enterprise infrastructure services**.

| Parameter | Value |
|----------|------|
| Network | 10.30.10.0/24 |
| Subnet Mask | 255.255.255.0 |
| Gateway | 10.30.10.1 |
| DNS Servers | DC1, DC2 |

---

## Infrastructure Servers

| Hostname | Role | IP Address |
|---------|------|-----------|
| DC1 | Primary Domain Controller | 10.30.10.10 |
| DC2 | Secondary Domain Controller | 10.30.10.11 |
| FS1 | File Server | 10.30.10.20 |
| PS1 | Print Server | 10.30.10.21 |
| WSUS1 | Patch Management | 10.30.10.30 |
| APP1 | Intranet Web Server | 10.30.10.40 |
| HELPDESK1 | Ticketing System | 10.30.10.50 |
| LOG1 | Central Logging Server | 10.30.10.60 |
| BACKUP1 | Backup Server | 10.30.10.70 |

---

# Client Network

The client network contains domain-joined workstation systems.

| Parameter | Value |
|----------|------|
| Network | 10.30.20.0/24 |
| Subnet Mask | 255.255.255.0 |
| Gateway | 10.30.20.1 |
| DNS Servers | 10.30.10.10, 10.30.10.11 |

---

## Client Systems

| Hostname | Role | Address Method |
|---------|------|---------------|
| WIN10-01 | Windows 10 Workstation | DHCP |
| WIN11-01 | Windows 11 Workstation | DHCP |

---

# DHCP Configuration

Dynamic addressing for client systems is provided through the **DHCP service hosted on DC1**.

The DHCP scope distributes IP addresses to workstation systems on the client network.

---

## DHCP Scope Parameters

| Parameter | Value |
|----------|------|
| Scope Network | 10.30.20.0/24 |
| DHCP Range | 10.30.20.100 – 10.30.20.200 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 10.30.20.1 |
| DNS Servers | 10.30.10.10, 10.30.10.11 |
| Domain Name | corp.lab |

---

## DHCP Excluded Range

Infrastructure and reserved addresses are excluded from DHCP allocation.

| Exclusion Range | Purpose |
|----------------|--------|
| 10.30.20.1 – 10.30.20.99 | Reserved infrastructure and static assignments |

This ensures DHCP only distributes addresses within the designated client range.

---

# DHCP Scope Configuration (Implementation)

The following screenshot shows the DHCP scope configuration implemented on **DC1**.

Screenshot location:

```
screenshots/network/dhcp-scope-address-pool.png
```

Captured elements include:

- DHCP scope name
- address pool range
- excluded addresses
- DHCP scope configuration

Example configuration visible in the screenshot:

| Parameter | Value |
|----------|------|
| Start IP | 10.30.10.2 |
| End IP | 10.30.10.100 |
| Exclusion | 10.30.10.1 – 10.30.10.99 |

This configuration ensures infrastructure addresses remain reserved while DHCP distributes client addresses.

---

# DNS Configuration

Internal DNS services are hosted on the **Active Directory domain controllers**.

| Server | Role |
|------|------|
| DC1 | Primary DNS Server |
| DC2 | Secondary DNS Server |

All domain-joined systems must use these DNS servers.

Using internal DNS ensures proper functionality for:

- Active Directory authentication
- domain controller discovery
- service location records
- internal resource resolution

Public DNS servers should **not be configured on domain-joined systems**.

---

# Addressing Diagram

The following diagram illustrates the addressing layout used within the lab environment.

Screenshot location:

```
screenshots/network/ip-address-plan-diagram.png
```

The diagram should display:

- VyOS router
- infrastructure network
- client network
- gateway addresses
- server IP allocations
- DHCP client range

---

# Static Server Configuration

Static addressing is configured manually on each infrastructure server.

Screenshot location:

```
screenshots/network/server-static-ip-config.png
```

Example systems to capture:

- DC1
- FS1
- WSUS1

Configuration elements:

- IP address
- subnet mask
- default gateway
- DNS server configuration

---

# Future Network Expansion

The addressing design allows additional networks to be introduced as the infrastructure evolves.

Example future segments:

| Network | Example Subnet |
|-------|---------------|
| Management Network | 10.30.30.0/24 |
| Monitoring Network | 10.30.40.0/24 |
| DMZ Network | 10.30.50.0/24 |

These additional segments would enable more advanced enterprise infrastructure scenarios including:

- monitoring isolation
- perimeter security zones
- management network separation

---

# Summary

The IP addressing strategy provides a structured and scalable network design for the **Enterprise Windows Infrastructure** lab.

Key benefits include:

- predictable infrastructure addressing
- centralized DHCP client management
- reliable DNS resolution
- scalable network segmentation
- enterprise-style infrastructure documentation

This addressing plan supports the deployment and operation of all infrastructure services within the lab environment.
