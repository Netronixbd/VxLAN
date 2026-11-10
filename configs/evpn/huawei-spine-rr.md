# Huawei BGP EVPN | Spine / Route Reflector

[![Huawei](https://img.shields.io/badge/Huawei-CloudEngine-ED1C24?style=flat-square)](../../README.md) ![Lab](https://img.shields.io/badge/BGP%20EVPN-advanced-7936D2?style=flat-square) ![Example](https://img.shields.io/badge/Status-Lab%20Template-64748B?style=flat-square)

[Repository home](../../README.md) · [Configuration index](../README.md) · [Full walkthrough](../../docs/05-huawei-advanced-evpn.md) · [Troubleshooting](../../docs/08-basic-troubleshooting.md)

> [!IMPORTANT]
> **Educational configuration template only.** These commands have **not** been verified on physical switches or vendor emulators. Check your model, OS release, feature support, licensing, MTU and interface names; save a backup before changing any device.

## Overview

Route reflector template for a three-node Huawei EVPN laboratory. This node exchanges EVPN reachability between Leaf 1 and Leaf 2 using iBGP; it forwards the routed underlay but **does not act as a VTEP**.

| Parameter | Example value |
|:--|:--|
| Platform | Huawei CloudEngine VRP (supported VXLAN/EVPN syntax and staged commit required) |
| Lab scenario | BGP EVPN VXLAN |
| Device role | Spine / Route Reflector |
| Hostname | `HW-SPINE-RR` |
| Loopback / router ID | `10.255.0.1/32` |
| Underlay interface IP(s) | `10.0.1.1/30; 10.0.2.1/30` |
| EVPN / remote VTEP peer(s) | `10.255.0.11 / 10.255.0.12` |
| Tenant segments | Control plane only; no local service VNI |

## Prerequisites

- Use a **lab or maintenance window** and confirm the hardware/software supports this exact command family.
- Match the sample physical interface IDs to the actual device, and confirm underlay IP addressing is unique.
- Confirm end-to-end MTU accommodates VXLAN encapsulation; jumbo MTU configuration on one interface does not prove the full path supports it.
- Build OSPF loopback reachability before relying on iBGP EVPN. Configure all devices with the correct AS, RDs and RTs.
- The selected RR template is an **alternative**: choose a Cisco RR **or** a Huawei RR for this three-node lab, not both.

## Full configuration

The following is the **complete original configuration**, preserved from the supplied `.cfg` file and formatted for GitHub readability. Copy only the CLI block, not the surrounding Markdown.

```text
system-view
sysname HW-SPINE-RR
interface LoopBack0
 ip address 10.255.0.1 255.255.255.255
 quit
interface 10GE1/0/1
 undo portswitch
 ip address 10.0.1.1 255.255.255.252
 mtu 9216
 undo shutdown
 quit
interface 10GE1/0/2
 undo portswitch
 ip address 10.0.2.1 255.255.255.252
 mtu 9216
 undo shutdown
 quit
ospf 1 router-id 10.255.0.1
 area 0.0.0.0
  network 10.0.1.0 0.0.0.3
  network 10.0.2.0 0.0.0.3
  network 10.255.0.1 0.0.0.0
  quit
 quit
evpn-overlay enable
bgp 65000
 router-id 10.255.0.1
 peer 10.255.0.11 as-number 65000
 peer 10.255.0.11 connect-interface LoopBack0
 peer 10.255.0.12 as-number 65000
 peer 10.255.0.12 connect-interface LoopBack0
 ipv4-family unicast
  undo peer 10.255.0.11 enable
  undo peer 10.255.0.12 enable
  quit
 l2vpn-family evpn
  undo policy vpn-target
  peer 10.255.0.11 enable
  peer 10.255.0.11 reflect-client
  peer 10.255.0.12 enable
  peer 10.255.0.12 reflect-client
  quit
 quit
commit
return
```

## Verification commands

Run these from the appropriate device context after applying the configuration (the operational command syntax can vary by release).

```text
display ospf peer
display ip routing-table
display bgp evpn peer
display bgp evpn all routing-table
```

On the RR, confirm that both leaf peers are established and EVPN route reflection works. This node has no `Nve1` configuration in the template.

## Save and operational notes

- Review all CLI errors and verify forwarding **before** saving to startup configuration.
- Use `commit` if your VRP release uses the staged configuration model, then run `save` as documented for the platform.
- Avoid pasting these examples into production as-is. No customer IPs, authentication, security ACLs or redundancy policies are provided.
- Follow the [validation checklist](../../docs/11-operations-validation.md) and [platform guidance](../../docs/02-platform-topology.md).

---

<sub>Maintained by **Amir Hosan Jesan (Netronixbd)** · VXLAN [Huawei & Cisco] · Configuration reference for learning and lab validation.</sub>
