# Project 3 - Notes

This document outlines key design decisions, platform limitations, and reasoning behind excluded technologies in the VRF-Lite WAN project.

---

## 1. Static Routing Only

All routing across this project is done using **manual static routes only**. No dynamic routing protocols (OSPF, BGP, EIGRP, RIP) are used in any phase.

### Why no OSPF/BGP?

- Intentional choice to **challenge myself to manage all routing manually**
- Static routing offers full control over path selection and behavior
- Avoids unintended behavior caused by route redistribution, neighbor instability, or process misconfiguration
- This discipline forces precision and awareness of every hop and dependency

---

## 2. No Linux or Ubuntu Systems

All services (AAA, DHCP) are simulated using only Cisco routers.

### Reason:

- Platform constraint: only CSR1000v and ASA are allowed
- Focus is on pure routing and switching skills, not external server configuration
- Linux is my weakest strength

---

## 3. IPSec Notes

- IPSec is implemented **without GRE** (tunnel mode only)
- **IKEv2** is used exclusively
- `AES-GCM` is **not supported** by the routers in this topology, so `AES-CBC` is used instead
- IPSec tunnels are configured from **R-EDGE (HQ)** to **BRANCH1** and **BRANCH2**
- IPSec is not fully supported on CSR1000v due to licensing issues. I can build, but it won't route.

---

## 4. CoPP Limitation

- Due to platform constraints, **CoPP (Control Plane Policing)** can only be applied per-interface
- `control-plane` syntax is not supported

---

## 5. Simulation Goal

This project simulates a real multi-site enterprise WAN under strict constraints:

- No dynamic routing
- No Linux
- No cloud
- No automation
- Minimal features, maximum manual control

Each enhancement demonstrates a focused real-world capability: security (IPSec, CoPP), address management (DHCP), and authentication (AAA/RADIUS).