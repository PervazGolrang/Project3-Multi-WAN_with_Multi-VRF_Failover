# Step 04 - Branch Routing (BRANCH1 and BRANCH2)

This step configures BRANCH1 and BRANCH2 routers with isolated VRFs, routed uplinks to CSW-HQ, and local LAN connectivity. Each branch operates as a separate routing domain using VRF-Lite.

---

## 1. Devices Involved

- BRANCH1-RTR (CSR1000v)
- BRANCH2-RTR (CSR1000v)

---

## 2. VRF Definitions

### BRANCH1-RTR
```bash
ip vrf CUSTOMER1
 rd 65100:1
```

---

## BRANCH2-RTR
```bash
ip vrf CUSTOMER2
 rd 65100:2
```

---

## 3. Interface Assignments

### BRANCH1-RTR

| Interface | Role          | IP Address   | VRF       |
| --------- | ------------- | ------------ | --------- |
| Gi1       | To CSW-HQ     | 10.20.0.2/30 | CUSTOMER1 |
| Gi2       | LAN Interface | 10.21.0.1/24 | CUSTOMER1 |

```bash
interface GigabitEthernet1
 description "To CSW-HQ"
 ip vrf forwarding CUSTOMER1
 ip address 10.20.0.2 255.255.255.252
 no shutdown
!
interface GigabitEthernet2
 description "LAN"
 ip vrf forwarding CUSTOMER1
 ip address 10.21.0.1 255.255.255.0
 no shutdown
```

---

### BRANCH2-RTR

| Interface | Role          | IP Address   | VRF       |
| --------- | ------------- | ------------ | --------- |
| Gi0/0     | To CSW-HQ     | 10.30.0.2/30 | CUSTOMER2 |
| Gi0/1     | LAN Interface | 10.31.0.1/24 | CUSTOMER2 |

```bash
interface GigabitEthernet1
 description "To CSW-HQ"
 ip vrf forwarding CUSTOMER2
 ip address 10.30.0.2 255.255.255.252
 no shutdown
!
interface GigabitEthernet2
 description "LAN"
 ip vrf forwarding CUSTOMER2
 ip address 10.31.0.1 255.255.255.0
 no shutdown
```

## 4. Static Routing

Each branch has a default route pointing to CSW-HQ and a directly connected LAN:

### BRANCH1-RTR
```bash
ip route vrf CUSTOMER1 0.0.0.0 0.0.0.0 10.20.0.1
```

---

### BRANCH2-RTR
```bash
ip route vrf CUSTOMER2 0.0.0.0 0.0.0.0 10.30.0.1
```

---

## 5. Notes
- LANs (10.21.0.0/24 and 10.31.0.0/24) can be used for simulated PC testing.
- Each branch is fully isolated in its own VRF.
- Branches forward Internet-bound traffic toward HQ via CSW-HQ and ASA.