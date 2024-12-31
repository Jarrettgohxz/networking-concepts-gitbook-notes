---
description: >-
  The SSH Daemon (sshd) is a OpenSSH server process. It listens for incoming
  connections using the SSH protocol and acts as the server.
---

# sshd

Related services includes:**`sshd.service`** and **`ssh.service`**`.`

A daemon is a Linux concept, which allows running a background process without direct intervention from any users. It will start on system boot, and runs until the system is shut down, or when terminated explicitly.

## To identify if the SSH Daemon (sshd) is running

Run either one of the commands below:

```bash
$ sudo systemctl status ssh.service
# OR
$ sudo systemctl status sshd.service
```

The reason for having two commands is that some systems may identify the SSH server process with **ssh.service** alone, while some may identify it with **sshd.service**. However, the SSH server will be running and able to accept connections, as long as either one of the services shown above is indicated to be running.

### Example outputs from systemctl status command

<mark style="color:green;">**SSH server is active (running)**</mark>

```
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; ...)
     Active: active (running) since ...
     ...
```

<mark style="color:red;">**SSH server is inactive (dead)**</mark>

```
○ ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; ...)
     Active: inactive (dead) since ...
     ...
```

### To view the sshd logs

```bash
$ sudo journalctl -u sshd
```

### More information about systemctl and journalctl commands

For more information about the **systemctl** and **journalctl** commands, refer to the Linux docs: [https://jarrettgxz-sec.gitbook.io/linux/](https://jarrettgxz-sec.gitbook.io/linux/)[https://jarrettgxz-sec.gitbook.io/linux/system/systemctl](https://jarrettgxz-sec.gitbook.io/linux/system/systemctl).

## <mark style="color:green;">sshd\_config</mark>

The sshd configuration file usually found in **/etc/ssh/sshd\_config**, can be used to control parameters such as access control, port forwarding, and many more. A brief overview of a few useful fields can be found below. Refer to the sshd\_config manpage at[https://man7.org/linux/man-pages/man5/sshd\_config.5.html](https://man7.org/linux/man-pages/man5/sshd_config.5.html) for more detailed explanations.

### Edit the sshd\_config file

```bash
$ sudo vim /etc/ssh/sshd_config
# OR
$ sudo nano /etc/ssh/sshd_config
```

Remember to restart the **sshd.servic**e or **ssh.service** after any changes have been made to the configuration file.&#x20;

```bash
$ sudo systemctl restart ssh.service
# OR
$ sudo systemctl restart sshd.service
```



A few of the available configuration options I have worked with are listed below:

### AllowTcpForwarding

Specifies if users are allowed to enable TCP port forwarding.

-> Allowed values:&#x20;

1. no
2. yes/all (default)
   * Allows all method of port forwarding: local, remote and dynamic
3. local
   * Only allow local port forwarding
4. remote
   * Only allow remote port forwarding

The default value is **yes/all**

### GatewayPorts

Specifies if remote hosts are allowed to connect to the ports forwarded by the remote port forwarding rule setup by the client. By default, only the localhost is allowed to connect to the forwarded ports.

-> Allowed values:&#x20;

1. no (default)
2. yes
   * To allow port forwarding to be bound to the wildcard address 0.0.0.0
3. clientspecified
   * Client can specify the address to which the forwarding is bound

The default value is **no**

### ForceCommand

Ignores any command supplied by the client. Instead, forces the execution of the command specified to this field as an argument. The command is invoked by using the user's login shell with the `-c`option. This simply executes the supplied command with any additional arguments if present, as a single string.

-> Example values:&#x20;

1. /bin/false or /bin/true
   * These command denies a remote shell
2. /usr/bin/nologin
   * This command denies login

The default value is **none**.

### PasswordAuthentication

Specifies whether password authentication is allowed. This can be used to force connections to be authenticated only with a public-key.

-> Allowed values: yes(default), no

The default value is **yes**.

### Match

Introduces a conditional block. Note that if a keyword is satisfied in multiple **Match** blocks, only the first instance of the keyword is applied.

<mark style="color:blue;">**Match Address**</mark>

1. Example: match rules for subnet range 192.168.1.0/24

Notice that GatewayPorts is set to <mark style="color:red;">**no**</mark> globally. However, this setting is overridden by the match rule for the address range specified, which sets GatewayPorts to <mark style="color:green;">**yes**</mark>.

This enforces the standard policy to disallow gateway ports for all connections, while whitelisting and allowing it for the specified address range.

```xml
# Global security settings
GatewayPorts no
...

# Match rules for specified address
Match Address 192.168.1.*
GatewayPorts yes
...
```

<mark style="color:blue;">**Match User**</mark>

2. Example: match rules for user **notRoot**

Notice that PasswordAuthentication is set to <mark style="color:red;">**no**</mark> globally. However, this setting is overridden by the match rule for the user **notRoot**, which sets PasswordAuthentication to <mark style="color:green;">**yes**</mark>.

This enforces the standard policy to disallow password authentication for all connections, while whitelisting and allowing it for the specified user.

```
# Global security settings
PasswordAuthentication no
...

# Match rules for user "notRoot"
Match User notRoot
PasswordAuthentication yes
...
```

This method essentially allows specific configurations to be applied for a matching rule. This can be useful for whitelisting by different parameters such as address subnet ranges and specified users as shown above in the example. The configurations specified under each rule can be extended to other keyword fields too such as **AllowTcpForwarding**, **ForceCommand**, etc.

