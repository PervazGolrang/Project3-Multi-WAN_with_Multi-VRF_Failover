# 04 - Policy-Based Routing and Static Route Leaking (VRF-Lite)

This document implements controlled communication between isolated VRFs using static route leaking and policy-based routing (PBR). VRF-Lite is designed to separate branch networks, but in some enterprise cases, selective communication between them is required (e.g. shared services).

All routing is static. No dynamic routing protocols are used.

---

## 1. Objective

- Allow specific subnets in BRANCH1 to reach selected destinations in BRANCH2
- Maintain default isolation between VRFs
- Do not allow full two-way routing between VRFs

---

## 2. VRF Assignments

| Device      | Interface        | VRF        |
|-------------|------------------|------------|
| CSW-HQ      | Gi1              | CUSTOMER1  |
| CSW-HQ      | Gi2              | CUSTOMER2  |
| BRANCH1-RTR | All interfaces   | CUSTOMER1  |
| BRANCH2-RTR | All interfaces   | CUSTOMER2  |

---

## 3. Static Route Leaking (One-Way)

Routes from BRANCH2 are injected into CUSTOMER1’s routing table at CSW-HQ.

### On CSW-HQ:

This allows CUSTOMER1 to reach BRANCH2’s LAN via CSW-HQ.

```bash
ip route vrf CUSTOMER1 10.31.0.0 255.255.255.0 10.30.0.2
```

---

## 4. Policy-Based Routing (Selective Forwarding)

Only traffic from specific hosts or subnets in BRANCH1 is permitted to use the leaked route to BRANCH2.

### ACLs
```bash
ip access-list extended PBR_ALLOW_BR1_TO_BR2
 permit ip 10.21.0.0 0.0.0.255 10.31.0.0 0.0.0.255
```

### Route-Map
```bash
route-map PBR-TO-BR2 permit 10
 match ip address PBR_ALLOW_BR1_TO_BR2
 set ip next-hop 10.30.0.2
```

### Apply to inbound Inteface (CSW-HQ side of BRANCH1)
```bash
interface GigabitEthernet1
 ip policy route-map PBR_ALLOW_BR1_TO_BR2
```

### Return Route (BRANCH2 -> BRANCH1)
```bash
ip route vrf CUSTOMER2 10.21.0.0 255.255.255.0 10.20.0.2
```

---

## 6. Verification

### From BRANCH1-RTR

```bash
ping vrf CUSTOMER1 10.31.0.1
```

### From BRANCH2-RTR

```bash
ping vrf CUSTOMER2 10.21.0.1
```

---

## 7. Notes
- All forwarding is based on static routes and explicit policies
- Route-maps can be extended to match source/destination ports, DSCP, or interfaces
- This method does not require redistribution or route leaking protocols