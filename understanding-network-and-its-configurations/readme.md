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

