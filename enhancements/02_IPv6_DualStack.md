# 02 - IPv6 Dual Stack Deployment

This enhancement introduces IPv6 dual stack across CSW-HQ, BRANCH1-RTR, and BRANCH2-RTR. All interfaces operate with both IPv4 and IPv6. No dynamic routing protocols are used. All IPv6 forwarding is based on static routes.

---

## 1. Addressing Plan

### WAN Interfaces

| Link               | Subnet                   | HQ IPv6 Address           | Branch IPv6 Address       |
|--------------------|--------------------------|---------------------------|----------------------------|
| HQ ↔ BRANCH1       | 2001:db8:10:1::/64       | 2001:db8:10:1::1/64       | 2001:db8:10:1::2/64        |
| HQ ↔ BRANCH2       | 2001:db8:10:2::/64       | 2001:db8:10:2::1/64       | 2001:db8:10:2::2/64        |

### LAN Interfaces

| Branch        | LAN Subnet           | Branch LAN IPv6 Gateway     |
|---------------|-----------------------|------------------------------|
| BRANCH1-RTR   | 2001:db8:21::/64      | 2001:db8:21::1/64            |
| BRANCH2-RTR   | 2001:db8:31::/64      | 2001:db8:31::1/64            |

---

## 2. Interface Configuration

### CSW-HQ

```bash
interface GigabitEthernet2
 ipv6 unicast-routing
 ipv6 address 2001:db8:10:1::1/64

interface GigabitEthernet3
 ipv6 unicast-routing
 ipv6 address 2001:db8:10:2::1/64
```

### BRANCH1-RTR
```bash
interface GigabitEthernet1
 ipv6 unicast-routing
 ipv6 address 2001:db8:10:1::2/64

interface GigabitEthernet2
 ipv6 unicast-routing
 ipv6 address 2001:db8:21::1/64
```

### BRANCH2-RTR
```bash
interface GigabitEthernet1
 ipv6 unicast-routing
 ipv6 address 2001:db8:10:2::2/64

interface GigabitEthernet2
 ipv6 unicast-routing
 ipv6 address 2001:db8:31::1/64
 ```

 ## 3. IPv6 Static Routing

### CSW-HQ
```bash
ipv6 route 2001:db8:21::/64 2001:db8:10:1::2
ipv6 route 2001:db8:31::/64 2001:db8:10:2::2
```

### BRANCH1-RTR
```bash
ipv6 route ::/0 2001:db8:10:1::1
```

### BRANCH2-RTR
```bash
ipv6 route ::/0 2001:db8:10:2::1
```

---

### 4. Verification

Check connectivity from branch LAN gateway to HQ and between routers:

```bash
ping ipv6 2001:db8:10:1::1 source 2001:db8:10:1::2
ping ipv6 2001:db8:21::1 source 2001:db8:10:1::1
```

Inspect routing table:

```bash
show ipv6 route
```

Confirm interface status:

```bash
show ipv6 interface brief
```

---

### 5. Notes
- This configuration uses only static routes for IPv6; no OSPFv3 or BGPv6 is deployed.
- IPv6 unicast forwarding must be globally enabled on all devices with `ipv6 unicast-routing`
- All interfaces are dual stack and capable of forwarding both IPv4 and IPv6.
- NAT64 or DNS64 is not used in this enhancement.
