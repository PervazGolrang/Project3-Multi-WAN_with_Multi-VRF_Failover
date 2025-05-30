# Step 01 - ISP Routers and R-EDGE Configuration

This step sets up both ISP routers and the enterprise edge router (R-EDGE). It includes BGP or static routing to simulate Internet connectivity, as well as IP SLA and tracking for failover between dual ISPs.

---

## 1. Devices Involved

- **ISP1** (CSR1000v)
- **ISP2** (CSR1000v)
- **R-EDGE** (CSR1000v)

---

## 2. Interface Overview

| Link                | Subnet           | ISP Side IP      | R-EDGE Side IP   |
|---------------------|------------------|------------------|------------------|
| ISP1 ↔ R-EDGE       | 198.51.100.0/30  | 198.51.100.1     | 198.51.100.2     |
| ISP2 ↔ R-EDGE       | 203.0.113.0/30   | 203.0.113.1      | 203.0.113.2      |
| R-EDGE ↔ ASA        | 192.168.10.0/30  | –                | 192.168.10.1     |

---

## 3. ISP Configuration (example – static or BGP)

### ISP1
```bash
hostname ISP1
interface GigabitEthernet1
 ip address 198.51.100.1 255.255.255.252
 description "To R-EDGE"
 no shutdown
!
ip route 192.168.10.0 255.255.255.252 198.51.100.2
!
interface GigabitEthernet2
 ip address DHCP
 description "To Internet"
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 198.168.40.1           # My personal SOHO router Gateway
```

### ISP2

```bash
hostname ISP2
interface GigabitEthernet1
 ip address 203.0.113.1 255.255.255.252
 description "To R-EDGE"
 no shutdown
!
ip route 192.168.10.0 255.255.255.252 203.0.113.2
!
interface GigabitEthernet2
 ip address DHCP
 description "To Internet"
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 198.168.40.1           # My personal SOHO router Gateway
```

### R-EDGE

```bash
hostname R-EDGE
interface loopback0
 ip address 10.255.0.1 255.255.255.255
!
interface GigabitEthernet1
 description "To ISP1"
 ip address 192.51.100.2 255.255.255.252
 no shutdown
!
interface GigabitEthernet2
 description "To ISP2"
 ip address 203.0.113.2 255.255.255.252
 no shutdown
!
interface GigabitEthernet3
 description "To ASA Outside"                   # Outside-facing link, not part of internal network.
 ip address 192.168.10.1 255.255.255.252
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 192.51.100.1 track 1
ip route 0.0.0.0 0.0.0.0 203.0.113.1 10
!
ip sla 1
 icmp-echo 192.51.100.1 source-ip 192.51.100.2
 frequency 5                                    # Send ICMP prove every 5 seconds
 timeout 2500                                   # Declare probe failed if no reply within 2500ms
 threshold 1500                                  # Mark probe as degraded if reply exceeds 1500ms
!
ip sla schedule 1 life forever start-time now
!
track 1 ip sla 1 reachability
 delay down 10                                 # Route is withdrawn after 10 consecutive seconds of failure
 delay up 20                                   # Route is restored only after 30 consecutive seconds of success
!
```

---

## 4. Notes
- The DHCP next-hop is highly dependent on your router, my personal router is 192.168.40.0/24
- Static routing is only used here for temporary simplicity. 
- In the `enhancements/` the static routes will be replaced with BGP (eBGP between R-EDGE and ISPs).
- IP SLA + tracking ensures automatic failover if ISP1 is unreachable.
- IP SLA is marked with notes so I remember the differences between the commands.