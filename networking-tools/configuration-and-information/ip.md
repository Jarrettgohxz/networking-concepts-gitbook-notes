# ip

### Useful basic commands

1. **View all network interfaces**

```bash
$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
...

# eg. show specific network interface
$ ip link show test0
4: test0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 32:25:da:4e:93:fa brd ff:ff:ff:ff:ff:ff
...
```

2. **Add a new network interface**

```bash
$ ip link add [iface_name] type [iface_type]

# eg. add a dummy interface with name test0
$ ip link add test0 type dummy

```

3. **Configure IP address on the interface**

```bash
$ ip addr add [ip_addr] dev [iface_name]

# eg.
$ ip addr add 192.168.1.222 dev test0
```

4. **Set the network interface up/down**

```bash
$ ip link set [iface_name] <up/down>

# eg.
$ ip link set test0 up
```

### Example

Create a dummy interface `test0` with IP address value of `192.168.1.222`

```bash
$ ip link add test0 type dummy
$ ip link set test0 down
$ ip addr add 192.168.1.222 dev test0
$ ip link test0 up
$ ip link show test0
...
```

