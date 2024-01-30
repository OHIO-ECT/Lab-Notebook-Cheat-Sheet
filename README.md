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
sudo nmcli con mod "Wired connection 1" ipv6.method auto
sudo nmcli con mod "Wired connection 1" ipv6.addresses 2610:a8:x:y::n/64
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

- **GNS3** - GNS3 is application that run in the gHost (like MS-Word runs in Windows). It allows the combination of virtual and real devices, used to simulate complex networks. It can also be used to experiment features or to check configurations that need to be deployed later on real devices.

- **Virtual Machine** - A virtual machine (VM) is an emulation of a computer system. Virtual machines are based on computer architectures and provide functionality of a physical computer. Their implementations may involve specialized hardware, software, or a combination.

- **Command Line Interface** - A command-line interface (CLI) processes commands to a computer program in the form of lines of text. The program which handles the interface is called a command-line interpreter or command-line processor. Operating systems implement a command-line interface in a shell for interactive access to operating system functions or services. Such access was primarily provided to users by computer terminals starting in the mid-1960s, and continued to be used throughout the 1970s and 1980s on VAX/VMS, Unix systems and personal computer systems including MS-DOS, CP/M and Apple DOS.

- **Graphical User Interface** - A graphical user interface (GUI) is a user interface that includes graphical elements, such as windows, icons and buttons. The term was created in the 1970s to distinguish graphical interfaces from text-based ones, such as command line interfaces. However, today nearly all digital interfaces are GUIs.

- **Network Interface Card** - A network interface controller (NIC, also known as a network interface card, network adapter, ethernet card, LAN adapter or physical network interface, and several other similar terms) is the Layer 1 computer hardware component that connects a computer to a computer network (can be wired or wireless).

- **Secure Shell (SSH)** - Secure Shell (SSH) is a cryptographic network protocol for operating network services securely over an unsecured network. Typical applications include remote command-line, login, and remote command execution, but any network service can be secured with SSH. SSH provides a secure channel over an unsecured network by using a client–server architecture, connecting an SSH client application with an SSH server. The protocol specification distinguishes between two major versions, referred to as SSH-1 and SSH-2. The standard TCP port for SSH is 22. SSH is generally used to access Unix-like operating systems, but it can also be used on Microsoft Windows. Windows 10 uses OpenSSH as its default SSH client and SSH server.

- **Router** - A router is a networking device that forwards data packets between computer networks. Routers perform the traffic directing functions on the Internet. Packets are typically forwarded from one router to another router through the networks that constitute an internetwork (e.g. the Internet) until it reaches its destination host.

- **Switch** - A network switch (also called switching hub, bridging hub, and, by the IEEE, MAC bridge) is networking hardware that connects devices on a computer network by using packet switching to receive and forward data to the destination device.

- **Firewall** - A firewall is a network security device that monitors incoming and outgoing network traffic and decides whether to allow or block specific traffic based on a defined set of security rules. Firewalls have been a first line of defense in network security for over 25 years. They establish a barrier between secured and controlled internal networks that can be trusted and untrusted outside networks, such as the Internet. A firewall can be hardware, software, or both.

- **Domain Name System (DNS)** - The Domain Name System (DNS) is a hierarchical and decentralized naming system for computers, services, or other resources connected to the Internet or a private network. It associates various information with domain names assigned to each of the participating entities. Most prominently, it translates more readily memorized domain names to the numerical IP addresses needed for locating and identifying computer services and devices with the underlying network protocols. By providing a worldwide, distributed directory service, the Domain Name System has been an essential component of the functionality of the Internet since 1985.

- **Dynamic Host Configuration Protocol (DHCP)** - The Dynamic Host Configuration Protocol (DHCP) is a network management protocol used on Internet Protocol (IP) networks, whereby a DHCP server dynamically assigns an IP address and other network configuration parameters to each device on the network, so they can communicate with other IP networks. A DHCP server enables computers to request IP addresses and networking parameters automatically from the Internet service provider (ISP), reducing the need for a network administrator or a user to manually assign IP addresses to all network devices. In the absence of a DHCP server, a computer or other device on the network needs to be manually assigned an IP address, or to assign itself an APIPA address, which will not enable it to communicate outside its local subnet.

- **Network Address Translation (NAT)** - Network address translation (NAT) is a method of remapping an IP address space into another by modifying network address information in the IP header of packets while they are in transit across a traffic routing device. The technique was originally used as a shortcut to avoid the need to readdress every host when a network was moved. It has become a popular and essential tool in conserving global address space in the face of IPv4 address exhaustion. One Internet-routable IP address of a NAT gateway can be used for an entire private network.

- **Virtual Local Area Network (VLAN)** - A virtual LAN (VLAN) is any broadcast domain that is partitioned and isolated in a computer network at the data link layer (OSI layer 2). LAN is the abbreviation for local area network and in this context virtual refers to a physical object recreated and altered by additional logic. VLANs work by applying tags to network packets and handling these tags in networking systems – creating the appearance and functionality of network traffic that is physically on a single network but acts as if it is split between separate networks. In this way, VLANs can keep network applications separate despite being connected to the same physical network, and without requiring multiple sets of cabling and networking devices to be deployed.

- **Virtual Private Network (VPN)** - A virtual private network (VPN) extends a private network across a public network and enables users to send and receive data across shared or public networks as if their computing devices were directly connected to the private network. Applications running across a VPN may therefore benefit from the functionality, security, and management of the private network.

- **Internet Protocol (IP)** - The Internet Protocol (IP) is the principal communications protocol in the Internet protocol suite for relaying datagrams across network boundaries. Its routing function enables internetworking, and essentially establishes the Internet. IP has the task of delivering packets from the source host to the destination host solely based on the IP addresses in the packet headers. For this purpose, IP defines packet structures that encapsulate the data to be delivered. It also defines addressing methods that are used to label the datagram with source and destination information.

- **Internet Control Message Protocol (ICMP)** - The Internet Control Message Protocol (ICMP) is a supporting protocol in the Internet protocol suite. It is used by network devices, including routers, to send error messages and operational information indicating success or failure when communicating with another IP address, for example, an error is indicated when a requested service is not available or that a host or router could not be reached. ICMP differs from transport protocols such as TCP and UDP in that it is not typically used to exchange data between systems, nor is it regularly employed by end-user network applications (with the exception of some diagnostic tools like ping and traceroute).

- **Transmission Control Protocol (TCP)** - The Transmission Control Protocol (TCP) is one of the main protocols of the Internet protocol suite. It originated in the initial network implementation in which it complemented the Internet Protocol (IP). Therefore, the entire suite is commonly referred to as TCP/IP. TCP provides reliable, ordered, and error-checked delivery of a stream of octets (bytes) between applications running on hosts communicating via an IP network. Major internet applications such as the World Wide Web, email, remote administration, and file transfer rely on TCP, which is part of the Transport Layer of the TCP/IP suite. SSL/TLS often runs on top of TCP.

- **User Datagram Protocol (UDP)** - The User Datagram Protocol (UDP) is one of the core members of the Internet protocol suite. The protocol was designed by David P. Reed in 1980 and formally defined in RFC 768. With UDP, computer applications can send messages, in this case referred to as datagrams, to other hosts on an Internet Protocol (IP) network. Prior communications are not required in order to set up communication channels or data paths.

