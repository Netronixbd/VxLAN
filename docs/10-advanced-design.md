# 10 — Advanced Design Beyond the L2 Lab

[Home](../README.md)

The executable advanced lab already includes two VNIs, explicit route targets, OSPF transport and EVPN route reflection. The subjects below require an additional, platform-specific design; they are not silently enabled by the provided files.

## Routing choices

| Design | Gateway location | Additional requirements |
|---|---|---|
| L2-only extension | External router or no gateway | Same-subnet service only |
| Centralized routing | One designated gateway location | SVI/VBDIF, service attachment, routing policy and capacity planning |
| Distributed anycast gateway | Gateway on multiple leaves | Consistent gateway IP/MAC, supported IRB mode, tenant VRFs and correct host advertisement |
| Symmetric IRB | Ingress and egress VTEP route | L3VNI/IP-VRF design, L2VNIs, RT/RD scheme, router MAC and route exchange |

A Cisco SVI and a Huawei VBDIF are not enough by themselves to produce interoperable symmetric IRB. Check Type 2 MAC/IP versus Type 5 behavior, router-MAC extended communities, IRB support and the intended service model for the exact releases.

Do not configure the same ordinary gateway IP independently on both leaves and call it anycast. Anycast requires the corresponding gateway MAC and control-plane behavior.

## Multihoming

Cisco vPC and Huawei M-LAG are vendor-specific mechanisms. They are not interchangeable with standards-based EVPN ESI multihoming. Mixing them across vendors requires explicit documented support, not just matching LACP settings.

A proper EVPN multihoming design covers ESI allocation, DF election, split horizon, aliasing, failure detection and Type 1/4 routes. No such redundancy is configured in this repo.

## Scaling and resilience

- Add a second transport spine and second RR only with a complete independent address/cabling plan.
- Decide whether RR and transport failure domains should be separate.
- Confirm ECMP forwarding behavior on the actual ASIC; OSPF equal-cost routes alone do not prove per-flow forwarding configuration.
- Size MAC, ARP, VNI, route, replication and TCAM resources.
- Ingress replication multiplies BUM copies with the number of remote participants; measure broadcast load.
- Keep VNIs/RTs in a source-of-truth table and limit imports to intended service membership.

## Security and ISP handoff considerations

VXLAN is encapsulation, not encryption. Restrict VTEP transport to trusted endpoints and use an appropriate encryption layer if the transport threat model requires one. Authentication, management ACLs and BGP protection are separate deployment work.

For an ISP L2 handoff, define VLAN tagging, service MTU, broadcast limits, MAC scale, customer loop handling, operational ownership and rollback before turn-up. Do not infer that every PPPoE, Q-in-Q, multicast or control-protocol tunneling use case is covered by a simple VLAN/VNI test.

## What is needed for a deployment-specific L3 extension?

Record exact Huawei model/VRP, Cisco PID/NX-OS, physical ports, desired gateway addresses, tenant subnets/VRFs, L2/L3VNI allocation, border routing, redundancy and MTU. Build and validate that configuration separately. This boundary avoids presenting an untested universal L3 or multihoming template.
