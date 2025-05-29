# VRF Table - Project 3

This document defines the virtual routing and forwarding (VRF) instances configured in the network. Each VRF provides logical separation between remote sites, allowing independent routing domains for each branch while sharing a common infrastructure at the HQ core.

---

## VRF Overview

| VRF Name   | Purpose                       | Defined On Devices   |
|------------|-------------------------------|----------------------|
| CUSTOMER1  | Routing isolation for BRANCH1 | CSW-HQ, BRANCH1-RTR  |
| CUSTOMER2  | Routing isolation for BRANCH2 | CSW-HQ, BRANCH2-RTR  | 

---

## CSW-HQ VRF Interfaces

| Interface | VRF        | IP Address        | Description                        |
|-----------|------------|-------------------|------------------------------------|
| Gi1       | CUSTOMER1  | 10.20.0.1/30      | Point-to-point link to BRANCH1-RTR |
| Gi2       | CUSTOMER2  | 10.30.0.1/30      | Point-to-point link to BRANCH2-RTR |

---

## BRANCH1-RTR VRF Interfaces

| Interface | VRF        | IP Address         | Description              |
|-----------|------------|--------------------|--------------------------|
| Gi1       | CUSTOMER1  | 10.20.0.2/30       | Link to CSW-HQ           |
| Gi2       | CUSTOMER1  | 10.21.0.1/24       | Local LAN subnet         |

---

## BRANCH2-RTR VRF Interfaces

| Interface | VRF        | IP Address         | Description              |
|-----------|------------|--------------------|--------------------------|
| Gi1       | CUSTOMER2  | 10.30.0.2/30       | Link to CSW-HQ           |
| Gi2       | CUSTOMER2  | 10.31.0.1/24       | Local LAN subnet         |

---

## Routing Details

| VRF        | Routing Method | Advertised Subnets                 |
|------------|----------------|------------------------------------|
| CUSTOMER1  | Static or OSPF | 10.20.0.0/30, 10.21.0.0/24         |
| CUSTOMER2  | Static or OSPF | 10.30.0.0/30, 10.31.0.0/24         |

---

## Inter-VRF Communication

By default, no communication occurs between VRFs. Selective route leaking and PBR will be applied if following the `enhancements/` folder, for controlled access between branches.