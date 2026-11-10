# 07 — Huawei–Cisco Interoperability

[Home](../README.md)

These are standards-based candidate lab combinations, not a certification of an exact Huawei/Cisco release pair. Test the actual PID, OS release and service encapsulation before deployment.

## Static mixed-vendor lab

| Role | Select exactly one file |
|---|---|
| Huawei Leaf A | [huawei-leaf1.cfg](../configs/static/huawei-leaf1.cfg) |
| Cisco Leaf B | [cisco-leaf2.cfg](../configs/static/cisco-leaf2.cfg) |

Connect Huawei 10GE1/0/1 directly to Cisco Ethernet1/1. Host links use tagged VLAN 100. Verify that Huawei emits the expected inner Ethernet format and that Cisco maps the decoded VNI to VLAN 100. Do not add Q-in-Q or retain-inner-tag settings unless deliberately testing that feature.

The reverse vendor arrangement uses Cisco leaf1 and Huawei leaf2 with the same role/address mapping.

## Advanced mixed-vendor lab

| Role | Default selection | Alternative |
|---|---|---|
| Leaf A | [Huawei EVPN leaf1](../configs/evpn/huawei-leaf1.cfg) | [Cisco EVPN leaf1](../configs/evpn/cisco-leaf1.cfg) |
| Leaf B | [Cisco EVPN leaf2](../configs/evpn/cisco-leaf2.cfg) | [Huawei EVPN leaf2](../configs/evpn/huawei-leaf2.cfg) |
| Spine/RR | [Cisco RR](../configs/evpn/cisco-spine-rr.cfg) | [Huawei RR](../configs/evpn/huawei-spine-rr.cfg) |

Use one file per role. Leaf A port 1 connects to spine port 1. Leaf B port 1 connects to spine port 2. No direct leaf-to-leaf physical link exists.

## Agreement checklist

| Property | Huawei | Cisco | Required agreement |
|---|---|---|---|
| Source VTEP | Nve1 source | NVE source-interface | Unique and routable |
| Service 100 | BD 100 / VNI 10100 | VLAN 100 / VNI 10100 | Same VNI and intended Ethernet service |
| Service 200 | BD 200 / VNI 10200 | VLAN 200 / VNI 10200 | Same VNI and intended Ethernet service |
| Import/export RT | vpn-target | route-target | 65000:10100 or 65000:10200 |
| Route uniqueness | route-distinguisher | rd | Unique per leaf/service |
| BUM replication | head-end peer-list protocol bgp | ingress-replication protocol bgp | EVPN Type 3 membership |
| Overlay session | l2vpn-family evpn | address-family l2vpn evpn | EVPN enabled on both peers |
| VXLAN destination UDP port | Verify effective value | 4789 | Must match |
| Host side | Tagged L2 subinterface | Tagged trunk | Host-facing service expectations match |

## Minimal acceptance

1. Source-loopback pings work both ways.
2. EVPN Type 3 routes appear on the remote leaf for each service.
3. After host traffic, Type 2 routes carry the intended VNI/RT and leaf next hop.
4. Local and remote MAC forwarding entries are installed.
5. Host-to-host ARP and ping work in each subnet.
6. Full-size inner IP traffic works; small ping alone is insufficient.
7. There is no accidental bridging between service 100 and service 200.

If static works but EVPN fails, examine EVPN family negotiation, RTs, next-hop resolution and route reflection. If both fail, return to tagging, VTEP routes, UDP transport, MTU and data-plane support.
