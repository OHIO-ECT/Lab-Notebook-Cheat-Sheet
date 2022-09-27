# ECT/ITS Lab Notebook Cheatsheet

### ECT/ITS IP Networking Convention
The last usable IP in a network is the gateway.

### VyOS Config

##### VyOS 1.3.1 User Guide - https://docs.vyos.io/en/equuleus - it is really good and not too long.

Switch into configuration mode.

```
config
```

Show Current Configuration

```
show
```

Push the configuration to the interfaces

```
commit
```

Configure Interfaces

```
set interfaces ethernet eth1 address 132.235.160.X/28
```

Default Route - This only needs to be done **once per router.**

```
set protocols static route 0.0.0.0/0 next-hop 132.235.160.14
```

Static Route Update

```
set protocols static route 100.70.111.64/26 next-hop 100.70.111.241
```

DNS Servers

````
set system name-server 132.235.9.75
set system name-server 132.235.200.41
````

DHCP Server
````
set service dhcp-server shared-network-name SUB1_POOL subnet 192.168.12.0/24 range 0 start 192.168.12.1
set service dhcp-server shared-network-name SUB1_POOL subnet 192.168.12.0/24 range 0 stop 192.168.12.50
set service dhcp-server shared-network-name SUB1_POOL subnet 192.168.12.0/24 default-router 192.168.12.254
set service dhcp-server shared-network-name SUB1_POOL subnet 192.168.12.0/24 name-server 132.235.9.75
set service dhcp-server shared-network-name SUB1_POOL subnet 192.168.12.0/24 name-server 132.235.200.41
set service dhcp-server shared-network-name SUB1_POOL subnet 192.168.12.0/24 lease 120
````
Show DHCP Leases
````
show dhcp server leases
````

Network Address Translation
````
set nat source rule 100 outbound-interface eth0
set nat source rule 100 source address 172.X.Y.Z/S
set nat source rule 100 translation address masquerade
````

VyOS VLAN Interfaces

````
set interfaces ethernet eth0 vif 310 address 132.235.160.1/28
set interfaces ethernet eth0 vif 100X address 192.168.11.254/24
````

IPv6 Config Interface

```
set interfaces ethernet eth1 address 2610:a8:4831:3XY::1/64
```

IPv6 Static Routing

```
set protocols static route6 ::/0 next-hop 2610:a8:4831:3XY::1
```

Show IPv6 Route table

```
show ipv6 route
```

Config Name Server

```
set system name-server 2610:a8:4831:310::1
```

Configure IPv6 RA

````
set service router-advert interface ethY prefix 2610:a8:483f:XXXX::/64
set service router-advert interface ethY name-server 2001:4860:4860::8888
````
Advanced Config Commands
```
show system | commands
```

Ubuntu-CLI
````
sudo nmcli general hostname <PICK A HOSTNAME>
sudo nmcli con mod "Wired connection 1" ipv4.addresses X.X.X.X/Y
sudo nmcli con mod "Wired connection 1" ipv4.gateway X.X.X.G
sudo nmcli con mod "Wired connection 1" ipv4.dns D.D.D.D,S.S.S.S
sudo nmcli con mod "Wired connection 1" ipv4.method manual
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"

````

## **IPv4 Quick Subnetting Table** ## 
|     |               |      |
|-----|---------------|------|
| /22 | 255.255.252.0 | 1024 |
| /23 | 255.255.254.0 | 512 |
|**/24**| **255.255.255.0**| **256** |
| /25 | 255.255.255.128 | 128 |
| /26 | 255.255.255.192 | 64 |
| /27 | 255.255.255.224 | 32 |
| /28 | 255.255.255.240 | 16 |
| /29 | 255.255.255.248 | 8 |
| /30 | 255.255.255.252 | 4 |
| /31 | 255.255.255.254 | 2 |
| /32 | 255.255.255.255 | 1 |
