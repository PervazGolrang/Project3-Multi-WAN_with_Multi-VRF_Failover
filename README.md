# Project 3 - Multi-VRF Enterprise Core with Failover

## Overview

This repository contains a complete enterprise WAN lab design based on VRF-Lite, NAT, IPsec VPN, and advanced network segmentation using Cisco CSR1000v and ASA. The topology is built with real-world structure and operational realism, not academic shortcuts.

---

## Platform

- Emulator: EVE-NG
- Images:
  - CSR1000v (for IP SLA / WAN failover)

- All routers are CSR1000v
- ASA is in routed mode
- VRF-Lite is used to isolate branches
- Centralized NAT, ACLs, and route control at HQ
- Static routing only (no OSPF, no BGP)

---

## Core Features

| Component      | Description                                                  |
|----------------|--------------------------------------------------------------|
| Dual ISPs      | IP SLA + route tracking with static default route failover   |
| ASA Firewall   | Routed mode with NAT, PAT, ACLs, and inside/outside zoning   |
| VRF-Lite       | One VRF per branch; no route leaking by default              |
| Static Routing | All inter-site routing is static, no dynamic protocols used  |

---

## Directory Layout

```
Project3-MultiVRF_Enterprise/
├── configs/           # Full .txt configs for each device
├── steps/             # Step-by-step initial build process
├── enhancements/      # Step-by-step contd build process
├── docs/              # IP plan, diagram, service descriptions
├── notes/             # Challenges, thoughts, improvements
├── topology/          # .drawio and PNG of lab topology
└── README.md          # This file
```

---

## Network Topology

![`Network Topology`](topology/project3_topology.png)

### Drawio Topology
[`project3_topology.drawio`](topology/project3_topology.drawio)  

---

## Core Implementation Phases

| Step     | Title                       |
|----------|-----------------------------|
| Step 01  | ISP and R-EDGE failover     |
| Step 02  | ASA firewall and NAT        |
| Step 03  | CSW-HQ VRF-Lite core        |
| Step 04  | BRANCH1/2 LAN connectivity  |
| Step 05  | Functional testing          |

---

## Enhancements

| Enhancement File            | Description                                        |
|-----------------------------|----------------------------------------------------|
| 01_IPSec_IKEv2.md           | IPSec tunnel mode to both branches using IKEv2     |
| 02_IPv6_DualStack.md        | Adds IPv6 to all links with static routing         |
| 03_CoPP_Config.md           | Interface-based rate-limiting for control traffic  |
| 04_PBR_and_Leaking.md       | Static inter-VRF route leaking and PBR             |
| 05_DHCP_Relay_and_AAA.md    | DHCP relay and RADIUS/AAA login simulation         |

---

## Platform Constraints

- All routers: Cisco CSR1000v (no Linux, no cloud, no Ansible)
- ASA: Routed mode only (no transparent)
- AES-GCM is not supported (AES-CBC used for IPSec)
- No dynamic routing (OSPF, BGP) in any phase
- CoPP implemented per-interface only (no `control-plane` support)

---

## Goals

This lab simulates a realistic, production-grade multi-site enterprise WAN using only routers and ASA. All configurations are built from scratch, fully documented, and demonstrate applied knowledge in:

- Segmentation (VRF, ACLs)
- Redundancy (IP SLA)
- Security (IPsec, AAA)
- Service simulation (DHCP)
- Platform adaptation under technical limitations
