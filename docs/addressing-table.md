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
