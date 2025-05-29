# Step 02 - ASA Configuration (Routed Mode with NAT and ACLs)

This step configures the ASA firewall in routed mode. It connects R-EDGE (outside) to CSW-HQ (inside), applies dynamic PAT, and sets up basic security policies for internal-to-external traffic.

---

## 1. ASA Interface Plan

| ASA Interface | Role     | Connected To | IP Address       | Security Level |
|---------------|----------|--------------|------------------|----------------|
| Gi0/1         | outside  | R-EDGE       | 192.168.10.2/30  | 0              |
| Gi0/0         | inside   | CSW-HQ       | 10.10.0.1/30     | 100            |

---

## 2. ASA Base Configuration

```bash
hostname ASA
no enable password
enable password golrang123
no names
!
interface GigabitEthernet0/1
 description "To R-EDGE"
 nameif outside
 security-level 0
 ip address 192.168.10.2 255.255.255.252
 no shutdown
!
interface GigabitEthernet0/0
 description "To CSW-HQ"
 nameif inside
 security-level 100
 ip address 10.10.0.1 255.255.255.252
 no shutdown
!
route outside 0.0.0.0 0.0.0.0 192.168.10.1
```

---

## 3. NAT/PAT Configuration

Dynamic PAT for all internal traffic using ASA’s outside interface IP:

```bash
object network INSIDE-NET
 subnet 10.10.0.0 255.255.0.0
 nat (inside,outside) dynamic interface
```

---

## 4. Basic ACLs

Allow outbound traffic from inside:

```bash
access-list OUTBOUND extended permit ip any any
access-group OUTBOUND in interface inside
```

---

## 5. Inspection Policy (Default)

Ensuring the default inspection policy is beng applied:

```bash
policy-map global_policy
 class inspection_default
  inspect icmp
  inspect ftp
  inspect http
  inspect https
  inspect dns
!
service-policy global_policy global

```

---

## 6. Notes
- The ASA is not doing routing beyond inside-outside zones.
- PAT allows multiple internal hosts to share one public IP (192.0.2.1 via R-EDGE).
