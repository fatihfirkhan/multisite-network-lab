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

## Routing
| Term | Description |
|---|---|
| Interface | A physical port on a router or switch where a cable plugs in. On a router, each interface has its own IP address |
| Subinterface | A virtual interface created on a physical interface (for example `Gi0/0/0.10`). One cable acts like several interfaces, each with its own IP address |
| Inter-VLAN routing | Letting devices in different VLANs communicate through a router |
| Encapsulation | Wrapping data with extra information (a header) so it can be delivered correctly. For VLANs, the extra information is the VLAN tag |
| 802.1Q (dot1q) | The standard for tagging traffic with its VLAN number over a trunk. `encapsulation dot1q 10` sets a subinterface to handle VLAN 10 |
| Router-on-a-stick | Inter-VLAN routing over one trunk cable. The router has one subinterface per VLAN, and each subinterface's IP is that VLAN's gateway |
