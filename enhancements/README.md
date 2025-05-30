# Enhancements - README

This folder contains optional, modular feature expansions for the core VRF-Lite WAN project. Each enhancement is documented in its own file and can be applied independently after the base topology is fully functional.

These features simulate real-world enterprise upgrades in security, routing control, and IPv6 integration.

---

## Enhancements

| File                     | Feature                   | Description                                                |
|--------------------------|---------------------------|------------------------------------------------------------|
| 01_IPSec_IKEv2.md        | IPSec + IKEv2             | Secure encrypted tunnels between HQ and branch sites       |
| 02_IPv6_DualStack.md     | IPv6 Dual Stack           | Adds IPv6 addressing and routing across all sites          |
| 03_CoPP_Config.md        | Control Plane Policing    | Protects router CPU from unnecessary or malicious traffic  |
| 04_PBR_and_Leaking.md    | PBR + Route Leaking       | Controlled VRF-to-VRF communication and traffic steering   |
| 05_DHCP_Relay_and_AAA.md | DHCP Relay and RADIUS AAA | Simulates enterprise access control and address management |

---

## Integration Guidelines

- Apply each enhancement incrementally and test before continuing
- All configurations follow the same IP and VRF structure defined in the core documentation

---

## Known Platform Limitations

- `AES-GCM` is **not supported** on CSR1000v and is replaced with `AES-CBC-256`
- Linux-based services (DHCP, RADIUS, syslog) are excluded and simulated using routers only
