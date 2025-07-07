# 03 - CoPP (Control Plane Policing via Per-Interface Method)

This enhancement applies CoPP-like protection on **R-EDGE** using per-interface access control to limit unauthorized or malicious traffic targeting router services. True CoPP using `control-plane` is not available due to platform limitations.

All filtering is implemented using inbound `service-policy` on interface level with class-maps matching specific control traffic types (ICMP, SSH, etc.).

---

## 1. Device Limitations

- `control-plane` configuration is not supported
- `class-map` and `policy-map` must be applied per physical interface
- Matching and policing is limited to supported features on the platform

---

## 2. CoPP Policy for Interfaces

### CoPP Goals:
- Allow ICMP (ping, traceroute)
- Allow SSH from trusted sources
- Drop unknown control traffic
- Apply basic rate-limit to prevent abuse

---

## 3. Class-Map Definitions

```bash
class-map match-any COPP-ICMP
 match protocol icmp

class-map match-any COPP-SSH
 match protocol ssh
```

## 4. Policy-Map Configuration

```bash
policy-map PER_INTERFACE_COPP
 class COPP-ICMP
  police 64000 8000 conform-action transmit exceed-action drop
!
 class COPP-SSH
  police 32000 4000 conform-action transmit exceed-action drop
```

---

## 5. Interface Configuration

Appling policy inbound on interfaces where external traffic enters:
```bash
interface range GigabitEthernet1-3
 service-policy input PER_INTERFACE_COPP
```

---

## 6. Verification
- Check if the policy is attached
- Check counters for class matches and drops

```bash
show policy-map interface GigabitEthernet1
!
show policy-map interface | include COPP
```

---

### 7. Notes
- This is a per-interface workaround, not true CoPP. CSR1000v does not support `control-plane` configuration mode.
- Cannot protect routing protocols (OSPF/BGP) if used, but that is not necessary in this lab
- Must be manually applied **only** on R-EDGE