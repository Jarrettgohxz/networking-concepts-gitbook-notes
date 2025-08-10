# Network hole punching

The standard method for network hole punching involves a relay server that bridges the connections between 2 remote machines behind different NAT:

{% embed url="https://www.geeksforgeeks.org/computer-networks/nat-hole-punching-in-computer-network/" %}

### Experiment

In this experiement, I will be testing a different configuration that potentially forces a remote port forwarding from a remote address to a LAN address, even behind a mobile provider that doesn't explicitly allow port mappings.

The following devices will be involved:

1. Initiating device on LAN (`dev1` )
2. Target device on LAN (`dev2` )

* Running a test web server on port 80

3. Remote server

#### Initial outbound TCP

initial outbound TCP from dev1: (dev2\_addr:80) -> (remote\_addr:8888)

* This should create a NAT entry on the router:
  * src\_ip + port: dev2\_addr:80
  * dest\_ip + port: remote\_addr:8888)
* The NAT will change the source IP + port to its own public address and a random port number

#### Remote server receive the TCP connection

The remote server will not have any listeners on port 8888, but instead run a `tcpdump` to identify the source IP + port of the TCP connection. The source value will be used as the destination IP + port to send a fresh HTTP request, with destinaton being (remote\_addr:8888).

This request is expected to be accepted, and routed by the NAT to  (dev2\_addr:80).

