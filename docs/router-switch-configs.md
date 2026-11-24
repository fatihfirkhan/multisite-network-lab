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
