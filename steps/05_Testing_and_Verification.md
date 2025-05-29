# Step 05 - Testing and Verification

This step verifies the functionality of the full network: reachability, routing, NAT, failover, and VRF isolation. Testing is conducted from branch LANs and HQ to confirm end-to-end behavior.

---

## 1. Test Preparation

### Devices to Use:
- BRANCH1-RTR (loopbacks or dummy PC IP)
- BRANCH2-RTR
- R-EDGE (for IP SLA)
- ASA (for NAT and logs)

Ensure:
- All interfaces are up
- IP addressing matches the IP_Plan.md
- Static routes and NAT are configured as per previous steps

---

## 2. Ping Tests

### From BRANCH1

```bash
ping vrf CUSTOMER1 10.21.0.1        ! Self LAN
ping vrf CUSTOMER1 10.20.0.1        ! CSW-HQ link
ping vrf CUSTOMER1 10.10.0.1        ! ASA inside
ping vrf CUSTOMER1 8.8.8.8          ! Internet via NAT
```

---

### From BRANCH1

```bash
ping vrf CUSTOMER2 10.31.0.1
ping vrf CUSTOMER2 10.30.0.1
ping vrf CUSTOMER2 10.10.0.1
ping vrf CUSTOMER2 8.8.8.8
```

---

## 3. ASA NAT Verification

### From ASA:

```bash
show xlate
show conn
```

---

## 4. VRF Isolation Test

### From ASA:

Ensuring that BRANCH1 and BRANCH2 cannot directly reach each other:

```bash
ping vrf CUSTOMER1 10.31.0.1        # Should fail
ping vrf CUSTOMER2 10.21.0.1        # Should fail
```

---

## 5. IP SLA Failover Test

From R-EDGE:
```bash
show ip sla statistics
show track 1
```

Then simulate ISP1 failure:
```bash
shutdown interface GigabitEthernet1   # R-EDGE to ISP1
```

Confirm that the default route should pooint to ISP2 (203.0.113.1):
```bash
show ip route
```

Restore ISP1:
```bash
no shutdown interface GigabitEthernet1
```

---

## 6. Notes
- This concludes the core implementation of the lab. Additional features and network enhancements are documented in the `enhancements/` directory for further development.