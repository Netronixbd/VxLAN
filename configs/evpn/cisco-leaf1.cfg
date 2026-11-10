configure terminal
hostname NX-LEAF-1
feature nv overlay
feature vn-segment-vlan-based
feature ospf
feature bgp
nv overlay evpn
interface loopback0
 ip address 10.255.0.11/32
 no shutdown
interface Ethernet1/1
 description UNDERLAY
 no switchport
 ip address 10.0.1.2/30
 mtu 9216
 no shutdown
 ip router ospf UNDERLAY area 0.0.0.0
interface loopback0
 ip router ospf UNDERLAY area 0.0.0.0
router ospf UNDERLAY
 router-id 10.255.0.11
router bgp 65000
 router-id 10.255.0.11
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
  rd 10.255.0.11:100
  route-target import 65000:10100
  route-target export 65000:10100
 vni 10200 l2
  rd 10.255.0.11:200
  route-target import 65000:10200
  route-target export 65000:10200
end
