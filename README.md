# Project 3 – Multi-VRF Enterprise Core with Failover and VLAN Routing

## Overview

This lab simulates a modern enterprise network with multiple isolated branches, VLANs, and dual-WAN failover. The design focuses on routing, segmentation, and high availability without MPLS, Linux, firewalls, or external servers. It is built entirely using Cisco IOSv and CSR1000v devices in EVE-NG.

Technologies demonstrated:
- Multi-VRF isolation using VRF-Lite
- IP SLA-based failover for dual WAN uplinks
- OSPF as IGP for internal core and branches
- Static or BGP-based WAN edge routing
- VLAN routing with ROAS (Router-on-a-Stick)
- VRRP for gateway redundancy
- Structured ACLs and route filtering

All service functions (DHCP, routing, tracking) are handled within routers. No Linux or Fortinet systems are used.

---

## Platform

- Emulator: EVE-NG
- Images:
  - IOSv-L2 (L2 switches)
  - IOSv (L3 routers)
  - CSR1000v (for IP SLA / WAN failover)

---

## Lab Features (Base Build)

### Core Design

- OSPF Area 0 between CORE, DIST, BRANCH, and ISP routers
- Dual-WAN setup with failover using IP SLA + static tracking
- BGP or static routing toward ISPs
- VRRP gateway between CORE1 and CORE2
- VRF separation on branches (e.g., HR, SALES, GUEST)
- VLANs on ACCESS routers (routed by DIST/CORE via ROAS)

### Routing and Segmentation

- Internal routing via OSPF
- External routing via static/BGP (per branch or site)
- VRF-Lite per branch or department
- Manual route-leaking (optional later)
- Loop prevention via route-maps and tags

---

## Topology

[`project3_topology.drawio`](topology/project3_topology.drawio)  
[`project3_topology.png`](topology/project3_topology.png)

---

## Directory Layout

```
Project3-MultiVRF_Enterprise/
├── configs/           # Full .txt configs for each device
├── steps/             # Step-by-step initial build process
├── enhancements/      # Step-by-step contd build process
├── docs/              # IP plan, diagram, service descriptions
├── notes/             # Challenges, thoughts, improvements
├── captures/          # Ping test, route output, failover proof
├── topology/          # .drawio and PNG of lab topology
└── README.md          # This file
```

---

## Lab Goals

- Build a segmented multi-VRF enterprise with OSPF and failover
- Use real-world CCNP-level features like VRRP, IP SLA, and ACLs
- Document the routing design, route separation, and failover logic
- Prepare the lab for optional service enhancements

---

## Enhancements

These features are excluded from the initial build and will be added once the base network is verified and stable:

- DHCP relay across VLANs
- SNMPv3 monitoring configuration
- CoPP (Control Plane Policing)
- IPv6 (OSPFv3 dual stack)
- Manual route leaking between VRFs
- RADIUS/AAA authentication (simulated using router)