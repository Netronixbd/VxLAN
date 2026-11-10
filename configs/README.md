# Configuration Library · VXLAN [Huawei & Cisco]

[Home](../README.md) · [Topology](../docs/02-platform-topology.md) · [Troubleshooting](../docs/08-basic-troubleshooting.md)

> [!NOTE]
> All **10 configuration templates** are now GitHub-friendly Markdown guides. Each includes a clear device overview, complete CLI block, prerequisites, verification commands and operational cautions. Select only the devices required for your lab; the static and EVPN setups are separate.

### 01 · Static VXLAN

| Vendor | Device | Configuration guide |
|:--|:--|:--|
| **Cisco** | Leaf 1 (VTEP) | [Cisco Leaf1](static/cisco-leaf1.md) |
| **Cisco** | Leaf 2 (VTEP) | [Cisco Leaf2](static/cisco-leaf2.md) |
| **Huawei** | Leaf 1 (VTEP) | [Huawei Leaf1](static/huawei-leaf1.md) |
| **Huawei** | Leaf 2 (VTEP) | [Huawei Leaf2](static/huawei-leaf2.md) |

### 02 · BGP EVPN VXLAN

| Vendor | Device | Configuration guide |
|:--|:--|:--|
| **Cisco** | Leaf 1 (VTEP) | [Cisco Leaf1](evpn/cisco-leaf1.md) |
| **Cisco** | Leaf 2 (VTEP) | [Cisco Leaf2](evpn/cisco-leaf2.md) |
| **Cisco** | Spine / Route Reflector | [Cisco Spine Rr](evpn/cisco-spine-rr.md) |
| **Huawei** | Leaf 1 (VTEP) | [Huawei Leaf1](evpn/huawei-leaf1.md) |
| **Huawei** | Leaf 2 (VTEP) | [Huawei Leaf2](evpn/huawei-leaf2.md) |
| **Huawei** | Spine / Route Reflector | [Huawei Spine Rr](evpn/huawei-spine-rr.md) |

### Which files should I open?

- **Static lab:** Choose one `leaf1` and one `leaf2`, either same-vendor or mixed-vendor when verified for your releases. No route reflector is used.
- **BGP EVPN lab:** Choose one `leaf1`, one `leaf2` and **one** `spine-rr` implementation, plus the matching topology guide.
- Cross-vendor experiments require hardware/software validation and review of [interoperability caveats](../docs/07-huawei-cisco-interop.md).

### How to use a guide

1. Read the overview and platform prerequisites.
2. Compare interface, underlay, loopback and VNI details against your real lab plan.
3. Copy **only** the fenced CLI configuration block to the appropriate device.
4. Run the verification commands, generate host traffic and record evidence.
5. Save only after validation. Do not treat these snippets as production-ready configs.

---

**Maintainer:** Amir Hosan Jesan (Netronixbd) · **Status:** Documentation-reviewed lab templates; not device-executed.
