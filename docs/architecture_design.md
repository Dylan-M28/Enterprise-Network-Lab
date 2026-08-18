## Objective

Design the physical and logical architecture for a simulated ~100-user
enterprise network.

The design will provide:

- Departmental network segmentation
- Redundant network paths
- A collapsed core architecture
- Dedicated guest and management networks
- A foundation for routing, security, and troubleshooting

---

## Network Architecture

The network uses a **two-tier collapsed-core architecture**.

Instead of having separate Core, Distribution, and Access layers, the
Core and Distribution functions are combined into two multilayer
switches.

### Devices

| Role | Device | Model |
|---|---|---|
| Edge Router | R1 | Cisco 2911 |
| Collapsed Core | DS1 | Cisco Catalyst 3650 |
| Collapsed Core | DS2 | Cisco Catalyst 3650 |
| Access Switch | AS1 | Cisco Catalyst 2960-24TT |
| Access Switch | AS2 | Cisco Catalyst 2960-24TT |

---

## Physical Topology

                         R1
                       /    \
                      /      \
                    DS1      DS2
                   /  \      /  \
                  /    \    /    \
                AS1      \  /      AS2

                    DS1 ===== DS2

## Physical Connections
Access Layer
AS1 Gi0/1 → DS1 Gi1/0/1
AS1 Gi0/2 → DS2 Gi1/0/1
AS2 Gi0/1 → DS1 Gi1/0/2
AS2 Gi0/2 → DS2 Gi1/0/2

Each access switch therefore has redundant uplinks to the collapsed-core layer.

Edge Router
DS1 Gi1/0/3 → R1 Gi0/1
DS2 Gi1/0/3 → R1 Gi0/2

Both collapsed-core switches have a physical connection to the edge router.

Core Interconnect
DS1 Gi1/0/23 → DS2 Gi1/0/23
DS1 Gi1/0/24 → DS2 Gi1/0/24

These two physical links will later be configured as an EtherChannel.

## VLAN Architecture

The network will be divided into seven logical networks:

VLAN 10 — Engineering & DevOps
VLAN 20 — Finance & HR
VLAN 30 — Sales & Admin
VLAN 40 — Executive
VLAN 50 — IT / Services
VLAN 100 — Guest
VLAN 200 — Network Management
Design Rationale

VLANs separate departments into different broadcast domains and provide logical boundaries for future routing and security policies.

The Guest VLAN will eventually be restricted to Internet access and prevented from accessing internal corporate networks.

The Management VLAN provides a dedicated logical network for administrative access to network infrastructure.
