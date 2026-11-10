# Cisco Static VXLAN | Leaf 2 (VTEP)

[![Cisco](https://img.shields.io/badge/Cisco-Nexus-0877BD?style=flat-square)](../../README.md) ![Lab](https://img.shields.io/badge/Static%20VXLAN-basic-15803D?style=flat-square) ![Example](https://img.shields.io/badge/Status-Lab%20Template-64748B?style=flat-square)

[Repository home](../../README.md) · [Configuration index](../README.md) · [Full walkthrough](../../docs/04-cisco-basic-static.md) · [Troubleshooting](../../docs/08-basic-troubleshooting.md)

> [!IMPORTANT]
> **Educational configuration template only.** These commands have **not** been verified on physical switches or vendor emulators. Check your model, OS release, feature support, licensing, MTU and interface names; save a backup before changing any device.

## Overview

Cisco Leaf 2 static VXLAN VTEP template. Extends tagged VLAN 100 over a routed point-to-point underlay using a manually defined remote VTEP.

| Parameter | Example value |
|:--|:--|
| Platform | Cisco Nexus NX-OS (VXLAN-capable device/release required) |
| Lab scenario | Static VXLAN |
| Device role | Leaf 2 (VTEP) |
| Hostname | `NX-LEAF-2` |
| Loopback / router ID | `10.255.0.12/32` |
| Underlay interface IP(s) | `10.0.12.2/30` |
| EVPN / remote VTEP peer(s) | `10.255.0.11` |
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
configure terminal
hostname NX-LEAF-2
feature nv overlay
feature vn-segment-vlan-based
interface loopback0
 ip address 10.255.0.12/32
 no shutdown
interface Ethernet1/1
 description UNDERLAY
 no switchport
 ip address 10.0.12.2/30
 mtu 9216
 no shutdown
ip route 10.255.0.11/32 10.0.12.1
vlan 100
 name TENANT-100
 vn-segment 10100
interface Ethernet1/2
 description TAGGED-HOST-OR-ACCESS-SWITCH
 switchport
 switchport mode trunk
 switchport trunk allowed vlan 100
 no shutdown
interface nve1
 no shutdown
 source-interface loopback0
 member vni 10100
  ingress-replication protocol static
   peer-ip 10.255.0.11
end
```

## Verification commands

Run these from the appropriate device context after applying the configuration (the operational command syntax can vary by release).

```text
show ip route 10.255.0.11
ping 10.255.0.11 source 10.255.0.12
show nve peers
show nve vni
show mac address-table vlan 100
```

After verifying sourced VTEP reachability, send traffic between hosts on VLAN 100 at each leaf. Confirm VLAN/VNI mapping and learned MAC entries.

## Save and operational notes

- Review all CLI errors and verify forwarding **before** saving to startup configuration.
- When satisfied with testing, run `copy running-config startup-config` from privileged EXEC to persist the running configuration.
- Avoid pasting these examples into production as-is. No customer IPs, authentication, security ACLs or redundancy policies are provided.
- Follow the [validation checklist](../../docs/11-operations-validation.md) and [platform guidance](../../docs/02-platform-topology.md).

---

<sub>Maintained by **Amir Hosan Jesan (Netronixbd)** · VXLAN [Huawei & Cisco] · Configuration reference for learning and lab validation.</sub>
