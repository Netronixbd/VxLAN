# 08 — Basic VXLAN Troubleshooting

[Home](../README.md) · [Advanced troubleshooting](09-advanced-troubleshooting.md)

Work from local attachment to transport to overlay. Preserve counters and logs before clearing anything.

## Command comparison

| Check | Huawei VRP | Cisco NX-OS |
|---|---|---|
| Version/platform | `display version` | `show version` |
| Interface summary | `display interface brief` | `show interface brief` |
| Uplink details | `display interface 10GE1/0/1` | `show interface Ethernet1/1` |
| Remote VTEP route | `display ip routing-table 10.255.0.12` | `show ip route 10.255.0.12` |
| Sourced ping from Leaf A | `ping -a 10.255.0.11 10.255.0.12` | `ping 10.255.0.12 source 10.255.0.11` |
| OSPF | `display ospf peer` | `show ip ospf neighbors` |
| EVPN peers | `display bgp evpn peer` | `show bgp l2vpn evpn summary` |
| EVPN routes | `display bgp evpn all routing-table` | `show bgp l2vpn evpn` |
| VNI state | `display vxlan vni` | `show nve vni` |
| VXLAN peers | `display vxlan tunnel` | `show nve peers` |
| Local/remote MAC | `display mac-address bridge-domain 100` | `show mac address-table vlan 100` |
| Logs | `display logbuffer` | `show logging last 100` |

The table targets CloudEngine/NX-OS lab syntax. Detailed forms and output fields vary with release.

## Symptom-to-check mapping

| Symptom | First checks | Next evidence |
|---|---|---|
| No local MAC | Host NIC, cable, VLAN tags, correct port/subinterface | Capture host ARP; check port counters |
| Remote loopback unreachable | /32 route or OSPF, link addresses, source selection | Route next hop and ARP on uplink |
| EVPN Idle/Active | TCP 179, AS 65000, source loopback reachability | Peer errors and address-family activation |
| EVPN Established, no service routes | VNI/BD active, RT, local host learning | Type 2 and Type 3 origin/advertisement |
| VNI down | Service mapping, member port, NVE source | VLAN/BD and interface state |
| Remote MAC missing | Remote local learning, static peer or EVPN import | Route and replication membership |
| MAC present, host ping fails | Host firewall/subnet, ARP, UDP 4789, MTU | Decode packet on both sides |
| Small ping works, large fails | End-to-end effective MTU and PMTUD | DF-size sweep, drops and ICMP too-big |
| Only one VLAN fails | That VLAN's local membership, VNI and RT | Compare with working VLAN |
| Only one direction fails | Return routing, ACLs, reverse MAC state | Capture both directions separately |

## Expected state

For static mode, expect correct configured peer VTEPs and host MAC learning. For EVPN, also expect reachable EVPN next hops and Type 3 membership. Type 2 routes depend on local host learning; empty host state before traffic is not proof of failure.

## Stop conditions

Do not reset BGP or remove all VLANs to diagnose an unresolved underlay failure. A transport ping does not exercise the tenant path. A router-originated ping without a tenant gateway is not a substitute for host-to-host testing.
