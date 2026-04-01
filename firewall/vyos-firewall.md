# Firewall Architecture — VyOS Zone-Based Policy

## Overview

This document describes the current implementation of the **zone-based firewall architecture** deployed on the VyOS router within the lab environment.

The objective is to provide:

- Network segmentation between infrastructure zones
- Controlled access between clients, servers, management, and internet
- A foundation for future security hardening and micro-segmentation

This implementation reflects a **functional baseline firewall policy**, with iterative improvements planned.

---

## Architecture Context

The firewall is based on a **zone-based model**, where traffic is controlled between defined network zones.

### Zones

| Zone      | Description                          | Interface     |
|----------|--------------------------------------|--------------|
| CLIENT   | User workstations                    | eth1         |
| SERVER   | Core infrastructure servers          | eth2         |
| MGMT     | Management network                   | eth3.30      |
| DMZ      | Public-facing services (future use)  | eth3.40      |
| INTERNET | External network                     | eth0         |

---

## Zone Relationships

Traffic is controlled using **directional policies between zones**:

| Source Zone | Destination Zone | Firewall Ruleset        |
|------------|----------------|--------------------------|
| CLIENT     | INTERNET       | CLIENT-INTERNET-v4       |
| CLIENT     | SERVER         | CLIENT-SERVER-v4         |
| MGMT       | INTERNET       | MGMT-INTERNET-v4         |
| MGMT       | SERVER         | MGMT-SERVER-v4           |
| SERVER     | INTERNET       | SERVER-INTERNET-v4       |

---

## Security Model

The firewall follows a **default deny model**:

- All traffic is denied unless explicitly allowed
- Stateful inspection is enabled via connection tracking

### Base Rules (Applied to All Policies)

| Rule | Action | Description                          |
|------|--------|--------------------------------------|
| 10   | accept | Allow established and related traffic |
| 20   | drop   | Drop invalid packets                  |
| default | drop | Deny all other traffic              |

---

## Ruleset Details

---

### CLIENT → INTERNET

Allows basic web access for client systems.

| Rule | Protocol | Ports        | Purpose              |
|------|----------|-------------|----------------------|
| 30   | TCP      | 80, 443     | Web browsing         |

---

### CLIENT → SERVER

Allows client systems to access core enterprise services.

| Rule | Protocol | Ports                | Purpose                         |
|------|----------|----------------------|----------------------------------|
| 30   | TCP/UDP  | 53                   | DNS                              |
| 40   | TCP/UDP  | 88                   | Kerberos authentication          |
| 50   | TCP/UDP  | 389                  | LDAP                             |
| 60   | TCP/UDP  | 3268                 | Global Catalog                   |
| 70   | TCP      | 445                  | SMB (file shares)                |
| 80   | TCP/UDP  | 464                  | Kerberos password change         |
| 90   | TCP      | 80, 443              | Internal web services            |
| 100  | UDP      | 67, 68               | DHCP                             |
| 110  | TCP      | 8530, 8531           | WSUS                             |
| 120  | TCP      | 49152-65535          | RPC dynamic ports                |
| 130  | ICMP     | -                    | Network diagnostics              |
| 140  | UDP      | 123                  | NTP                              |
| 150  | TCP      | 9389                 | Active Directory Web Services    |
| 160  | TCP      | 135                  | RPC Endpoint Mapper              |

---

### MGMT → INTERNET

Allows limited outbound access from management systems.

| Rule | Protocol | Ports    | Purpose       |
|------|----------|----------|---------------|
| 30   | TCP      | 80, 443  | Updates / tools |

---

### MGMT → SERVER

Allows administrative access to infrastructure servers.

| Rule | Protocol | Ports                | Purpose                    |
|------|----------|----------------------|-----------------------------|
| 30   | TCP/UDP  | 3389                 | RDP                         |
| 40   | TCP      | 5985                 | WinRM                       |
| 50   | TCP      | 22                   | SSH                         |
| 60   | TCP      | 135                  | RPC                         |
| 70   | ICMP     | -                    | Diagnostics                 |
| 80   | TCP      | 445                  | SMB                         |
| 90   | TCP      | 80, 443              | Web services                |
| 100  | TCP      | 49152-65535          | RPC dynamic ports           |

---

### SERVER → INTERNET

Allows servers to access external services when required.

| Rule | Protocol | Ports        | Purpose              |
|------|----------|-------------|----------------------|
| 30   | TCP      | 80, 443     | Updates / downloads  |
| 40   | TCP/UDP  | 53          | DNS resolution       |

---

## Design Decisions

### 1. Zone-Based Firewall Model

A zone-based approach was selected to:

- Improve readability and scalability
- Align with enterprise firewall design patterns
- Simplify policy management between network segments

---

### 2. Stateful Filtering

Connection tracking is used to:

- Automatically allow return traffic
- Reduce rule complexity
- Improve performance

---

### 3. Broad Service Allowance (Initial Phase)

Some services (e.g., RPC dynamic ports) are widely allowed between CLIENT and SERVER zones.

This is intentional to:

- Ensure Active Directory functionality
- Avoid premature complexity during initial deployment

---

## Known Limitations (Current State)

- No micro-segmentation inside the SERVER zone
- Broad access to AD-related services across all servers
- Limited logging and monitoring of firewall events
- No per-service or per-host restrictions
- Management access is not yet fully restricted

---

## Future Improvements

Planned enhancements include:

- Implementing server role-based segmentation (DC, FS, WSUS, APP)
- Restricting AD services to Domain Controllers only
- Introducing logging for denied traffic
- Tightening CLIENT → SERVER access policies
- Hardening MGMT zone access controls
- Implementing DMZ zone for public services

---

## Verification

### Check firewall rules

```bash
show firewall
```

### Check zone assignments

```bash
show firewall zone
```

### Connectivity tests

```bash
ping <target>
nslookup <domain>
Test-NetConnection <server> -Port <port>
```

---

# Troubleshooting Notes

Common issues observed during deployment:

- Group Policy delays due to blocked RPC (port 135)
- DNS resolution failures when port 53 was restricted
- Client login delays due to missing AD service ports

These issues were resolved by:

- Allowing required AD-related ports
- Verifying DNS and time synchronization
- Adjusting firewall rules accordingly

---

# Conclusion

This firewall implementation provides a functional and realistic baseline for a segmented infrastructure environment.

While not fully hardened, it establishes:

- A correct architectural foundation
- Proper use of stateful firewalling
- Realistic enterprise service exposure