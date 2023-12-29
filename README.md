# ECT/ITS Lab Notebook Cheatsheet

## ITS Network Conventions (aka Local Best Practices)
These conventions MUST be used for IP address assignment. These conventions will be in effect for all assignments for the entire semester.
- A. Default Gateway (Router) will use the last usable address in the IP Network
- B. All other static assigned IPs (including other routers that are not the default gateway) start at the beginning of the range
- C. DHCP pools are between the statically addressed clients and the Default Gateway
- D. Unless indicated otherwise use the following DNS Name servers: 132.235.9.75, 132.235.200.41

### Wireshark
Display Filter
```
ip.addr==X.X.X.X
```
#### Common Network Diagnostic Commands
- `ipconfig /all` - [Windows] or `nmcli` [Ubuntu] Used to display basic network card configuration information
- `ping` - Used to check if a host is reachable across a network or to display the round-trip time for information traveling to or from the host
- `traceroute` [Ubuntu] or `tracert` [Windows] - Used to display the IP addresses and/or host names of all the routers between your host and another host, including round trip times to each of the intermediate routers. Nearly always use the -d [Windows] or -n option [Ubuntu] to disable DNS resolution.
- `netstat` [Windows] or `ss` - [Ubuntu] Used to display the Network status information for your particular host operating system. This can include existing network connections as well as routing information (i.e. how to reach other hosts if more than one network interface exists on the host)
- `tnc` [Windows/PowerShell] aka Test Network Connection has many of the features of ipconfig, Ping and Traceroute in one app. For a list of features use `man tnc` (Note: ONLY works in a PowerShell window).
- `dig` - Used to convert host names to IPv4 addresses or vice-versa. Web version: http://digwebinterface.com
- `nslookup` - Also used to convert hostnames to IPv4 addresses. A deprecated version of this command still exists in most major operating systems
- `Resolve-DNSName` [Windows/PowerShell] converts hostnames to IPs 

### VyOS Config

##### VyOS 1.3.X User Guide - https://docs.vyos.io/en/equuleus - it is really good and not too long.

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

Set Hostname
```
set system host-name VyOS-1
```

Configure Interfaces

```
set interfaces ethernet eth0 address 132.235.160.X/28
set interfaces ethernet eth0 description WAN
```

Show IPv4 Route table

```
show ip route
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
set nat source rule 100 source address 172.X.Y.Z/S
set nat source rule 100 outbound-interface eth0
set nat source rule 100 translation address masquerade
````

VyOS VLAN Interfaces

````
set interfaces ethernet eth0 vif 310 address 132.235.160.1/28
set interfaces ethernet eth0 vif 100X address 192.168.11.254/24
````

VyOS VPN Sample Commands
```
set interfaces openvpn vtun1 local-address <VyOS-1 vtun1 IP> subnet-mask <VyOS-1 vtun1 mask>
set interfaces openvpn vtun1 local-host 10.101.100.X
set interfaces openvpn vtun1 local-port 1195
set interfaces openvpn vtun1 mode site-to-site
set interfaces openvpn vtun1 remote-address <VyOS-2 vtun1 IP>
set interfaces openvpn vtun1 remote-host 132.235.160.X
set interfaces openvpn vtun1 remote-port 1195
set interfaces openvpn vtun1 shared-secret-key-file /config/auth/openvpn-1.key
set interfaces openvpn vtun1 protocol udp
set interfaces openvpn vtun1 persistent-tunnel
```

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

#Optional IPv6 configurations
sudo nmcli con mod "Wired connection 1" ipv6.addresses 2610:a8:483f:m::n/64
sudo nmcli con mod "Wired connection 1" ipv6.gateway 2610:a8:483f:m::1
sudo nmcli con mod "Wired connection 1" ipv6.dns 2610:a8:483f:300::10,2610:a8:483f:300::11
sudo nmcli con mod "Wired connection 1" ipv6.method manual
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
````
#### IPv4 Quick Subnetting Table
| Slash   | Dotted Decimal    | Total IPs
|---      |--                 |--
| /22     | 255.255.252.0     | 1024
| /23     | 255.255.254.0     | 512
|**/24**  | **255.255.255.0** | **256**
| /25     | 255.255.255.128   | 128
| /26     | 255.255.255.192   | 64
| /27     | 255.255.255.224   | 32
| /28     | 255.255.255.240   | 16
| /29     | 255.255.255.248   | 8
| /30     | 255.255.255.252   | 4
| /31     | 255.255.255.254   | 2
| /32     | 255.255.255.255   | 1


#### Vernacular and Definitions

- **Host** - A computer or other device that generates packets and transmits them to other hosts on a network (LAN or WAN). Hosts often include clients and servers that send or receive data, services and applications. Hosts are not network devices like switches and routers, which are categorized as network nodes.

- **GNS3** - GNS3 is application that run in the gHost (like MS-Word runs in/on Windows). It allows the combination of virtual and real devices, used to simulate complex networks. It can also be used to experiment features or to check configurations that need to be deployed later on real devices.

- **Virtual Machine** - A virtual machine (VM) is an emulation of a computer system. Virtual machines are based on computer architectures and provide functionality of a physical computer. Their implementations may involve specialized hardware, software, or a combination.

- **Command Line Interface** - A command-line interface (CLI) processes commands to a computer program in the form of lines of text. The program which handles the interface is called a command-line interpreter or command-line processor. Operating systems implement a command-line interface in a shell for interactive access to operating system functions or services. Such access was primarily provided to users by computer terminals starting in the mid-1960s, and continued to be used throughout the 1970s and 1980s on VAX/VMS, Unix systems and personal computer systems including MS-DOS, CP/M and Apple DOS.

- **Graphical User Interface** - A graphical user interface (GUI) is a user interface that includes graphical elements, such as windows, icons and buttons. The term was created in the 1970s to distinguish graphical interfaces from text-based ones, such as command line interfaces. However, today nearly all digital interfaces are GUIs.

- **Network Interface Card** - A network interface controller (NIC, also known as a network interface card, network adapter, ethernet card, LAN adapter or physical network interface, and several other similar terms) is the Layer 1 computer hardware component that connects a computer to a computer network (can be wired or wireless).

- **Secure Shell (SSH)** - Secure Shell (SSH) is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH. SSH provides a secure channel over an unsecured network by using a client–server architecture, connecting an SSH client application with an SSH server. The protocol specification distinguishes between two major versions, referred to as SSH-1 and SSH-2. The standard TCP port for SSH is 22. SSH is generally used to access Unix-like operating systems, but it can also be used on Microsoft Windows. Windows 10 uses OpenSSH as its default SSH client and SSH server.


