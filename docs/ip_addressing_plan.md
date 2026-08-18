# Phase 2: IP Addressing & VLSM Design Plan

## 1. Parent Network Assignment

* **Parent Supernet:** `10.10.0.0/16`
* **Subnet Mask:** `255.255.0.0`
* **Scope:** Private RFC 1918 IPv4 space allocated for internal user VLANs, management networks, and Layer 3 infrastructure transit links.

---

## 2. Design Conventions & IP Allocation Rules

To ensure predictable deployment and operation, the following rules govern IP address assignments across all subnets:

1. **Default Gateways:** The **first usable IP address** (`.1` offset of the usable range) in each user subnet is reserved for the Switch Virtual Interface (SVI) gateway on the Collapsed Core layer.
2. **Dynamic Host Pools (DHCP):** Host addresses are handed out dynamically starting from the mid-range of each subnet upward.
3. **Static Allocations:** Lower host IP addresses (e.g., `.2` through `.10`) are reserved for static infrastructure (printers, servers, APs).
4. **VLSM Allocation Order:** Subnets are carved sequentially from largest required block size down to smallest block size to align with binary subnet boundaries and eliminate address fragmentation.

---

## 3. Master VLSM IP Addressing Table

| Subnet / VLAN Name | Planned Hosts (+30%) | CIDR Prefix | Subnet Mask | Network ID | Usable IP Range | Broadcast Address | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **VLAN 10 - Engineering & DevOps** | 39 | `/26` | `255.255.255.192` | `10.10.0.0` | `10.10.0.1` – `10.10.0.62` | `10.10.0.63` | `10.10.0.1` |
| **VLAN 30 - Sales & Admin** | 39 | `/26` | `255.255.255.192` | `10.10.0.64` | `10.10.0.65` – `10.10.0.126` | `10.10.0.127` | `10.10.0.65` |
| **VLAN 100 - Guest Wi-Fi** | 39 | `/26` | `255.255.255.192` | `10.10.0.128` | `10.10.0.129` – `10.10.0.190` | `10.10.0.191` | `10.10.0.129` |
| **VLAN 20 - Finance & HR** | 26 | `/27` | `255.255.255.224` | `10.10.0.192` | `10.10.0.193` – `10.10.0.222` | `10.10.0.223` | `10.10.0.193` |
| **VLAN 40 - Executive Team** | 13 | `/28` | `255.255.255.240` | `10.10.0.224` | `10.10.0.225` – `10.10.0.238` | `10.10.0.239` | `10.10.0.225` |
| **VLAN 50 - IT / Central Services** | 13 | `/28` | `255.255.255.240` | `10.10.0.240` | `10.10.0.241` – `10.10.0.254` | `10.10.0.255` | `10.10.0.241` |
| **VLAN 200 - Network Management** | 7 | `/28` | `255.255.255.240` | `10.10.1.0` | `10.10.1.1` – `10.10.1.14` | `10.10.1.15` | `10.10.1.1` |
| **Transit Link 1 (DS1 <-> R1)** | 2 | `/30` | `255.255.255.252` | `10.10.1.16` | `10.10.1.17` – `10.10.1.18` | `10.10.1.19` | N/A |
| **Transit Link 2 (DS2 <-> R1)** | 2 | `/30` | `255.255.255.252` | `10.10.1.20` | `10.10.1.21` – `10.10.1.22` | `10.10.1.23` | N/A |

---

## 4. Point-to-Point Infrastructure Transit Links

Infrastructure connectivity between the Collapsed Core switches (`DS1`, `DS2`) and the Edge Router (`R1`) uses dedicated `/30` point-to-point subnets to isolate routing domains and terminate broadcast traffic at the core layer.

### Transit Link 1: `DS1` to `R1`
* **Network ID:** `10.10.1.16/30`
* **`DS1` Interface (`Gi1/0/3`):** `10.10.1.17`
* **`R1` Interface (`Gi0/1`):** `10.10.1.18`
* **Broadcast Address:** `10.10.1.19`

### Transit Link 2: `DS2` to `R1`
* **Network ID:** `10.10.1.20/30`
* **`DS2` Interface (`Gi1/0/3`):** `10.10.1.21`
* **`R1` Interface (`Gi0/2`):** `10.10.1.22`
* **Broadcast Address:** `10.10.1.23`

---

## 5. Engineering Rationale & VLSM Boundary Logic

* **No Address Waste:** By using Variable Length Subnet Masking (VLSM) instead of classful subnetting, the network saves over 90% of address space in the assigned `10.10.0.0/16` parent block for future enterprise expansion.
* **Binary Boundary Alignment:** Placing `/26` subnets (block size 64), `/27` subnets (block size 32), and `/28` subnets (block size 16) in descending order guarantees that every network address lands cleanly on its binary multiple boundary (`.0`, `.64`, `.128`, `.192`, `.224`, `.240`).
* **Cross-Octet Continuity:** VLAN 200 seamlessly transitions across the 4th octet boundary into `10.10.1.0/28` without breaking contiguous allocation.
