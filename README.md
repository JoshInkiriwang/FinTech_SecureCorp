# FinTech Network Security Lab
### Segmented Infrastructure Design and Zone-Based Firewall Implementation

![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![GNS3](https://img.shields.io/badge/Simulation-GNS3-brightgreen?style=for-the-badge)
![VyOS](https://img.shields.io/badge/Firewall-VyOS-blue?style=for-the-badge)
![VLAN](https://img.shields.io/badge/Segmentation-VLAN-orange?style=for-the-badge)

---

## Overview

This project simulates a secure network infrastructure for **FinTech SecureCorp**, a fictional digital payment company handling settlement, payroll, and vendor transactions. Environments where unauthorized access and lateral movement are critical risks.

The Packet Tracer implementation was built as part of a team assignment, where my contribution covered topology implementation, device configuration, IP addressing and subnetting, VLAN setup, ACL, NAT, and port security. After submission, I independently extended the project using GNS3 and VyOS to explore stateful zone-based firewall enforcement, functionality that Packet Tracer cannot simulate and to validate whether the security policies I had implemented would hold under more realistic traffic conditions.

---

## The Problem

A flat network where all departments share the same broadcast domain creates two compounding risks. First, a compromised device can reach any server without crossing a security boundary. Second, lateral movement between user segments goes undetected because there is nothing to detect it.

FinTech SecureCorp's network needed segmentation that could enforce least privilege not just between users and servers, but between user groups as well.

---

## Network Architecture

The design separates traffic into four zones, each with a defined trust level and access policy.

| Zone | Subnet | Devices | Trust Level |
|---|---|---|---|
| DMZ | 10.10.10.0/27 | Web Server, Mail Server | Public-facing, untrusted |
| Server Farm | 10.10.20.0/28 | Database Server, App Server | Restricted, high sensitivity |
| Internal LAN | 10.10.30.0/26, 10.10.40.0/24, 10.10.99.0/28 | Finance, HR, Staff, IT Admin | Internal, role-based access |
| External | 203.0.113.0/30 | ISP Router, Internet PC | Untrusted |

All inter-zone traffic passes through the Firewall/Router, which enforces ACL policies and performs static NAT to expose only the Web Server on port 80/443 to the internet. The IT Admin subnet uses a dedicated /28 block so that SSH management access to all servers can be restricted to a single verifiable source subnet in the ACL, making the policy both auditable and harder to spoof.

Full topology diagram and IP addressing table are in [`/Assets`](./asset).

---

## Security Implementation

**Cisco Packet Tracer — ACL and segmentation layer**

Three extended ACLs enforce traffic policy at the firewall. The most critical rule set blocks all internal LAN traffic from reaching the Server Farm directly, with the exception of IT Admin accessing servers via SSH on port 22. Finance and HR reach the App Server only through the application layer, not by direct database access.

Static NAT with port forwarding exposes only port 80 and 443 from the Web Server to the internet. All other inbound traffic from the ISP is implicitly denied.

Port security on access switches limits each port to one learned MAC address, preventing rogue device connections.

**GNS3 + VyOS — stateful firewall proof of concept**

The GNS3 extension abstracts the full topology into three zones which is CLIENT-A (trusted), CLIENT-B (restricted), and SERVER to isolate and validate stateful firewall behavior that Packet Tracer cannot test.

The enforced policy is:

| Source | Destination | Action | Reason |
|---|---|---|---|
| CLIENT-A | SERVER | Allow | Trusted users require system access |
| CLIENT-B | SERVER | Deny | Restricted users, no server access |
| CLIENT-A | CLIENT-B | Deny | Prevent lateral movement |
| CLIENT-B | CLIENT-A | Deny | Prevent lateral movement |
| SERVER | CLIENT-A | Deny | Server does not initiate to clients |
| SERVER | CLIENT-B | Deny | Server does not initiate to clients |

Each directional policy uses a separate named ruleset with `default-action drop`. Full configuration is in [`/Configurations/vyos-router.txt`](./Configurations/).

---

## Validation

| Source | Destination | Expected | Result |
|---|---|---|---|
| CLIENT-A (10.10.10.2) | SERVER (10.10.30.2) | Allow | Success |
| CLIENT-B (10.10.20.2) | SERVER (10.10.30.2) | Deny | Timeout |
| CLIENT-A (10.10.10.2) | CLIENT-B (10.10.20.2) | Deny | Timeout |
| CLIENT-B (10.10.20.2) | CLIENT-A (10.10.10.2) | Deny | Timeout |

---

## Lessons Learned

**Separate rulesets per direction, not one ruleset for everything.**
The initial GNS3 config applied a single ruleset across all zone transitions. Because zone-based firewall policy is directional, each FROM-TO pair needs its own named ruleset. A single ruleset applied everywhere produces unpredictable behavior because the same rules evaluate against traffic flows they were never designed for.

**Default-action accept is not a default deny model.**
The original config used `default-action accept` with one explicit drop rule — which is a targeted block, not a deny-all posture. Every ruleset now uses `default-action drop` so that any traffic not explicitly permitted is rejected without a matching rule needed.

**Stateful tracking requires established/related rules for return traffic.**
After fixing the default action, CLIENT-A could initiate connections to SERVER but received no replies. The issue was that return traffic from SERVER travels back through BLOCK-LATERAL, which was dropping it. VyOS tracks connection state, so adding `state established enable` and `state related enable` as the first rule in each ruleset allows return traffic from approved sessions without opening broad exceptions.

---

## Future Improvements

- Replace VPCS endpoints with Alpine Linux hosts to enable port-based rule testing with real HTTP and SSH traffic
- Extend GNS3 topology to reflect the full Packet Tracer architecture including DMZ and Server Farm zones
- Add IDS/IPS simulation using Snort on a dedicated GNS3 node
- Implement OSPF between zones to reflect dynamic routing behavior in larger deployments

---

## Repository Structure

```
/Asset          → Topology diagrams, IP addressing table
/Simulation      → Cisco Packet Tracer (.pkt) file
/Configurations  → Device CLI configs (Packet Tracer) + VyOS config
/Docs            → Full technical report (PDF)
```
