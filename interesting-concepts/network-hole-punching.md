# Network hole punching

The standard method for network hole punching involves a relay server that bridges the connections between 2 remote machines behind different NAT:

{% embed url="https://www.geeksforgeeks.org/computer-networks/nat-hole-punching-in-computer-network/" %}

## Experiment

In this experiment, we will be testing a configuration that may potentially force a port forwarding to a LAN device (that we do not control) from a port on the public address, or worst, to a port on the router itself. This may even behind a mobile provider that doesn't explicitly allow port mappings.

This form of exploitation is known as a "NAT injection" attack, which usually involve a weak UPnP implementation: [https://www.akamai.com/site/en/documents/research-paper/upnproxy-blackhat-proxies-via-nat-injections-white-paper.pdf](https://www.akamai.com/site/en/documents/research-paper/upnproxy-blackhat-proxies-via-nat-injections-white-paper.pdf).

Suppose the following situation where an attack is connected to a public wifi (eg. hotel, cafe, etc.), and notices that the admin web interface on the router is exposed with a weak default credential. To avoid suspicion, the attacker can perform a series of steps to attempt to forward the web interface port on the public address of the router (instead of directly accessing from within the LAN). If the router is misconfigured with weak validation, the attacker will be able to remotely access and potentially exploit the web interface.

### Technical details

We will first explore exposing the a port on another LAN device, before moving on the test on the router itself.

The following devices will be involved:

1. Attacker device on LAN (`dev1` )
2. Target device on LAN (`dev2` )

* Device to "force" a port forwarding
* Running a test web server on port 80

3. Remote server

* A server hosted on the internet that we control
* To as the entry point for the port forwarding configurations

{% stepper %}
{% step %}
### Initial outbound TCP

The initial outbound TCP from the attacker machine `dev1` will attempt the following:

a. To spoof the source IP address + port combination to be that of `dev2` and the port where the test web server is running

b. Send the connection to the remote server at a certain port (eg. 8888)

* The remote server will run a `tcpdump` or equivalent tool to capture the packet (more information can be found in the later steps)
* Note that the remote server will not actually run a listener on the port. Hence, the TCP 3-way handshake should not complete

```
(dev2_addr:80) -> (remote_addr:8888)
```

* This should create a NAT entry on the router:
  * src\_ip + port: dev2\_addr:80
    * The NAT will change the source IP + port to its own public address and a random port number (eg. 9999)
  * dest\_ip + port: remote\_addr:8888
{% endstep %}

{% step %}
### Remote server receives the TCP connection (inspect router public address + src port)

The remote server will not have any listeners on port 8888, but instead run a `tcpdump` to identify the source IP + port of the TCP connection.&#x20;


{% endstep %}

{% step %}
### Send a new HTTP request to dev2 via the router public address + found port

The source value found in the previous step will be used as the destination IP + port to send a fresh HTTP request, with destination being the public address of the router + port which the initial TCP connection is sent from (retrieved from the `tcpdump`).

Since there is an active NAT entry on the router, if there are no validations involved such as:

a. Expecting a TCP ACK in response to the initial TCP SYN (from the attacker machine)

b. Or validation of the TCP parameters such as sequence or acknowledgement numbers&#x20;

this request is expected to be accepted, and routed by the NAT to:

```
(router_public_addr:9999) -> (dev2_addr:80) 
```

If successful, the HTTP request will be accepted by `dev2`, allowing the remote server to access the web server from the public address of the router.
{% endstep %}

{% step %}
### Keep the NAT entry alive

In order to keep the NAT entry active and accepting inbound connections, the remote server can be configured to open a raw TCP connection to the forwarded port on the router public address (which maps to the web server on `dev2`), and send periodic keep-alive pings.&#x20;

Again, if the router have weak validation, this may trick the router in thinking that the NAT entry is still active (valid traffic is being transferred), and persist the NAT mapping.
{% endstep %}
{% endstepper %}

### Forwarding router LAN port

The series of steps discussed above can be replicated with the initial TCP connection having the source IP address as the router LAN IP instead (eg. 192.168.1.254). If the router have weak validation against this form of attack, an attacker may able to forward the admin interface (eg. port 80) of the router, and make it accessible via the public address of the router.

### Technical details

...pending tests





