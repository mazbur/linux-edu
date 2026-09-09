## Understandin Network and its configuration

Networking really is just two questions:
1. How does computer know where to send data?
2. How does the receiving computer know, what it received?

The first question is easy, its the address of another computer. In literal terms the IPv4 or IPv6 address of the computer.
Every device has a unique one, in its network, whether it be LAN, WAN, or the Internet.
For the second question we have what we call the network model to understand it. It includes the Physical Layer, 
Data Link layer, Network Layer and the application layer, on the high level basis.

#### Network Basics

Lets say we have a LAN(Local Area Network) of 5 computers and 1 Router. Actually it can be any device with a Network
Interface not just a computer so we'll refer to them as Hosts. Router stores/knows the IP address of every host. The
Router is also connected with other neteworks and their routers. When one the hosts on this network wants to send
data to another host, it establishes a connection with that host using the router. Router knows address of both the 
hosts. If someone from outside the LAN want to communicate with a host on this LAN, router again recieves the signal
to establish connection. Thats the basics of networking.

#### Packets

The way information or data is traffered between two hosts/computers is known as Packets. Packets are of different 
size based on the IP version and the size/type of data. A long stream of data is also split into packets, with
each packet containing the source IP address and destination IP address so none of them get lost. They also contain
additional info apart from the data itself, such the info required when these packets will again be merged at
destination.

#### Network Layers

To understand any networking system, it is very important to understand Network Layers. Usually people learn this
through the OSI Model but here we already have a practical implemention in Linux, so we'll learn that

* Application Layer
Contains Language that applications and servers use to communicate. High Leve Protocol. Common Examples are HTTP, TLS,
FTP. They can also be combined ,example; HTTP + TLS = HTTPS.

* Transport Layer
Deals with data transmission characterstics of the application layer. Includes data intergrity checking, creating packets
at both source and destination(can also be done in network layer), checking ports. TCP and UDP are most commonly used
Transport Layer Protocols.

```
In Linux all Layers after Transport and including Transport are handled by the kernel, with very few exceptions.
```

* Network Layer / Internet Layer
Defines how to move packets between source and destination. Most popular protocol is Internet Protocol(IP) with v4 and
later v6 as well. 

* Physical Layer
Deals with sending raw data using hardware across physical medium. Using Fiber Optics, cable. Popular medium/protocol
is Ethernet.

#### Internet Layer

Internet right now is based on two protocols, IP v4 and IPv6. Each host has one IPv4 address, in the form of `a.b.c.d`.
Here each letter represents 8 bits or 1 byte. So each letter can have values ranging from 0 to 255. This also means
that there are only ~4 billion, which is less than the number of people today and even less than the number of hosts.
There are different methods like NAT gateway and private networks to resolve this. 

To view IP adress of your computer use
```bash
ip address show
```
```
2: enp0s31f6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state
UP group default qlen 1000
 link/ether 40:8d:5c:fc:24:1f brd ff:ff:ff:ff:ff:ff
 inet 10.23.2.4/24 brd 10.23.2.255 scope global noprefixroute enp0s31f6
 valid_lft forever preferred_lft forever
```
There will be lot of different outputs. This includes detains from physical layer, transport layer and some other stuff
too.

When we talked about LAN previously, it was an example of a subnet. Internet today is a big collection of subnets all
around world. In a subnet there can be n number of hosts with each using a unique IP address. You define subnet using
two pieces, a *network prefix* and *subnet mask*. Lets say you want to create a subnet of IP address from
`10.23.2.1` to `10.23.2.254`. The common part will *network prefix* ie. `10.23.2.0` and *subnet mask* will be
`255.255.255.0`. The entire subnet can be denoted as `10.23.2.0/255.255.255.0`.

Now this can also be written as `10.23.2.0/24`. This is called the **Classless Inter-Domain Routing(CIDR)** notation.
A *subnet mask* is nothing but leading 1s with 0s at the end. So instead of writing the whole *subnet mask* we just
denote it by the number of leading 1s. 

```
Long form        CIDR form
255.0.0.0           /8
255.255.0.0         /16
255.240.0.0         /12
255.255.255.0       /24
255.255.255.192     /26
```

#### Routes and Kernel Routing Table



