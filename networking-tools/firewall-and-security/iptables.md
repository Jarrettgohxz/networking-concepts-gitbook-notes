---
description: iptables-persistent, netfilter-persistent
---

# iptables

## Basic options

There are a few basic concepts within iptables:&#x20;

### Tables

Within each table, there are a few available chain options. The values will be listed down under each table, and will be further discussed in a later section.

1. filter (default)
   * INPUT
   * FORWARD
   * OUTPUT
2. nat
   * ...
3. mangle
   * ...
4. raw
   * ...

### Chain

1. INPUT
2. FORWARD
3. OUTPUT

### Target

1. ACCEPT
2. DROP
3. REJECT



## Basic flag options

1. -A OR --append
2. -I OR --insert
3. -D OR --delete
4. -t OR --table
5. -L OR --list
6. -p OR --protocol
7. -P or --policy
8. -j OR --jump
9. -s OR --source
10. -d OR --destination
11. \--sport OR --source-port
12. \--dport OR --destination-port

### Add/insert rules examples

1. Add an _**INPUT**_ rule to _**DROP**_ all traffic regardless of the protocol, destination port, source address, or any other parameters

```bash
$ sudo iptables -A INPUT -j DROP
```

2. Add an _**INPUT**_ rule to _**DROP**_ all SSH traffic (TCP port 22)&#x20;

```bash
$ sudo iptables -A INPUT -p tcp --dport 22 -j DROP
```

An additional rule can be added to allow SSH traffic from a source address range. Note that the `-I`    flag option is used instead of `-A` to insert, rather than append the new rule. This allow sthe new rule to take precedence over the previously defined rule (for matching traffic).

```bash
$ sudo iptables -I INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j ACCEPT
```

### View the rules

```bash
$ sudo iptables -L
$ sudo iptables --list
```

## ~~Persistent rules with iptables-persistent~~

1. ~~Install iptables-persistent~~&#x20;

```bash
$ sudo apt install iptables-persistent
```

1. ~~Update rules with iptables command~~
2. ~~Save to rules file: _**/etc/iptables/rules.v4**_ (For IPv4)~~

```bash
$ sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

## Persistent rules with netfilter-persistent

All the same steps as above for iptables-persistent, with some additional steps:

1. Install netfilter-persistent and iptables-persistent

* seems like installing iptables-persistent automatically adds the current rules in the iptables into the file: _**/etc/iptables/rules.v4**_

```bash
$ sudo apt install netfilter-persistent
$ sudo install iptables-persistent
```

2. Enable netfilter-persistent service

```bash
$ sudo systemctl enable netfilter-persistent
```

Check to ensure that it is enabled. It doesn't matter if its not running yet, as it will be started on reboot as long its enabled.

```bash
$ sudo systemctl status netfilter-persistent
```

3. Verify if the rules have been added to the configuration file: _**/etc/iptables/rules.v4**_

```bash
$ sudo cat /etc/iptables/rules.v4
```

Otherwise, manually add the rules using the _**iptables-save**_ tool

```bash
$ sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

The iptables-save tool simply returns the current iptables rules

```bash
$ sudo iptables-save
...
...
...
```

4. Reboot the system

```bash
$ reboot
```

5. Check iptables rule

```bash
$ sudo iptables --list
$ sudo iptables -L
```

The iptables rules should persist from the previous configurations.

## Example use case
