# IP Address Plan – Project3-VRFLite-EnterpriseWAN

This document outlines the IP addressing schema used across the entire network. Subnets are assigned to point-to-point WAN links, LAN segments, loopbacks, and simulated service interfaces to ensure deterministic routing and simplified troubleshooting.

---

## Addressing Overview

| Network Segment        | Subnet             | Purpose                                  |
|------------------------|--------------------|------------------------------------------|
| ISP1 ↔ Internet        | 198.168.40.0/24    | DHCP from SOHO router                    |
| ISP2 ↔ Internet        | 198.168.40.0/24    | DHCP from SOHO router                    |
| ISP1 ↔ R-EDGE          | 198.51.100.0/30    | Uplink to ISP1                           |
| ISP2 ↔ R-EDGE          | 203.0.113.0/30     | Uplink to ISP2                           |
| R-EDGE ↔ ASA           | 192.168.10.0/30    | Edge to firewall connection              |
| ASA ↔ CSW-HQ           | 10.10.0.0/30       | Firewall to core                         |
| CSW-HQ ↔ BRANCH1-RTR   | 10.20.0.0/30       | WAN link in VRF CUSTOMER1                |
| CSW-HQ ↔ BRANCH2-RTR   | 10.30.0.0/30       | WAN link in VRF CUSTOMER2                |
| BRANCH1 LAN            | 10.21.0.0/24       | Local subnet for BRANCH1 clients         |
| BRANCH2 LAN            | 10.31.0.0/24       | Local subnet for BRANCH2 clients         |

---

## Loopback Interfaces

| Device         | Loopback IP         | Use Case              |
|----------------|---------------------|-----------------------|
| R-EDGE         | 10.255.0.1/32       | Router-ID             |
| CSW-HQ         | 10.255.0.2/32       | Router-ID             |
| BRANCH1-RTR    | 10.255.1.1/32       | Router-ID             |
| BRANCH2-RTR    | 10.255.1.2/32       | Router-ID             |

---

## NAT and Public Access Notes

- ASA outside interface IP (192.0.2.1) is used for dynamic NAT.
- All internal hosts use PAT via ASA to reach external destinations.
- No NAT is configured on CSR routers.

---

## IPv6 Extensions

- IPv6 dual-stack subnets are defined separately in `02_IPv6_DualStack.md` found in `enhancements/`.
- Global unicast addressing (GUA) uses `2001:db8::/32` reserved lab prefix.

---

## Routing Policy Summary

- Point-to-point WAN links use /30 for clarity and compatibility
- Loopback interfaces are used for stable identifiers and tunnel endpoints
- LAN subnets are routed within each VRF and advertised accordingly