# 13 — Standards and Primary References

[Home](../README.md)

Documentation checked on 2026-11-08. Vendor sites may redirect or require selecting the exact product release. These links support protocol and CLI research; they do not prove that the packaged lab ran on hardware.

| Source | Subject |
|---|---|
| [RFC 7348](https://www.rfc-editor.org/rfc/rfc7348.html) | VXLAN encapsulation, VNI and transport |
| [RFC 7432](https://www.rfc-editor.org/rfc/rfc7432.html) | EVPN control-plane foundations |
| [RFC 8365](https://www.rfc-editor.org/rfc/rfc8365.html) | EVPN network virtualization overlays |
| [Huawei VXLAN overview](https://info.support.huawei.com/info-finder/encyclopedia/en/VXLAN.html) | Huawei terminology and static/dynamic tunnel concepts |
| [Huawei Cloud tunnel gateway example](https://support.huaweicloud.com/intl/en-us/qs-esw/esw_qs_0007.html) | CloudEngine static VTEP and tagged service attachment |
| [Huawei VNI troubleshooting](https://info.support.huawei.com/network/ptmngsys/Web/tsrev_ce/en/content/ce/17_edesk_dc_failed_to_forward_vxlan_traffic/edesk_dc_failed_to_forward_vxlan_traffic_edesk003.html) | VNI/tunnel state |
| [Huawei MAC advertisement troubleshooting](https://info.support.huawei.com/network/ptmngsys/Web/tsrev_ce/en/content/ce/18_edesk_dc_failed_to_forward_evpn_unicast_traffic/edesk_dc_failed_to_forward_evpn_unicast_traffic_edesk003.html) | EVPN MAC route checks; includes routed use cases beyond this lab |
| [Cisco Nexus NX-OS 10.1 VXLAN](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/101x/configuration/vxlan/cisco-nexus-9000-series-nx-os-vxlan-configuration-guide-release-101x/m-configuring-vxlan.html) | Static ingress replication and platform constraints |
| [Cisco Nexus NX-OS 10.4 EVPN](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/104x/configuration/vxlan/cisco-nexus-9000-series-nx-os-vxlan-configuration-guide-release-104x/m_configuring_vxlan_bgp_evpn.html) | NVE, EVPN peers, route targets and RR configuration |
| [H3C primary interoperability guide](https://wwwsg-proxy.h3c.com/en/Support/Resource_Center/EN/Home/Switches/00-Public/Configure___Deploy/Interoperability_Guides/Interoperation_Guide_Third-Party-Long/?CHID=735739) | Vendor-authored tests including Huawei BD EVPN/RR syntax; not Huawei–Cisco certification |

Examples in this repository use independently selected addressing and a deliberately small single-homed L2 design. They are not a verbatim copy of a vendor deployment example. Consult the exact product command reference before changing syntax to resolve a platform mismatch.
