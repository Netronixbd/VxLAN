# Cisco BGP EVPN | Spine / Route Reflector

[![Cisco](https://img.shields.io/badge/Cisco-Nexus-0877BD?style=flat-square)](../../README.md) ![Lab](https://img.shields.io/badge/BGP%20EVPN-advanced-7936D2?style=flat-square) ![Example](https://img.shields.io/badge/Status-Lab%20Template-64748B?style=flat-square)

[Repository home](../../README.md) · [Configuration index](../README.md) · [Full walkthrough](../../docs/06-cisco-advanced-evpn.md) · [Troubleshooting](../../docs/08-basic-troubleshooting.md)

> [!IMPORTANT]
> **Educational configuration template only.** These commands have **not** been verified on physical switches or vendor emulators. Check your model, OS release, feature support, licensing, MTU and interface names; save a backup before changing any device.

## Overview

Route reflector template for a three-node Cisco EVPN laboratory. This node exchanges EVPN reachability between Leaf 1 and Leaf 2 using iBGP; it forwards the routed underlay but **does not act as a VTEP**.

| Parameter | Example value |
|:--|:--|
| Platform | Cisco Nexus NX-OS (VXLAN-capable device/release required) |
| Lab scenario | BGP EVPN VXLAN |
| Device role | Spine / Route Reflector |
| Hostname | `NX-SPINE-RR` |
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
configure terminal
hostname NX-SPINE-RR
feature ospf
feature bgp
nv overlay evpn
interface loopback0
 ip address 10.255.0.1/32
 ip router ospf UNDERLAY area 0.0.0.0
interface Ethernet1/1
 description TO-LEAF-A
 no switchport
 ip address 10.0.1.1/30
 mtu 9216
 ip router ospf UNDERLAY area 0.0.0.0
 no shutdown
interface Ethernet1/2
 description TO-LEAF-B
 no switchport
 ip address 10.0.2.1/30
 mtu 9216
 ip router ospf UNDERLAY area 0.0.0.0
 no shutdown
router ospf UNDERLAY
 router-id 10.255.0.1
router bgp 65000
 router-id 10.255.0.1
 address-family l2vpn evpn
  retain route-target all
 neighbor 10.255.0.11
  remote-as 65000
  update-source loopback0
  address-family l2vpn evpn
   send-community both
   route-reflector-client
 neighbor 10.255.0.12
  remote-as 65000
  update-source loopback0
  address-family l2vpn evpn
   send-community both
   route-reflector-client
end
```

## Verification commands

Run these from the appropriate device context after applying the configuration (the operational command syntax can vary by release).

```text
show ip ospf neighbors
show ip route
show bgp l2vpn evpn summary
show bgp l2vpn evpn
```

On the RR, confirm that both leaf peers are established and EVPN route reflection works. This node has no `Nve1` configuration in the template.

## Save and operational notes

- Review all CLI errors and verify forwarding **before** saving to startup configuration.
- When satisfied with testing, run `copy running-config startup-config` from privileged EXEC to persist the running configuration.
- Avoid pasting these examples into production as-is. No customer IPs, authentication, security ACLs or redundancy policies are provided.
- Follow the [validation checklist](../../docs/11-operations-validation.md) and [platform guidance](../../docs/02-platform-topology.md).

---

<sub>Maintained by **Amir Hosan Jesan (Netronixbd)** · VXLAN [Huawei & Cisco] · Configuration reference for learning and lab validation.</sub>
