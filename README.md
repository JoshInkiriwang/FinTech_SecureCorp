# FinTech SecureCorp Network Security Design

## 📑 Table of Contents

- [Overview](#-overview)
- [Problem Statement](#️-problem-statement)
- [Objectives](#-objectives)
- [Network Architecture](#-network-architecture)
- [Security Implementation](#-security-implementation)
- [Advanced Implementation (GNS3 + VyOS)](#-advanced-implementation)
- [Threat Scenarios & Mitigation](#️-threat-scenarios--mitigation)
- [Results](#-results)
- [Key Learnings](#-key-learnings)
- [Future Improvements](#-future-improvements)
- [Tools Used](#-tools-used)
- [Repository Structure](#-repository-structure)

- https://github.com/JoshInkiriwang/FinTech-SecureCorp-Network-Security/blob/main/README.md#-advanced-implementation-gns3--vyos

## 📌 Overview

This project simulates a secure enterprise-grade network architecture for a FinTech environtment, focusing on protecting sensitive financial systems from unauthorized access and internal threats. The initial design was developed using **Cisco Packet Tracer** to establish core networking concepts such as VLAN segmentation, inter-VLAN routing and access control. To better reflect real-world network behaviour, the project was extended using **GNS3** and **VyOS** to implement zone-based firewall policies and simulate realistic traffic flows. The solution demonstrates how network segmentation, access control, and layered security mechanisms can be combined to reduce attack surface, prevent lateral movement, and enforce least-privilege communication in a production-like environment.

---

## ⚠️ Problem Statement

FinTech systems handle highly sensitive financial and user data, making them a prime target for cyber threats, so it require proper security controls to prevent:

* Unauthorized users accessing critical systems
* Internal network attacks can spread laterally.
* Compromised devices can disrupt operations.

This project addresses these risks by implementing structured network segmentation, and layered security controls.

---

## 🎯 Objectives

* Design a scalable enterprise network for a FinTech environment.
* Implement network segmentation using VLANs.
* Enforce access control using ACLs (Access Control Lists)
* Apply Layer 2 security mechanisms (Port Security)
* Reduce the risk of unauthorized access and lateral movement

---

## 🏗️ Network Architecture

The network is structured into multiple segments to isolate traffic and improve security:

* Department-based VLAN segmentation.
* Inter-VLAN routing using SVI (Switch Virtual Interface).
* NAT configuration for external communication
* Controlled communication between segments

![Network Topology Diagram](Assets/Logical-Topology.png)

---

## 🔐 Security Implementation

1. VLAN Segmentation
   * Separates departments into isolated broadcast domains
   * Limits unnecessary communication between network zones

2. Access Control Lists
   * Restrict traffic between VLANs
   * Enforces least privilege principles
   * Prevents unauthorized access to sensitive servers
   
3. Port Security
   * Restrict switch ports to specific MAC addresses
   * Prevent unauthorized device connections
   * Mitigate risks from rogue devices
   
4. Network Address Translation
   * Hides internal IP addresses.
   * Adds an additional layer of protection for internal systems

---

## 🚀 Advanced Implementation (GNS3 + VyOS)
To enhance the realism of the simulation and align with industry pratices, this project was extended beyond Cisco Packet Tracer using GNS3 and VyOS

🔹 Motivation
While Cisco Packet Tracer is useful for foundational learning, it has limitations in simulating real-world firewall behaviour and network traffic inspection. Therefore, this project was upgraded to:
* Simulate real packet flow and routing behaviour
* Implement zone-based firewall policies
* Gain hands-on experience with Linux-based network operating systems

🔹 Network Segmentation Upgrade
The network was redesign into three main zones:
| Zone     | Subnet        | Description                |
| -------- | ------------- | -------------------------- |
| Client-A | 10.10.10.0/24 | Trusted internal users     |
| Client-B | 10.10.20.0/24 | Restricted users           |
| Server   | 10.10.30.0/24 | Critical financial systems |

🔹 Zone-Based Firewall Implementation

Using VyOS, a zone-based firewall policy was implemented:

* Traffic is controlled based on source zone → destination zone
* Policies are explicitly defined (default deny model)

🔐 Security Policy
| Source   | Destination | Action  |
| -------- | ----------- | ------- |
| Client-A | Server      | ✅ Allow |
| Client-B | Server      | ❌ Deny  |
| Server   | Client-B    | ❌ Deny  |
| Client-A | Client-B    | ✅ Allow |
| Client-B | Client-A    | ✅ Allow |

🔹 Key Configuration Example

set firewall ipv4 name BLOCK-SERVER default-action accept

set firewall ipv4 name BLOCK-SERVER rule 10 action drop
set firewall ipv4 name BLOCK-SERVER rule 10 source address 10.10.20.0/24
set firewall ipv4 name BLOCK-SERVER rule 10 destination address 10.10.30.0/24


🔹 Key Learnings from Advanced Setup
* Understanding zone-based traffic flow (FROM → TO)
* Importance of rule order and implicit deny
* Handling return traffic and state awareness
* Difference between: Cisco ACL (interface-based) and VyOS firewall (policy-based)

---

## ⚔️ Threat Scenarios & Mitigation

Scenario 1: Unauthorized Device Access
   * An attacker connects a device to a switch port
   * Mitigation: Port security blocks unknown MAC addresses

Scenario 2: Unauthorized Access to Server VLAN
   * A user from one department tries to acccess restricted servers
   * Mitigation: ACL rules deny unauthorized traffic
   
Scenario 3: Lateral Movement Risk
   * A compromised device attempts to access other departments.
   * Mitigation: VLAN segmentation limits network exposure
---

## 📊 Results

* Improved network isolation through segmentation
* Controlled access between network segments
* Reduced attack surface within the internal network
* Enhanced security posture for FinTech infrastructure

---

## 🛠️ Key Learnings

* Importance of defense-in-depth strategy
* Practical implementation of network security controls
* Trade-offs between security and network flexibility
* Designing networks with real-world constraints
* Experience troubleshooting firewall misconfigurations (e.g., unintended deny-all rules)
* Understanding real-world debugging process in network security environments

---

## 🔮 Future Improvements

* Implement Dynamic Routing (OSPF) for scalability
* Add IDS/IPS and Honeypot systems for advanced threat detection
* Introduce DNS & Mail Servers in DMZ architecture
* Integrate network monitoring (SNMP-based tools)

---

## 🧰 Tools Used (Extended)

* Cisco Packet Tracer
* Network configuration (VLAN, ACL, NAT, Port Security)
* GNS3
* VyOS
* Wireshark (optional for traffic inspection)

---

## 📁 Repository Structure

* `Docs/` → Technical Report & Documentation
* `Simulation/` → Cisco Packet Tracer (.pkt) file
* `Configurations/` → Device CLI configurations (.txt)
* `Assets/` → Topology diagrams & addressing tables
