# Huawei BGP EVPN | Leaf 2 (VTEP)

[![Huawei](https://img.shields.io/badge/Huawei-CloudEngine-ED1C24?style=flat-square)](../../README.md) ![Lab](https://img.shields.io/badge/BGP%20EVPN-advanced-7936D2?style=flat-square) ![Example](https://img.shields.io/badge/Status-Lab%20Template-64748B?style=flat-square)

[Repository home](../../README.md) · [Configuration index](../README.md) · [Full walkthrough](../../docs/05-huawei-advanced-evpn.md) · [Troubleshooting](../../docs/08-basic-troubleshooting.md)

> [!IMPORTANT]
> **Educational configuration template only.** These commands have **not** been verified on physical switches or vendor emulators. Check your model, OS release, feature support, licensing, MTU and interface names; save a backup before changing any device.

## Overview

Huawei Leaf 2 EVPN-VXLAN VTEP template. Uses OSPF for underlay reachability, iBGP EVPN for overlay signaling and separate VNIs for tenant VLANs 100 and 200.

| Parameter | Example value |
|:--|:--|
| Platform | Huawei CloudEngine VRP (supported VXLAN/EVPN syntax and staged commit required) |
| Lab scenario | BGP EVPN VXLAN |
| Device role | Leaf 2 (VTEP) |
| Hostname | `HW-LEAF-2` |
| Loopback / router ID | `10.255.0.12/32` |
| Underlay interface IP(s) | `10.0.2.2/30` |
| EVPN / remote VTEP peer(s) | `10.255.0.1` |
| Tenant segments | VLAN 100 ↔ VNI 10100; VLAN 200 ↔ VNI 10200 |

## Prerequisites

- Use a **lab or maintenance window** and confirm the hardware/software supports this exact command family.
- Match the sample physical interface IDs to the actual device, and confirm underlay IP addressing is unique.
- Confirm end-to-end MTU accommodates VXLAN encapsulation; jumbo MTU configuration on one interface does not prove the full path supports it.
- Build OSPF loopback reachability before relying on iBGP EVPN. Configure all devices with the correct AS, RDs and RTs.
- Deploy alongside a compatible EVPN spine/RR and the opposite leaf. The sample uses AS 65000 and two Layer 2 VNIs.

## Full configuration

The following is the **complete original configuration**, preserved from the supplied `.cfg` file and formatted for GitHub readability. Copy only the CLI block, not the surrounding Markdown.

```text
system-view
sysname HW-LEAF-2
interface LoopBack0
 ip address 10.255.0.12 255.255.255.255
 quit
interface 10GE1/0/1
 description UNDERLAY
 undo portswitch
 ip address 10.0.2.2 255.255.255.252
 mtu 9216
 undo shutdown
 quit
ospf 1 router-id 10.255.0.12
 area 0.0.0.0
  network 10.0.2.0 0.0.0.3
  network 10.255.0.12 0.0.0.0
  quit
 quit
evpn-overlay enable
bgp 65000
 router-id 10.255.0.12
 peer 10.255.0.1 as-number 65000
 peer 10.255.0.1 connect-interface LoopBack0
 ipv4-family unicast
  undo peer 10.255.0.1 enable
  quit
 l2vpn-family evpn
  peer 10.255.0.1 enable
  quit
 quit
bridge-domain 100
 vxlan vni 10100
 evpn
  route-distinguisher 10.255.0.12:100
  vpn-target 65000:10100 export-extcommunity
  vpn-target 65000:10100 import-extcommunity
  quit
 quit
bridge-domain 200
 vxlan vni 10200
 evpn
  route-distinguisher 10.255.0.12:200
  vpn-target 65000:10200 export-extcommunity
  vpn-target 65000:10200 import-extcommunity
  quit
 quit
interface 10GE1/0/2
 description TAGGED-HOST-OR-ACCESS-SWITCH
 undo shutdown
 quit
interface 10GE1/0/2.100 mode l2
 encapsulation dot1q vid 100
 bridge-domain 100
 quit
interface 10GE1/0/2.200 mode l2
 encapsulation dot1q vid 200
 bridge-domain 200
 quit
interface Nve1
 source 10.255.0.12
 vni 10100 head-end peer-list protocol bgp
 vni 10200 head-end peer-list protocol bgp
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
display vxlan vni
display vxlan tunnel
display mac-address bridge-domain 100
display mac-address bridge-domain 200
```

Check underlay and EVPN peer establishment first. Generate traffic between attached hosts and confirm learned MACs, correct VNIs and tenant isolation.

## Save and operational notes

- Review all CLI errors and verify forwarding **before** saving to startup configuration.
- Use `commit` if your VRP release uses the staged configuration model, then run `save` as documented for the platform.
- Avoid pasting these examples into production as-is. No customer IPs, authentication, security ACLs or redundancy policies are provided.
- Follow the [validation checklist](../../docs/11-operations-validation.md) and [platform guidance](../../docs/02-platform-topology.md).

---

<sub>Maintained by **Amir Hosan Jesan (Netronixbd)** · VXLAN [Huawei & Cisco] · Configuration reference for learning and lab validation.</sub>
