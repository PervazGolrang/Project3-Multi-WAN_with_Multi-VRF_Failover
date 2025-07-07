# Step 03 - CSW-HQ Configuration (VRF-Lite Core)

This step configures CSW-HQ as the VRF-Lite core. It connects to the ASA on the global routing table and connects BRANCH1 and BRANCH2 using separate VRFs for routing isolation.

---

## 1. Interface Overview

| Interface | Role                 | IP Address      | VRF          |
|-----------|----------------------|------------------|-------------|
| Gi1       | To ASA (inside)      | 10.10.0.2/30     | Global      |
| Gi2       | To BRANCH1           | 10.20.0.1/30     | CUSTOMER1   |
| Gi3       | To BRANCH2           | 10.30.0.1/30     | CUSTOMER2   |

---

## 2. VRF Definition

RD is not required for VRF-Lite, since no MPLS is involved. However, I still include it as a best practice for future migration to MPLS L3VPN. Including it early helps maintain consistent and scalable configurations.

```bash
ip vrf CUSTOMER1
 rd 65100:1
!
ip vrf CUSTOMER2
 rd 65100:2
```

---

## 3. Interface Configuration

```bash
interface loopback0
 ip address 10.255.0.2 255.255.255.255
!
interface GigabitEthernet1
 description "To ASA inside"
 ip address 10.10.0.2 255.255.255.252
 no shutdown
!

interface GigabitEthernet2
 description "To BRANCH1-RTR"
 ip vrf forwarding CUSTOMER1
 ip address 10.20.0.1 255.255.255.252
 no shutdown
!

interface GigabitEthernet3
 description "To BRANCH2-RTR"
 ip vrf forwarding CUSTOMER2
 ip address 10.30.0.1 255.255.255.252
 no shutdown
```

---

## 4. Interface Configuration

```bash
ip route vrf CUSTOMER1 10.21.0.0 255.255.255.0 10.20.0.2
ip route vrf CUSTOMER1 0.0.0.0 0.0.0.0 10.10.0.1
!
ip route vrf CUSTOMER2 10.31.0.0 255.255.255.0 10.30.0.2
ip route vrf CUSTOMER2 0.0.0.0 0.0.0.0 10.10.0.1
```

---

## 4. Notes
- This device acts as a VRF-aware core router, separating customer/branch routing domains.
- All branch connections use point-to-point links with /30 subnets.
- Default routes are manually injected into each VRF to forward traffic to ASA.