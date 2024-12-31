---
description: >-
  This method uses HTTPS with TLS encryption to encapsulate SSH traffic. This
  allows SSH traffic to appear as if it's HTTPS traffic, and can be useful to
  bypass firewalls that block port 22 (SSH).
---

# SSH Over HTTPS

server: apache, nginx \
client: corkscrew



<mark style="color:red;">**\*\* TO CONFIRM WITH A PRACTICAL EXPERIMENT and further develop the documentations**</mark>

### <mark style="color:green;">Firewalls with destination port 22 restriction only</mark>

Firewalls can be configured to block SSH traffic by the default destination port number 22. Thus, by changing the port that the SSH server listens to, these types of firewalls can be potentially bypassed.&#x20;

**Server side**

The `ListenAddress` value can be used to change the listen address on the SSH server. This configuration can be done by editing the _**/etc/ssh/sshd\_confi**_&#x67; file.

The allowed format for the `ListenAddress` value configuration ([https://linux.die.net/man/5/sshd\_config](https://linux.die.net/man/5/sshd_config)):

1. ListenAddress host|IPv4\_addr|IPv6\_addr
2. ListenAddress host|IPv4\_addr:port
3. ListenAddress \[host|IPv6\_addr]:port

```bash
$ cat /etc/ssh/sshd_config

# eg. to listen on all interfaces at port 4000
ListenAddress <listen_address>:<new_port_for_ssh_server_to_listen> 
```

**Client side**

```bash
$ ssh -p <new_port_for_ssh_server_to_listen> user@ssh_server_address
```

### Example

Suppose I want my SSH server to listen on port _22888._&#x20;

<pre class="language-bash"><code class="lang-bash"><strong># SSH server
</strong><strong>
</strong><strong>$ cat /ssh/sshd_config
</strong># eg. to listen on all interfaces at port 4000
ListenAddress 0.0.0.0:22888 # *** REQUIRE TESTING
</code></pre>

```bash
# client

$ ssh -p 22888 user@ssh_server_address
```

### <mark style="color:green;">Firewalls with destination port 22 restriction & deep packet inspection</mark>

Some firewalls may implement deep packet inspection to identify and block SSH traffic by other methods aside from the destination port number. A method to enable SSH traffic over HTTPS can be utilized to potentially bypass the firewalls.

The SSH over HTTPS is essentially the same as normal HTTPS with HTTP headers such as <mark style="color:blue;">Host</mark>, <mark style="color:blue;">User-Agent,</mark> <mark style="color:blue;">Content-Length</mark>, etc. with the exception that the body will include SSH data rather than the usual Javascript/HTML content

**Client side**: Tools such as corkscrew is required to establish TLS connection: with the handshake and other processes.

**Server side**: apache/nginx server required to configure the server to listen over HTTPS, before routing the SSH traffic to port 22 (where the actual SSH server is running)

Corkscrew example from _ChatGPT_

```bash
ssh -o "ProxyCommand=corkscrew your.server.com 443 %h %p" username@localhost
```
