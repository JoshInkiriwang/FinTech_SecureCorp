# FinTech SecureCorp Network Security Design

## 📑 Table of Contents

- [Overview](#-overview)
- [Problem Statement](#️-problem-statement)
- [Objectives](#-objectives)
- [Network Architecture](#-network-architecture)
- [Security Implementation](#-security-implementation)
- [Threat Scenarios & Mitigation](#️-threat-scenarios--mitigation)
- [Results](#-results)
- [Key Learnings](#-key-learnings)
- [Future Improvements](#-future-improvements)
- [Tools Used](#-tools-used)
- [Repository Structure](#-repository-structure)

## 📌 Overview

This project presents the design and implementation of a secure enterprise network architecture for a FinTech company using Cisco Packet Tracer. The focus is on network segmentation, access control, and security hardening to protect sensitive financial data from unauthorized access and internal threats.

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

---

## 🔮 Future Improvements

* Implement Dynamic Routing (OSPF) for scalability
* Add IDS/IPS and Honeypot systems for advanced threat detection
* Introduce DNS & Mail Servers in DMZ architecture
* Integrate network monitoring (SNMP-based tools)

---

## 🧰 Tools Used

* Cisco Packet Tracer
* Network configuration (VLAN, ACL, NAT, Port Security)

---

## 📁 Repository Structure

* `Docs/` → Technical Report & Documentation
* `Simulation/` → Cisco Packet Tracer (.pkt) file
* `Configurations/` → Device CLI configurations (.txt)
* `Assets/` → Topology diagrams & addressing tables
