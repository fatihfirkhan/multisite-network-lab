# Terminology
This document contains definitions of technical terms encountered while building the Packet Tracer multi-site network lab.

## Switching
| Term | Description |
|---|---|
| VLAN | Virtual LAN; splits one physical switch into separate networks. Devices in different VLANs cannot talk to each other directly. Each VLAN is identified by a number (the VLAN ID), which is only a label and not an IP address |
| Trunk | A switch port that carries traffic for several VLANs over a single cable. Each piece of traffic is tagged with its VLAN ID so the device at the other end knows which VLAN it belongs to. Used between a switch and a router |

## Addressing and Links
| Term | Description |
|---|---|
| Gateway | Router's address on a network. A device sends traffic to its gateway when the destination is outside its own network |
| Management IP | The IP address given to a switch itself (not to the devices plugged into it) so an admin can connect to it remotely, for example over SSH |
| WAN link | Wide Area Network link; the connection between routers at different sites. Each WAN link gets its own small subnet, with one address for each router on the cable |
| DHCP | Dynamic Host Configuration Protocol; automatically gives devices an IP address, subnet mask, and default gateway when they join a network |
| DHCP pool | The range of addresses a DHCP server can hand out for a network, plus the settings given with them (such as the gateway) |
| Lease | The length of time a device may keep the address DHCP gave it. The device renews it or gives it back when the time is up |

## Routing
| Term | Description |
|---|---|
| Interface | Physical ports on a router or switch where a cable plugs in. On a router, each interface has its own IP address |
| Subinterface | A virtual interface created on a physical interface (for example `Gi0/0/0.10`). One cable acts like several interfaces, each with its own IP address |
| Inter-VLAN routing | Letting devices in different VLANs communicate through a router |
| Encapsulation | Wrapping data with extra information (a header) so it can be delivered correctly. For VLANs, the extra information is the VLAN tag |
| 802.1Q (dot1q) | The standard for tagging traffic with its VLAN number over a trunk. `encapsulation dot1q 10` sets a subinterface to handle VLAN 10 |
| Router-on-a-stick | Inter-VLAN routing over one trunk cable. The router has one subinterface per VLAN, and each subinterface's IP is that VLAN's gateway |
| Static routing | Routes typed in by hand on each router |
| Dynamic routing | Routers learn routes automatically from each other |
| OSPF | Open Shortest Path First; a dynamic routing protocol where routers share their networks and pick the shortest path to each |
| router id | Unique name for router in OSPF, written like an IP address |
| Area | A group of routers that share the same OSPF map. Small networks use one area, and area 0 is the main one |
| Passive interface | An interface that does not send OSPF messages, used for networks with only end devices. Its network is still advertised |
| Wildcard mask | The inverse of a subnet mask, used in OSPF `network` commands. `0.0.0.255` matches a /24 network |

## NAT
| Term | Description |
|---|---|
| Private address | An address range reserved for internal networks (such as 192.168.0.0/16) that cannot be used on the internet |
| NAT | Network Address Translation; a router changes the address in traffic as it passes through, usually swapping a private address for a public one |
| PAT (NAT overload) | NAT where many devices share one address. The router tells their traffic apart using port numbers |
| Inside local | A device's real private address |
| Inside global | The address the outside world sees for that device |
| Inside / outside interface | Inside faces the company's own network. Outside faces the ISP |

## ACLs
| Term | Description |
|---|---|
| ACL | Access Control List; a list of permit and deny rules that match traffic |
| Inbound (in) | The ACL checks traffic as it enters the router through that interface |
| Implicit deny | The invisible rule at the end of every ACL that drops traffic matching no rule |
| Stateless | The ACL checks each packet on its own and does not remember connections, so replies are filtered like new traffic |
| Port | A number that identifies a service on a device, such as 80 for web or 67 and 68 for DHCP |
