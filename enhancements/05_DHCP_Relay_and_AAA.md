# 06 - DHCP Relay and AAA Login Simulation (All-Router)

This enhancement enables DHCP relay and simulates AAA login using a RADIUS server running on a router. No external Linux servers are used. All services are hosted within the network using CSR1000v devices only.

### Goal

- Allow BRANCH1 and BRANCH2 hosts to receive DHCP addresses from a central DHCP server at CSW-HQ.

### Topology Roles

| Device       | Role              | Interface    | IP             |
|--------------|-------------------|--------------|----------------|
| CSW-HQ       | DHCP server       | Loopback0    | 10.100.0.1     |
| BRANCH1-RTR  | DHCP relay agent  | Gi1 (VRF)    | 10.21.0.1      |
| BRANCH2-RTR  | DHCP relay agent  | Gi1 (VRF)    | 10.31.0.1      |

---

## 1. DHCP Relay

### CSW-HQ DHCP Server

```bash
ip dhcp excluded-address 10.21.0.1 10.21.0.10
ip dhcp excluded-address 10.31.0.1 10.31.0.10

ip dhcp pool BRANCH1
   network 10.21.0.0 255.255.255.0
   default-router 10.21.0.1
   dns-server 1.1.1.1

ip dhcp pool BRANCH2
   network 10.31.0.0 255.255.255.0
   default-router 10.31.0.1
   dns-server 1.1.1.1
```

---

## 2. BRANCH DHCP Relay

### BRANCH1-RTR (VRF CUSTOMER1)
```bash
interface GigabitEthernet1-2
 ip vrf forwarding CUSTOMER1
 ip helper-address 10.100.0.1
```

### BRANCH2-RTR (VRF CUSTOMER2)
```bash
interface GigabitEthernet1-2
 ip vrf forwarding CUSTOMER2
 ip helper-address 10.100.0.1
```

### 3. AAA Login (RADIUS Simulation)

A router (R-EDGE) is configured as a fake RADIUS server. The CSW-HQ and branch routers authenticate CLI login requests via this RADIUS service.

### RADIUS Server (R-EDGE)

```bash
aaa new-model
radius-server host 10.100.0.2 auth-port 1812 acct-port 1813 key project3
!
aaa group server radius RADIUS-GRP
 server 10.100.0.2 auth-port 1812 acct-port 1813
!
aaa authentication login default group RADIUS-GRP local
!
username golrang privilege 15 secret project3
```

Then, simulate the RADIUS server:
```bash
aaa new-model
aaa authentication login RADIUS-SERVER local
username golrang password project3
!
line vty 0 4
 login authentication RADIUS-SERVER
```

### Client Routers (CSW-HQ, BR1, BR2)

This tries RADIUS first, then local if the server is unreachable.

```bash
aaa new-model
!
radius-server host 10.100.0.2 key project3
!
aaa authentication login default group radius local
```

---

## 3. Testing

### DHCP

From BRANCH1/BRANCH2:

```bash
clear ip dhcp binding *
debug ip dhcp server packet
```

Check leases on CSW-HQ:

```bash
show ip dhcp binding
```

### AAA

SSH into a router:

```bash
ssh -l radiusadmin 10.10.0.2
```

Should authenticate via RADIUS. If RADIUS is down, fallback to `local`.

---

## 4. Notes
- All services are simulated with routers
- DHCP is scoped per VRF using relay and subnet targeting
- RADIUS server is simulated using local username + VTY policy
