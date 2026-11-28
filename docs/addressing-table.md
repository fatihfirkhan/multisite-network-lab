## subnet and VLAN

| Site | VLAN ID | Name | Subnet | Gateway |
|---|---|---|---|---|
| HQ | 10 | staff | 192.168.10.0/24 | 192.168.10.1 |
| HQ | 20 | servers | 192.168.20.0/24 | 192.168.20.1 |
| HQ | 30 | guest | 192.168.30.0/24 | 192.168.30.1 |
| Branch | 40 | staff | 192.168.40.0/24 | 192.168.40.1 |
| Branch | 50 | servers | 192.168.50.0/24 | 192.168.50.1 |
| WAN | none | HQ to ISP | 10.0.0.0/30 | none |
| WAN | none | ISP to Branch | 10.0.0.4/30 | none |
| Internet | none | Internet server | 203.0.113.0/24 | 203.0.113.1 |

## Router Interfaces
| Device | Interface | IP address | Mask | Connects to |
|---|---|---|---|---|
| HQ-R1 | Gi0/0/0 | no IP (trunk) | none | HQ-SW1 Fa0/1 |
| HQ-R1 | Gi0/0/0.10 | 192.168.10.1 | 255.255.255.0 | VLAN 10 (Staff) |
| HQ-R1 | Gi0/0/0.20 | 192.168.20.1 | 255.255.255.0 | VLAN 20 (Servers) |
| HQ-R1 | Gi0/0/0.30 | 192.168.30.1 | 255.255.255.0 | VLAN 30 (Guest) |
| HQ-R1 | Gi0/0/1 | 10.0.0.1 | 255.255.255.252 | ISP-R1 Gi0/0/0 |
| ISP-R1 | Gi0/0/0 | 10.0.0.2 | 255.255.255.252 | HQ-R1 Gi0/0/1 |
| ISP-R1 | Gi0/0/1 | 10.0.0.5 | 255.255.255.252 | BR-R1 Gi0/0/1 |
| ISP-R1 | Gi0/0/2 | 203.0.113.1 | 255.255.255.0 | Internet-SRV |
| BR-R1 | Gi0/0/0 | no IP (trunk) | none | BR-SW1 Fa0/1 |
| BR-R1 | Gi0/0/0.40 | 192.168.40.1 | 255.255.255.0 | VLAN 40 (Staff) |
| BR-R1 | Gi0/0/0.50 | 192.168.50.1 | 255.255.255.0 | VLAN 50 (Servers) |
| BR-R1 | Gi0/0/1 | 10.0.0.6 | 255.255.255.252 | ISP-R1 Gi0/0/1 |

`ISP-R1` Gi0/0/2 is an SFP slot on the PT8200, so a GLC-T copper module is installed to use it.

## Switch Ports
| Switch | Port | Mode | VLAN | Connects to |
|---|---|---|---|---|
| HQ-SW1 | Fa0/1 | trunk | 10, 20, 30 | HQ-R1 Gi0/0/0 |
| HQ-SW1 | Fa0/2-3 | access | 10 | HQ-PC-Staff1, HQ-PC-Staff2 |
| HQ-SW1 | Fa0/4-5 | access | 20 | HQ-SRV1, HQ-SRV2 |
| HQ-SW1 | Fa0/6 | access | 30 | HQ-PC-Guest1 |
| BR-SW1 | Fa0/1 | trunk | 40, 50 | BR-R1 Gi0/0/0 |
| BR-SW1 | Fa0/2-3 | access | 40 | BR-PC-Staff1, BR-PC-Staff2 |
| BR-SW1 | Fa0/4-5 | access | 50 | BR-SRV1, BR-SRV2 |

## Address Ranges (each /24 user network)
| Range | Used for |
|---|---|
| .1 | Gateway (router) |
| .2 to .9 | Network devices |
| .10 to .49 | Servers (static) |
| .50 to .200 | PCs (DHCP pool) |

## Static Addresses (Servers)
| Device | VLAN | IP address | Mask | Gateway |
|---|---|---|---|---|
| HQ-SRV1 | 20 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| HQ-SRV2 | 20 | 192.168.20.11 | 255.255.255.0 | 192.168.20.1 |
| BR-SRV1 | 50 | 192.168.50.10 | 255.255.255.0 | 192.168.50.1 |
| BR-SRV2 | 50 | 192.168.50.11 | 255.255.255.0 | 192.168.50.1 |
| Internet-SRV | none | 203.0.113.10 | 255.255.255.0 | 203.0.113.1 |

## DHCP Pools (PCs)
| Router | Pool | VLAN | Network | Gateway | Addresses handed out |
|---|---|---|---|---|---|
| HQ-R1 | HQ-STAFF | 10 | 192.168.10.0/24 | 192.168.10.1 | .50 to .200 |
| HQ-R1 | HQ-GUEST | 30 | 192.168.30.0/24 | 192.168.30.1 | .50 to .200 |
| BR-R1 | BR-STAFF | 40 | 192.168.40.0/24 | 192.168.40.1 | .50 to .200 |

## OSPF Router IDs
| Router | Router ID |
|---|---|
| HQ-R1 | 1.1.1.1 |
| ISP-R1 | 2.2.2.2 |
| BR-R1 | 3.3.3.3 |

## NAT
| Router | Inside interfaces | Outside interface | Translated to |
|---|---|---|---|
| HQ-R1 | Gi0/0/0.10, .20, .30 | Gi0/0/1 | 10.0.0.1 |
| BR-R1 | Gi0/0/0.40, .50 | Gi0/0/1 | 10.0.0.6 |
