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

### Example 1: Create a dummy interface

Create a dummy interface `test0` with IP address value of `192.168.1.222`

```bash
$ ip link add test0 type dummy
$ ip link set test0 down
$ ip addr add 192.168.1.222 dev test0
$ ip link test0 up
$ ip link show test0
...
```

### Example 2: Reset configurations on interface

```shellscript
# release DHCP
$ dhclient -r <iface>
# flush interface
$ ip addr flush dev <iface>
# set interface DOWN
$ ip link set <iface> down

# retrieve DHCP
$ dhclient <iface>
# set interface UP
$ ip link set <iface> up  
```

### Example 3: Replace default route

Suppose we have an interface `eth0` with a lower metric (preferred) over another interface `eth1`

* Now, all traffic not destined for **192.168.0.0** (`eth1` specific address) will exit via `eth0`, with the default gateway of **192.168.1.1**
* We can run a few commands to make `eth1` the preferred route, to force all traffic to exit via the gateway address **192.168.0.1** instead

> Note that all traffic destined for **192.168.1.0** will still exit via the `eth0` interface

```shellscript
# view routing table
$ route -n 
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.1.1     0.0.0.0         UG    0      0        0 eth0
0.0.0.0         192.168.0.1     0.0.0.0         UG    100    0        0 eth1
192.168.0.0     0.0.0.0         255.255.255.0   U     100    0        0 eth1
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0

# update default route
$ ip route replace default via 192.168.0.1 dev eth1 metric 0

# check route
$ ip route get 1.1.1.1
1.1.1.1 via 192.168.0.1 dev eth1 src 192.168.0.5 uid 1000 
    cache 

$ ip route get 8.8.8.8
8.8.8.8 via 192.168.0.1 dev eth1 src 192.168.0.5 uid 1000 
    cache 

$ ip route get 192.168.1.1
192.168.1.1 dev eth0 src 192.168.1.xxx uid 1000 
    cache 
    
$ ip route get 192.168.1.2
192.168.1.2 dev eth0 src 192.168.1.xxx uid 1000 


```



