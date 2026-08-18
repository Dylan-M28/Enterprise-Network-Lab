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
