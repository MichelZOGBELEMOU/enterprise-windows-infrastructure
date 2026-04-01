# Incident Report — GPO Failure Due to RPC Port Blocking

## Incident ID
INC-004

---

## Title
Group Policy Processing Failure Caused by Blocked RPC Endpoint Mapper (Port 135)

---

## Environment

| Component         | Value                      |
|------------------|----------------------------|
| Domain           | corp.lab                   |
| Client Network   | 10.30.10.0/24 (CLIENT)     |
| Server Network   | 10.30.20.0/24 (SERVER)     |
| Domain Controller| DC1, DC2                        |
| Firewall         | VyOS (Zone-Based Firewall) |
| Services         | AD DS, DNS, Group Policy   |

---

## Summary

Group Policy processing failed on domain-joined clients.  
Commands such as `gpupdate /force` and `gpresult` were hanging indefinitely.

The issue was caused by a missing firewall rule blocking RPC endpoint mapper traffic (TCP 135), preventing clients from establishing required RPC sessions with the Domain Controller.

---

## Symptoms

- `gpupdate /force` stuck at "Updating Group Policy..."
- `gpresult` stuck at "OS Information"
- Intermittent success after long delays
- No explicit error messages (timeout behavior)

---

## Initial Hypotheses

- DNS misconfiguration
- Kerberos authentication failure
- LDAP or SMB port blocking
- Firewall misconfiguration
- Routing issue

---

## Investigation

### DNS Test
Command: nslookup dc1.corp.lab  
Result: Successful

### Connectivity Tests
Commands:
- Test-NetConnection dc1 -Port 88
- Test-NetConnection dc1 -Port 389
- Test-NetConnection dc1 -Port 445  
Result: Successful

### RPC Test
Command: Test-NetConnection dc1 -Port 135  
Result: Failed

### Firewall Review (VyOS)

Observed:
- DNS (53) allowed
- Kerberos (88) allowed
- LDAP (389) allowed
- SMB (445) allowed
- RPC (135) NOT allowed

---

## Root Cause

TCP port 135 (RPC Endpoint Mapper) was blocked between CLIENT and SERVER zones.

This prevented:
- RPC service discovery
- Dynamic port negotiation (49152–65535)
- GPO retrieval via RPC

Clients entered retry loops, causing operations to hang due to timeouts.

---

## Resolution

Firewall rules updated on VyOS:

- Allow TCP 135 (RPC Endpoint Mapper)
- Allow TCP 49152–65535 (Dynamic RPC range)

Configuration applied and committed.

---

## Result

- `gpupdate /force` completed successfully
- `gpresult` returned full data
- Group Policy applied correctly
- System behavior stabilized

---

## Why It Sometimes Worked Before

- Windows uses retry and timeout logic
- Some attempts reused cached sessions
- Occasional transient success occurred

This created misleading intermittent behavior.

---

## Impact

| Area               | Impact                  |
|------------------|------------------------|
| Group Policy      | Not applied            |
| Client Management | Degraded               |
| Troubleshooting   | Difficult (timeouts)   |
| Reliability       | Intermittent failures  |

---

## Lessons Learned

- Active Directory heavily depends on RPC
- Port 135 is mandatory for RPC endpoint discovery
- Dynamic RPC ports require proper firewall configuration
- Zone-based firewalls must allow full communication paths

---

## Preventive Measures

### Required AD Ports

- TCP 135 (RPC Endpoint Mapper)
- TCP 49152–65535 (Dynamic RPC)
- TCP/UDP 53 (DNS)
- TCP/UDP 88 (Kerberos)
- TCP/UDP 389 (LDAP)
- TCP 445 (SMB)

### Hardening Recommendation

- Restrict RPC dynamic port range (e.g., 50000–50100)
- Align firewall rules accordingly

### Monitoring

- Use Test-NetConnection for validation
- Monitor Group Policy Operational logs

---

## Key Takeaway

If Group Policy hangs instead of failing, suspect RPC or firewall issues first.

---

## Author

Lab: Enterprise Windows Infrastructure (corp.lab)  
Engineer: Michel