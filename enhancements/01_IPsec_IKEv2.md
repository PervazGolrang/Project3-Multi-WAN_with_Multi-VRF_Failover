# 01 - IPSec + IKEv2 + GRE Tunnel

This enhancement sets up a tunnel between CSW-HQ and BRANCH1-RTR, secured with IPSec using IKEv2.

| Tunnel       | Local Device | Local IP     | Remote Device  | Remote IP    | Tunnel IPs          |
|--------------|--------------|--------------|----------------|--------------|---------------------|
| Tunnel100    | CSW-HQ       | 10.10.0.2    | BRANCH1-RTR    | 10.20.0.2    | 172.16.100.1 ↔ .2   |
| Tunnel200    | CSW-HQ       | 10.10.0.2    | BRANCH2-RTR    | 10.30.0.2    | 172.16.200.1 ↔ .2   |

---

## 1. IPSec and IKEv2 Common Setup (ALL DEVICES)

```bash
crypto ikev2 proposal IKEV2-PROPOSAL
 encryption aes-cbc-256
 integrity sha256
 group 19

crypto ikev2 policy IKEV2-POLICY
 proposal IKEV2-PROPOSAL

crypto ipsec transform-set IPSEC-TUNNEL esp-aes 256 esp-sha256-hmac
 mode tunnel
 ```

### CSW-HQ
```bash
crypto ikev2 keyring IKEV2-KEYRING
 peer BR1
  address 10.20.0.2
  pre-shared-key GolKey
 peer BR2
  address 10.30.0.2
  pre-shared-key GolKey
!
crypto ikev2 profile IKEV2-PROFILE
 match identity remote address 10.20.0.2 255.255.255.255
 match identity remote address 10.30.0.2 255.255.255.255
 identity local address 10.10.0.2
 authentication remote pre-share
 authentication local pre-share
 keyring local IKEV2-KEYRING
!
crypto ipsec profile IPSEC-TUNNEL-PROFILE
 set transform-set IPSEC-TUNNEL
 set ikev2-profile IKEV2-PROFILE
!
interface Tunnel100
 ip vrf forwarding CUSTOMER1
 ip address 172.16.100.1 255.255.255.252
 tunnel source 10.10.0.2
 tunnel destination 10.20.0.2
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile IPSEC-TUNNEL-PROFILE
!
interface Tunnel200
 ip vrf forwarding CUSTOMER2
 ip address 172.16.200.1 255.255.255.252
 tunnel source 10.10.0.2
 tunnel destination 10.30.0.2
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile IPSEC-TUNNEL-PROFILE
```

### BRANCH1-RTR
```bash
crypto ikev2 keyring IKEV2-KEYRING
 peer HQ
  address 10.10.0.2
  pre-shared-key GolKey
!
crypto ikev2 profile IKEV2-PROFILE
 match identity remote address 10.10.0.2 255.255.255.255
 identity local address 10.20.0.2
 authentication remote pre-share
 authentication local pre-share
 keyring local IKEV2-KEYRING
!
crypto ipsec profile IPSEC-TUNNEL-PROFILE
 set transform-set IPSEC-TUNNEL
 set ikev2-profile IKEV2-PROFILE
!
interface Tunnel100
 ip vrf forwarding CUSTOMER1
 ip address 172.16.100.2 255.255.255.252
 tunnel source 10.20.0.2
 tunnel destination 10.10.0.2
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile IPSEC-TUNNEL-PROFILE
```

### BRANCH2-RTR
```bash
crypto ikev2 keyring IKEV2-KEYRING
 peer HQ
  address 10.10.0.2
  pre-shared-key GolKey
!
crypto ikev2 profile IKEV2-PROFILE
 match identity remote address 10.10.0.2 255.255.255.255
 identity local address 10.30.0.2
 authentication remote pre-share
 authentication local pre-share
 keyring local IKEV2-KEYRING
!
crypto ipsec profile IPSEC-TUNNEL-PROFILE
 set transform-set IPSEC-TUNNEL
 set ikev2-profile IKEV2-PROFILE
!
interface Tunnel200
 ip vrf forwarding CUSTOMER2
 ip address 172.16.200.2 255.255.255.252
 tunnel source 10.30.0.2
 tunnel destination 10.10.0.2
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile IPSEC-TUNNEL-PROFILE
```

### 2. Static Routing Over Tunnels

### CSW-HQ

```bash
ip route vrf CUSTOMER1 10.21.0.0 255.255.255.0 172.16.100.2
ip route vrf CUSTOMER2 10.31.0.0 255.255.255.0 172.16.200.2
```

### BRANCH1-RTR

```bash
ip route vrf CUSTOMER1 10.10.0.0 255.255.255.0 172.16.100.1
```

### BRANCH2-RTR

```bash
ip route vrf CUSTOMER2 10.10.0.0 255.255.255.0 172.16.200.1
```

---

## 3. Verification
```bash
show crypto ikev2 sa
show crypto ipsec sa
show interface Tunnel100
show interface Tunnel200
ping vrf CUSTOMER1 10.10.0.1 source 10.21.0.1
ping vrf CUSTOMER2 10.10.0.1 source 10.31.0.1
```

---

## 4. Notes
- `AES-GCM` is not used due to platform limitations on CSR1000v.
- Ensure that routing between tunnel source/destination is reachable before testing IKEv2.
- Both IPSec tunnels are fully configured end-to-ends
- VRF separation is maintained at all times