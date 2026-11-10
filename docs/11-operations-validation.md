# 11 — Operations, Validation and Saving

[Home](../README.md)

## Loading configuration

The `.cfg` files are terminal command templates, not automation scripts. Use a console or known working management session. Read prerequisites and substitute actual interface names first. Do not paste the Markdown prose or combine both alternatives for one role.

Huawei templates use staged VRP `commit`; on immediate-apply releases this command/workflow differs. Inspect the actual OS rather than treating an unknown command as harmless. Cisco configuration commands apply immediately and are not protected by a Junos-style `commit confirmed` workflow.

## Acceptance matrix

| Test | Static expectation | EVPN expectation | Actual result |
|---|---|---|---|
| Physical links | Both leaves up | Two spine links up | Not run |
| Sourced VTEP loopback ping | Pass both ways | Pass both ways | Not run |
| OSPF | Not configured | One neighbor/leaf, two/spine | Not run |
| BGP EVPN | Not configured | One peer/leaf, two/RR | Not run |
| BUM membership | Configured remote peer | Type 3 per intended VNI | Not run |
| Host learning | Local/remote service MACs | Type 2 and installed MACs | Not run |
| VLAN 100 ARP/ping | Pass both directions | Pass both directions | Not run |
| VLAN 200 ARP/ping | Not configured | Pass both directions | Not run |
| VLAN 100 to 200 | Not applicable | No inter-subnet routing | Not run |
| Full-size inner IP | Pass with adequate transport MTU | Same | Not run |
| Huawei/Cisco combination | Must be measured | Must be measured | Not run |

## Save after testing

Huawei staged VRP, from system view:

```text
commit
return
save
```

Read and answer the save confirmation on the device. Commit activates candidate changes; save writes the startup configuration. On immediate-apply VRP use its documented workflow instead.

Cisco NX-OS, from privileged EXEC:

```text
copy running-config startup-config
```

## Rollback approach

Capture a pre-change baseline and use the actual platform's documented checkpoint/configuration rollback facilities. A restore can interrupt management and traffic. Do not assume `undo`, `no`, reboot, or copying startup to running restores an entire prior state identically.

For these isolated labs, re-create the clean baseline between static and EVPN scenarios. On existing devices, produce a reviewed differential rollback rather than deleting global protocol configuration shared by other services.

## Record one result

```text
Date/time and timezone:
Lab: static / EVPN
Device role:
Vendor/model/PID:
OS release and license:
Config file and local changes:
Host VLAN/VNI and addresses:
Underlay ping result:
BGP / VNI state:
Local and remote MAC evidence:
Host ARP / ICMP result:
Largest tested inner IP size:
Fault injected and restored:
Unexpected behavior:
```

No fabricated CLI outputs or measured convergence numbers are supplied. Replace `Not run` with actual evidence as the lab is tested.
