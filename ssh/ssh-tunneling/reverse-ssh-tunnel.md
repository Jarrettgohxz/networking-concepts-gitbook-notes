---
description: >-
  The remote port forwarding capability (also known as reverse tunneling)
  provided by SSH is opposite to that of the direct SSH tunnel.
---

# Reverse SSH tunnel

> A reverse SSH tunnel, also known as remote port forwarding, allows the creation of a tunnel that redirects traffic from a remote server and port, to a specified port on the local machine, via an SSH connection.

<mark style="color:orange;">Note</mark>: The terms _reverse tunnel_ and _remote port forwarding_ will be used interchangeably.&#x20;

Reverse tunneling is enabled with the `-R` flag, with the supplied values: `[remote_address:]remote_port:local_address:local_port`

```bash
$ ssh ... -R [remote_address]:<remote_port>:<local_address>:<local_port>

# Example: bind remote port 8888 on all addresses on the remote side (0.0.0.0) to the localhost (127.0.0.1) port 6666
$ ssh ... -R 0.0.0.0:8888:127.0.0.1:6666
$ ssh ... -R 8888:127.0.0.1:6666 # notice that the <remote_port> can be omitted (default to 0.0.0.0)
```

### Special use case (reverse dynamic port forwarding)

{% embed url="https://www.openssh.com/txt/release-7.6" %}
Under "New Features" -> "add support for reverse dynamic port forwarding ..."
{% endembed %}

{% embed url="https://unix.stackexchange.com/a/409535" %}

> According to the release notes, it is implemented in OpenSSH 7.6

```sh
ssh ... -R <listen_port> 
```

* Providing a single port value to the `-R` option allows the initiating client to act as a proxy for any connections that arrives from the SSH tunnel
* any traffic sent to the localhost address at the supplied `<listen_port>` on the remote server (which received the SSH connection) will be proxied through the client

```sh
# eg. from client server (initiating the request)
$ ssh user@remote -R 9999 -N

# from server running SSH 
$ lsof -i -P -n | grep 9999
... ... xxxx:9999

$ curl -x socks5://localhost:9999 ifconfig.me
...address of the client server will be returned
```

#### Alternative method to try

> The following has not been tested

{% code title="client -> server" overflow="wrap" %}
```sh
$ ssh -v -D 1080 -R 127.0.0.1:8888:localhost:1080 user@1.1.1.1 -N
```
{% endcode %}

* TCP request to port 8888 on server -> port 1080 on client -> ??

...&#x20;

### Example

The SSH reverse tunnel can be utilized to expose a locally running service to the internet (without the need to manually setup port forwarding on the gateway).&#x20;

For example, suppose there is a workstation running certain services on port 3389 (Remote Desktop Protocol) and 22 (SSH) that is required to be accessed from the internet. However, this workstation is behind a managed secure network that he/she is not in direct control of (eg. school campus network), and it is not practical to approach the network administrator to enable port forwarding on the internet-facing gateway. Along with additional access to a remote SSH server, a reverse tunnel can be used in this case.&#x20;

Let's assume the situation:

1. <mark style="color:blue;">Machine A</mark>: remote SSH server (eg. AWS EC2) with a public address of 11.11.11.11 present on a network environment where port forwarding rules can be added on the internet-facing gateway
2. <mark style="color:purple;">Machine B</mark>: workstation (behind the managed network) running RDP/SSH on port 3389/22 respectively
3. <mark style="color:yellow;">Machine C</mark>: client on the internet to access the services on <mark style="color:blue;">machine A</mark>



<mark style="color:blue;">**Machine A**</mark>

1. Enable and start the SSH daemon (SSHD) is running:

```bash
$ sudo systemctl enable sshd 
$ sudo systemctl start sshd
```

2. Enable _GatewayPorts_ (not the safest method \~ continue reading about the commands for <mark style="color:yellow;">machine C</mark> for more information)

**Port forwarding requirements on the internet-facing gateway**

Let's assume beforehand that the entry ports to the reverse tunnel on <mark style="color:blue;">machine A</mark> are 8888 and 6666, that will be routed to ports 3389 and 22 respectively. In this case, a port forwarding rule is needed to be made on the internet-facing gateway: mapping 2 ports of choice on the public address (assume 8866 and 6688), to local ports 8888 and 6666 respectively as follows:

\~ Assuming the LAN address of <mark style="color:blue;">machine A</mark> is 192.168.1.80

* 11.11.11.11:8866 -> 192.168.1.80:8888 -> 127.0.0.1:8888 (entry point to the reverse tunnel)
* 11.11.11.11:6688 -> 192.168.1.80:6666 -> 127.0.0.1:6666 (entry point to the reverse tunnel)

_<mark style="color:red;">... explanation of why GatewayPorts is needed</mark>_

```bash
$ sudo vim /etc/ssh/sshd_config
...
GatewayPorts clientspecified
...
```



<mark style="color:purple;">**Machine B**</mark>

1. Send a remote port forwarding request to <mark style="color:blue;">machine A</mark>

<mark style="color:orange;">Note</mark>: Basic SSH command flags (such as -i) will be omitted, only the relevant one will be included:

```bash
$ ssh -R 8888:127.0.0.1:3389 -R 6666:127.0.0.1:22 -N user@11.11.11.11
```

This command bind the ports  8888 and 6666 on 11.11.11.11 (<mark style="color:blue;">machine A</mark>) to the localhost address on port 8888 and 6666 on <mark style="color:purple;">machine B</mark>, respectively. This essentially allows traffic to be sent to the RDP and SSH ports on <mark style="color:purple;">machine B</mark> from the remote forwarded ports on <mark style="color:blue;">machine A</mark> (8888 and 6666).



<mark style="color:yellow;">**Machine C**</mark>

1. Suppose the _GatewayPorts_ setting is configured correctly, and the public-to-private addresses port forwarding on the internet-facing gateway is configured correctly. From <mark style="color:yellow;">machine C</mark>, the services port on <mark style="color:purple;">machine B</mark> can be accessed via the public address of  <mark style="color:blue;">machine A</mark> at 11.11.11.11 as follows:

* _<mark style="color:green;">RDP</mark>_: 11.11.11.11:8866 (<mark style="color:blue;">machine A</mark>) -> 192.168.1.80:8888 (<mark style="color:blue;">machine A</mark>)  -> 127.0.0.1:8888 (<mark style="color:blue;">machine A</mark>) -> 127.0.0.1:3389 (<mark style="color:purple;">machine B \~ RDP</mark>)&#x20;
* _<mark style="color:green;">SSH</mark>_: 11.11.11.11:6688 (<mark style="color:blue;">machine A</mark>)> 192.168.1.80:6666 (<mark style="color:blue;">machine A</mark>)  -> 127.0.0.1:6666 (<mark style="color:blue;">machine A</mark>) -> 127.0.0.1:22 (<mark style="color:purple;">machine B \~ SSH</mark>)&#x20;

**Eg. Accessing a SSH shell on&#x20;**<mark style="color:purple;">**machine B**</mark>:

```bash
machine-c$ ssh user_at_machine_b@11.11.11.11 -p 6688
```

Notice that the user to connect to is the one on <mark style="color:purple;">machine B</mark>, rather than on <mark style="color:blue;">machine A</mark>.&#x20;



2. Another <mark style="color:orange;">**safer alternative**</mark> to access the services running on <mark style="color:purple;">machine B</mark> is to directly send a local port forwarding connection via SSH to <mark style="color:blue;">machine A</mark>:

```bash
$ ssh -L 8866:127.0.0.1:8888 -L 6688:127.0.0.1:6666 -N user_at_machine_a@11.11.11.11
```

Notice that the user to connect to is the one on <mark style="color:blue;">machine A</mark>, not <mark style="color:purple;">machine B</mark>.&#x20;

The services on <mark style="color:purple;">machine B</mark> can be accessed via localhost ports 8866 and 6688 on <mark style="color:yellow;">machine C</mark> locally instead, without the need of exposing ports 8866 and 6688 on <mark style="color:blue;">machine A</mark> (11.11.11.11) as follows:

* 127.0.0.1:8866 (<mark style="color:yellow;">machine C</mark>) -> 127.0.0.1:8888 (<mark style="color:blue;">machine A</mark>) -> 127.0.0.1:3389 (<mark style="color:purple;">machine B \~ RDP</mark>)&#x20;
* 127.0.0.1:6688 (<mark style="color:yellow;">machine C</mark>) -> 127.0.0.1:6666 (<mark style="color:blue;">machine A</mark>) -> 127.0.0.1:22 (<mark style="color:purple;">machine B \~ SSH</mark>)&#x20;



Thus, the port forwarding rules on the internet-facing gateway in <mark style="color:blue;">machine A</mark>'s network environment can be disabled, along with _GatewayPorts:_

```bash
$ sudo vim /etc/ssh/sshd_config
...
GatewayPorts no
...
```

**Overview of benefits of this method**:

1. Prevent over-exposure of public facing ports on <mark style="color:blue;">machine A</mark> without a proper authentication mechanism - anyone on the internet can access the ports and gain access to the services running on <mark style="color:purple;">machine B</mark>. This can lead to unauthorized access, assuming that IP whitelisting is not enabled.
2. Only port 22 (SSH) <mark style="color:blue;">machine A</mark> will be exposed to the internet. This provides an authentication mechanism that is provided by SSH

**Eg. Accessing a SSH shell on&#x20;**<mark style="color:purple;">**machine B**</mark>:

```bash
$ machine-c$ ssh user_at_machine_b@localhost -p 6666
# OR
$ machine-c$ ssh user_at_machine_b@127.0.0.1 -p 6666
```

Notice that the user to connect to (supplied as the _user_ value in the format: `<user>@address`) is the one on <mark style="color:purple;">machine B</mark>, rather than on <mark style="color:blue;">machine A</mark>.&#x20;
