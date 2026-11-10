# Huawei Static VXLAN | Leaf 1 (VTEP)

[![Huawei](https://img.shields.io/badge/Huawei-CloudEngine-ED1C24?style=flat-square)](../../README.md) ![Lab](https://img.shields.io/badge/Static%20VXLAN-basic-15803D?style=flat-square) ![Example](https://img.shields.io/badge/Status-Lab%20Template-64748B?style=flat-square)

[Repository home](../../README.md) · [Configuration index](../README.md) · [Full walkthrough](../../docs/03-huawei-basic-static.md) · [Troubleshooting](../../docs/08-basic-troubleshooting.md)

> [!IMPORTANT]
> **Educational configuration template only.** These commands have **not** been verified on physical switches or vendor emulators. Check your model, OS release, feature support, licensing, MTU and interface names; save a backup before changing any device.

## Overview

Huawei Leaf 1 static VXLAN VTEP template. Extends tagged VLAN 100 over a routed point-to-point underlay using a manually defined remote VTEP.

| Parameter | Example value |
|:--|:--|
| Platform | Huawei CloudEngine VRP (supported VXLAN/EVPN syntax and staged commit required) |
| Lab scenario | Static VXLAN |
| Device role | Leaf 1 (VTEP) |
| Hostname | `HW-LEAF-1` |
| Loopback / router ID | `10.255.0.11/32` |
| Underlay interface IP(s) | `10.0.12.1/30` |
| EVPN / remote VTEP peer(s) | `10.255.0.12` |
| Tenant segments | VLAN 100 ↔ VNI 10100 |

## Prerequisites

- Use a **lab or maintenance window** and confirm the hardware/software supports this exact command family.
- Match the sample physical interface IDs to the actual device, and confirm underlay IP addressing is unique.
- Confirm end-to-end MTU accommodates VXLAN encapsulation; jumbo MTU configuration on one interface does not prove the full path supports it.
- Prepare VLAN 100 as tagged on the attached host or access switch; ensure the opposite VTEP loopback is reachable.
- This leaf has an NVE tunnel endpoint. Configure the matching opposite leaf and test host-side VLAN tags.

## Full configuration

The following is the **complete original configuration**, preserved from the supplied `.cfg` file and formatted for GitHub readability. Copy only the CLI block, not the surrounding Markdown.

```text
system-view
sysname HW-LEAF-1
interface LoopBack0
 ip address 10.255.0.11 255.255.255.255
 quit
interface 10GE1/0/1
 description UNDERLAY
 undo portswitch
 ip address 10.0.12.1 255.255.255.252
 mtu 9216
 undo shutdown
 quit
ip route-static 10.255.0.12 255.255.255.255 10.0.12.2
bridge-domain 100
 vxlan vni 10100
 quit
interface 10GE1/0/2
 description TAGGED-HOST-OR-ACCESS-SWITCH
 undo shutdown
 quit
interface 10GE1/0/2.100 mode l2
 encapsulation dot1q vid 100
 bridge-domain 100
 quit
interface Nve1
 source 10.255.0.11
 vni 10100 head-end peer-list 10.255.0.12
 quit
commit
return
```

## Verification commands

Run these from the appropriate device context after applying the configuration (the operational command syntax can vary by release).

```text
display ip routing-table 10.255.0.12
ping -a 10.255.0.11 10.255.0.12
display vxlan vni
display vxlan tunnel
display mac-address bridge-domain 100
```

After verifying sourced VTEP reachability, send traffic between hosts on VLAN 100 at each leaf. Confirm VLAN/VNI mapping and learned MAC entries.

## Save and operational notes

- Review all CLI errors and verify forwarding **before** saving to startup configuration.
- Use `commit` if your VRP release uses the staged configuration model, then run `save` as documented for the platform.
- Avoid pasting these examples into production as-is. No customer IPs, authentication, security ACLs or redundancy policies are provided.
- Follow the [validation checklist](../../docs/11-operations-validation.md) and [platform guidance](../../docs/02-platform-topology.md).

---

<sub>Maintained by **Amir Hosan Jesan (Netronixbd)** · VXLAN [Huawei & Cisco] · Configuration reference for learning and lab validation.</sub>
