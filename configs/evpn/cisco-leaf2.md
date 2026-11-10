# Cisco BGP EVPN | Leaf 2 (VTEP)

[![Cisco](https://img.shields.io/badge/Cisco-Nexus-0877BD?style=flat-square)](../../README.md) ![Lab](https://img.shields.io/badge/BGP%20EVPN-advanced-7936D2?style=flat-square) ![Example](https://img.shields.io/badge/Status-Lab%20Template-64748B?style=flat-square)

[Repository home](../../README.md) · [Configuration index](../README.md) · [Full walkthrough](../../docs/06-cisco-advanced-evpn.md) · [Troubleshooting](../../docs/08-basic-troubleshooting.md)

> [!IMPORTANT]
> **Educational configuration template only.** These commands have **not** been verified on physical switches or vendor emulators. Check your model, OS release, feature support, licensing, MTU and interface names; save a backup before changing any device.

## Overview

Cisco Leaf 2 EVPN-VXLAN VTEP template. Uses OSPF for underlay reachability, iBGP EVPN for overlay signaling and separate VNIs for tenant VLANs 100 and 200.

| Parameter | Example value |
|:--|:--|
| Platform | Cisco Nexus NX-OS (VXLAN-capable device/release required) |
| Lab scenario | BGP EVPN VXLAN |
| Device role | Leaf 2 (VTEP) |
| Hostname | `NX-LEAF-2` |
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
configure terminal
hostname NX-LEAF-2
feature nv overlay
feature vn-segment-vlan-based
feature ospf
feature bgp
nv overlay evpn
interface loopback0
 ip address 10.255.0.12/32
 no shutdown
interface Ethernet1/1
 description UNDERLAY
 no switchport
 ip address 10.0.2.2/30
 mtu 9216
 no shutdown
 ip router ospf UNDERLAY area 0.0.0.0
interface loopback0
 ip router ospf UNDERLAY area 0.0.0.0
router ospf UNDERLAY
 router-id 10.255.0.12
router bgp 65000
 router-id 10.255.0.12
 neighbor 10.255.0.1
  remote-as 65000
  update-source loopback0
  address-family l2vpn evpn
   send-community both
vlan 100
 name TENANT-100
 vn-segment 10100
vlan 200
 name TENANT-200
 vn-segment 10200
interface Ethernet1/2
 description TAGGED-HOST-OR-ACCESS-SWITCH
 switchport
 switchport mode trunk
 switchport trunk allowed vlan 100,200
 no shutdown
interface nve1
 no shutdown
 source-interface loopback0
 host-reachability protocol bgp
 member vni 10100
  ingress-replication protocol bgp
 member vni 10200
  ingress-replication protocol bgp
evpn
 vni 10100 l2
  rd 10.255.0.12:100
  route-target import 65000:10100
  route-target export 65000:10100
 vni 10200 l2
  rd 10.255.0.12:200
  route-target import 65000:10200
  route-target export 65000:10200
end
```

## Verification commands

Run these from the appropriate device context after applying the configuration (the operational command syntax can vary by release).

```text
show ip ospf neighbors
show ip route
show bgp l2vpn evpn summary
show bgp l2vpn evpn
show nve peers
show nve vni
show mac address-table vlan 100
show mac address-table vlan 200
```

Check underlay and EVPN peer establishment first. Generate traffic between attached hosts and confirm learned MACs, correct VNIs and tenant isolation.

## Save and operational notes

- Review all CLI errors and verify forwarding **before** saving to startup configuration.
- When satisfied with testing, run `copy running-config startup-config` from privileged EXEC to persist the running configuration.
- Avoid pasting these examples into production as-is. No customer IPs, authentication, security ACLs or redundancy policies are provided.
- Follow the [validation checklist](../../docs/11-operations-validation.md) and [platform guidance](../../docs/02-platform-topology.md).

---

<sub>Maintained by **Amir Hosan Jesan (Netronixbd)** · VXLAN [Huawei & Cisco] · Configuration reference for learning and lab validation.</sub>
