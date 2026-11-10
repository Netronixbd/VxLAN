# 01 — VXLAN Concepts

[Home](../README.md) · [Next: topology](02-platform-topology.md)

## The problem VXLAN solves

A normal VLAN is a local Layer 2 segmentation mechanism. Extending it over a large network traditionally requires Layer 2 transport and creates broad failure domains. VXLAN makes the transport a routed IP fabric while retaining an Ethernet segment at the edges.

In an ISP, a suitable use case is a controlled L2 service between POPs or a data-center attachment. Extending subscriber broadcast domains indiscriminately across a WAN can also extend loops, DHCP noise and failures. Choose the service boundary before choosing the tunnel.

## Terms

| Term | Meaning in this repo |
|---|---|
| VTEP | Device function that adds/removes VXLAN encapsulation |
| NVE | Vendor interface representing the network virtualization edge |
| VNI | 24-bit segment identifier; 2^24 encodings, usable values/platform scale are smaller |
| VLAN | Local Ethernet service identifier, mapped to a VNI |
| BD | Huawei bridge domain, containing the local L2 service |
| Underlay | Routed transport between loopback/VTEP addresses |
| Overlay | Encapsulated tenant Ethernet service |
| BUM | Broadcast, unknown unicast and multicast |
| Ingress replication | Source VTEP makes a separate copy for each remote flood destination |
| EVPN | BGP address family advertising Ethernet VPN reachability |
| RD | Distinguishes otherwise identical EVPN routes; unique per originating service/leaf |
| RT | Extended community controlling service route import/export |
| L2VNI | VNI representing an Ethernet broadcast domain |
| L3VNI | VNI representing a routed tenant context in an IRB design |

The same VNI must identify the same intended service at all participating VTEPs. Local VLAN numbers can differ in some designs, but these labs keep them equal to simplify diagnosis. VLAN 100 is mapped to VNI 10100; this numbering is a convention, not a protocol requirement.

## Encapsulation

The inner host Ethernet frame is carried after a VXLAN header, UDP header, outer IP header and outer Ethernet header. The routed fabric inspects the outer destination VTEP IP, not the inner host IP.

For an untagged inner Ethernet frame carrying a 1500-byte IP packet, an IPv4 underlay needs an outer IP packet of about 1550 bytes: 1500 + 14 inner Ethernet + 8 VXLAN + 8 UDP + 20 IPv4. The outer Ethernet frame is larger again. VLAN tags and IPv6 change the accounting. A nominal 1500-byte underlay is therefore insufficient for full-size 1500-byte inner IP packets without an alternate MTU design.

## Static VXLAN vs EVPN

| Behavior | Static | EVPN |
|---|---|---|
| Remote VTEP discovery | Manual | BGP signaling |
| MAC reachability | Data-plane learning | EVPN routes plus local learning |
| BUM destinations | Configured peers | Derived from relevant EVPN membership |
| Operational complexity | Simple for a tiny lab | More control-plane state, better scaling |
| Underlay multicast in these labs | Not required | Not required |

Static VXLAN has no BGP session to inspect. A configured static tunnel status does not prove that the remote host is reachable. EVPN BGP Established also does not prove UDP 4789 or hardware forwarding works.

## EVPN routes

| Type | Purpose | Expected here? |
|---|---|---|
| 1 | Ethernet auto-discovery, used in multihoming procedures | Not required in this single-homed lab |
| 2 | MAC advertisement, optionally including host IP | Yes after host traffic; IP binding is not guaranteed |
| 3 | Inclusive multicast membership / IMET | Yes for ingress-replication membership |
| 4 | Ethernet segment discovery | Not required; no ESI configuration |
| 5 | IP prefix advertisement | Not configured; an L2 lab does not require it |

## Packet walkthrough

1. Host A sends ARP for Host B in VLAN 100.
2. Leaf A classifies it into BD/VLAN 100 and VNI 10100.
3. The VTEP replicates the BUM frame to the appropriate remote VTEP.
4. The underlay routes the outer packet to Leaf B.
5. Leaf B decapsulates, maps VNI 10100 into the local service and sends the frame with the expected access-side tagging.
6. Host B replies. Subsequent known-unicast forwarding uses the learned/signaled destination MAC and remote VTEP.

No gateway is necessary for same-subnet communication. A router/SVI/VBDIF/IRB design is needed for different IP subnets.

[Standards and references](13-references.md)
