# Router and Switch configurations

## Basic Device Setup
Applied to all routers and switches. Only the hostname differs per device:

```
enable
config terminal
hostname hostname
no ip domain-lookup
enable secret LabPass123
banner motd #Authorized access only#
line console 0
 logging sync
 exit
end
write
```

| Command | Description |
|---|---|
| `enable` | Moves from user mode (`>`) to privileged mode (`#`), where configuration commands are allowed |
| `config terminal` | Enters global configuration mode, where changes to the device are made |
| `hostname ISP-R1` | Sets the device name, matching the topology diagram |
| `no ip domain-lookup` | Stops the device from trying to look up mistyped commands as hostnames, which causes long freezes |
| `enable secret LabPass123` | Sets the password required to enter privileged mode. Stored in encrypted form in the config |
| `banner motd #Authorized access only#` | Sets a warning message shown to anyone who logs in. The `#` marks the start and end of the message |
| `line console 0` | Enters the settings for the console port (the direct cable connection) |
| `logging sync` | Stops system messages from interrupting a command while it is being typed |
| `exit` | Goes back one level (here, from the console line settings to global configuration mode) |
| `end` | Returns directly to privileged mode from any configuration level |
| `write` | Saves the running config to the startup config so it survives a restart |

## Switch VLAN Setup
Applied to HQ-SW1. BR-SW1 uses the same commands with VLAN 40 (Staff) and VLAN 50 (Servers).

```
enable
config terminal
vlan 10
 name Staff
vlan 20
 name Servers
vlan 30
 name Guest
 exit
interface range fa0/2-3
 switchport mode access
 switchport access vlan 10
interface range fa0/4-5
 switchport mode access
 switchport access vlan 20
interface range fa0/6
 switchport mode access
 switchport access vlan 30
interface fa0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
end
write
```

`enable`, `configure terminal`, `exit`, `end` and `write memory` work as described in Basic Device Setup.

| Command | Description |
|---|---|
| `vlan 10` | Creates VLAN 10 on the switch and enters its settings |
| `name Staff` | Gives the VLAN a readable name |
| `interface range fa0/2-3` | Selects several ports at once so they can be configured together |
| `switchport mode access` | Sets the port to carry a single VLAN, for end devices |
| `switchport access vlan 10` | Assigns the port to VLAN 10 |
| `interface fa0/1` | Selects a single port (here, the port toward the router) |
| `switchport mode trunk` | Sets the port to carry multiple VLANs over one cable |
| `switchport trunk allowed vlan 10,20,30` | Limits the trunk to only the listed VLANs |
| `show vlan brief` | Lists the VLANs and which ports belong to each |
| `show interfaces trunk` | Shows which ports are trunks and which VLANs they carry |

## Inter-VLAN Routing
Router-on-a-stick: one subinterface per VLAN on the trunk port. BR-R1 uses the same commands with VLAN 40 and 50.

```
interface gi0/0/0
 description Trunk to HQ-SW1
 no shutdown
 exit
interface gi0/0/0.10
 description Staff VLAN
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 exit
interface gi0/0/0.20
 description Servers VLAN
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 exit
interface gi0/0/0.30
 description Guest VLAN
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 exit
```

| Command | Description |
|---|---|
| `description Trunk to HQ-SW1` | Adds a label to the interface for documentation |
| `no shutdown` | Turns the interface on |
| `interface gi0/0/0.10` | Creates subinterface 10 on the physical port |
| `encapsulation dot1Q 10` | Sets the subinterface to handle traffic tagged with VLAN 10 |
| `ip address 192.168.10.1 255.255.255.0` | Sets the router's address in the VLAN, which is the gateway for its devices |
| `show ip interface brief` | Lists interfaces with their IP address and status |
| `show ip route` | Shows the networks the router knows how to reach |
| `show cdp neighbors` | Shows directly connected Cisco devices and the ports they connect to |

## DHCP
The router hands out addresses to PCs in the Staff and Guest VLANs. Servers keep static addresses. BR-R1 uses the same commands for VLAN 40 only, with pool `BR-STAFF`.

```
ip dhcp excluded-address 192.168.10.1 192.168.10.49
ip dhcp excluded-address 192.168.10.201 192.168.10.254
ip dhcp excluded-address 192.168.30.1 192.168.30.49
ip dhcp excluded-address 192.168.30.201 192.168.30.254
ip dhcp pool HQ-STAFF
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 exit
ip dhcp pool HQ-GUEST
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 exit
```

| Command | Description |
|---|---|
| `ip dhcp excluded-address 192.168.10.1 192.168.10.49` | Reserves a range of addresses so DHCP never hands them out (gateway, servers, network devices) |
| `ip dhcp pool HQ-STAFF` | Creates a DHCP pool with a name and enters its settings |
| `network 192.168.10.0 255.255.255.0` | Sets the subnet the pool gives addresses from |
| `default-router 192.168.10.1` | Sets the default gateway given to devices |
| `show ip dhcp binding` | Lists the addresses handed out and which device received each |
| `show ip dhcp pool` | Shows each pool's address range and how many addresses are in use |

## WAN Links and OSPF
Sets the WAN link IP, then turns on OSPF so routers learn each other's networks automatically. BR-R1 use the same commands with their own values (see the table after the commands).

HQ-R1
```
int gi0/0/1
 desc Link to ISP-R1
 ip addr 10.0.0.1 255.255.255.252
 no shutdown
 exit
router ospf 1
 router-id 1.1.1.1
 passive-interface default
 no passive-interface gi0/0/1
 network 192.168.10.0 0.0.0.255 area 0
 network 192.168.20.0 0.0.0.255 area 0
 network 192.168.30.0 0.0.0.255 area 0
 network 10.0.0.0 0.0.0.3 area 0
```

ISP-R1
```
conf terminal
int gi0/0/0
 description Link to HQ-R1
 ip address 10.0.0.2 255.255.255.252
 no shutdown
 exit
int gi0/0/1
 desc Link to BR-R1
 ip address 10.0.0.5 255.255.255.252
 no shutdown
 exit
int gi0/0/2
 desc Link to Internet-SRV
 ip addr 203.0.113.1 255.255.255.0
 no shutdown
 exit
conf terminal
router ospf 1
 router-id 2.2.2.2
 passive-interface default
 no passive-interface gi0/0/0
 no passive-interface gi0/0/1
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.4 0.0.0.3 area 0
 network 203.0.113.0 0.0.0.255 area 0
 end
```

| Command | Description |
|---|---|
| `ip address 10.0.0.1 255.255.255.252` | Sets the interface's IP address and subnet mask |
| `router ospf 1` | Starts OSPF. The `1` is a local ID number and does not need to match other routers |
| `router-id 1.1.1.1` | Sets the router's unique name in OSPF |
| `passive-interface default` | Stops the router from sending OSPF messages on every interface |
| `no passive-interface gi0/0/1` | Allows OSPF messages on the link to the next router |
| `network 192.168.10.0 0.0.0.255 area 0` | Turns on OSPF for interfaces in that range and advertises the network to other routers |
| `show ip ospf neighbor` | Lists neighbor routers and their state |
| `show ip route ospf` | Shows only the routes learned through OSPF |

| Router | WAN interface IPs | Router ID | Networks advertised |
|---|---|---|---|
| HQ-R1 | Gi0/0/1: 10.0.0.1/30 | 1.1.1.1 | 192.168.10.0, 192.168.20.0, 192.168.30.0, 10.0.0.0/30 |
| ISP-R1 | Gi0/0/0: 10.0.0.2/30, Gi0/0/1: 10.0.0.5/30, Gi0/0/2: 203.0.113.1/24 | 2.2.2.2 | 10.0.0.0/30, 10.0.0.4/30, 203.0.113.0/24 |
| BR-R1 | Gi0/0/1: 10.0.0.6/30 | 3.3.3.3 | 192.168.40.0, 192.168.50.0, 10.0.0.4/30 |

## NAT (HQ-R1)
PAT (NAT overload): PCs share the router's WAN address when reaching the Internet server. Traffic between the company's own sites is not translated. BR-R1 uses the same commands with its own interfaces (Gi0/0/0.40 and .50 inside, Gi0/0/1 outside).

HQ-R1
```
int gi0/0/0.10
 ip nat inside
 exit
int gi0/0/0.20
 ip nat inside
 exit
int gi0/0/0.30
 ip nat inside
 exit
int gi0/0/1
 ip nat outside
 exit
ip access-list extended NAT-TRAFFIC
 deny ip 192.168.0.0 0.0.255.255 192.168.0.0 0.0.255.255
 permit ip 192.168.0.0 0.0.255.255 any
 exit
ip nat inside source list NAT-TRAFFIC int gi0/0/1 overload
```

| Command | Description |
|---|---|
| `ip nat inside` | Marks an interface as facing the company's own network |
| `ip nat outside` | Marks an interface as facing the ISP |
| `ip access-list extended NAT-TRAFFIC` | Creates a named list of rules that decides which traffic NAT translates |
| `deny ip 192.168.0.0 0.0.255.255 192.168.0.0 0.0.255.255` | Do not translate traffic from a private network to another private network (between the company's sites) |
| `permit ip 192.168.0.0 0.0.255.255 any` | Translate traffic from private networks to anywhere else |
| `ip nat inside source list NAT-TRAFFIC interface gi0/0/1 overload` | Replaces the private address of matching traffic with the router's `Gi0/0/1` address. `overload` lets many devices share that one address |
| `show ip nat translations` | Lists the active translations |
| `show ip nat statistics` | Shows the inside and outside interfaces and translation counts |
| `clear ip nat translation *` | Removes all current translations |
