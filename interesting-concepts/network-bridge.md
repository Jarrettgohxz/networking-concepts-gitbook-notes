# Network bridge

{% embed url="https://www.geeksforgeeks.org/computer-networks/what-is-bridge-in-computer-network-types-uses-functions-differences/" %}

{% embed url="https://wiki.archlinux.org/title/Network_bridge" %}

{% embed url="http://developers.redhat.com/blog/2018/10/22/introduction-to-linux-interfaces-for-virtual-networking#bridge" %}

{% embed url="https://www.baeldung.com/linux/bridging-network-interfaces" %}

### What is a network bridge?

A network bridge is a networking device in a network that is used to connect multiple LANs to a larger LAN.

### Creating a virtual network bridge

In this setup, we discuss how we can connect 2 interfaces:&#x20;

```sh
# (1) 
sudo ip link add name bridge-iface type bridge # create bridge
sudo ip link set up dev bridge-iface # bring the bridge up

# (2) add host-only interfaces to the bridge (one from VirtualBox and another from VMware)
sudo ip link set dev vboxnet1 master bridge-iface
sudo ip link set dev vmnet1 master bridge-iface

# (3) bring the interfaces up
sudo ip link set up dev vboxnet0
sudo ip link set up dev vmnet8

```

### Verify the bridge configuration

{% code overflow="wrap" %}
```sh
$ ip link show <iface> 
xxx: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ... master <master-iface> # noticen the text master <master-iface>

# OR
$ bridge link
```
{% endcode %}

### Remove master configuration

```sh
$ sudo ip link set dev <iface> nomaster
```

### Create virtual network bridge between 2 host-only VM interfaces

Suppose we want to create a network bridge (on a host OS) to bridge 2 host-only networks (from both VirtualBox and VMware). The method above of simply making the bridge interface the master of the 2 host-only interfaces will not work, since the host-only interfaces are fundamentally isolated, and the layer 2 packets (which the network bridge depends upon) will not leave the respective host-only interfaces.

#### Reading references

{% embed url="https://superuser.com/a/700383" %}

{% embed url="https://superuser.com/questions/272962/virtualbox-vpn-networking-test-environment" %}

#### (1) Routing & forwarding configurations

In this setup, we will have the host OS act as a router, to perform routing based on the IP address of each guest VM. Suppose we have the following configurations:

1. Guest VM 1&#x20;

* VirtualBox&#x20;
* Interface on the host OS (`vboxnet0`): **10.10.10.1**
* IP (`eth0`): **10.10.10.10**



2. Guest VM 2

* VMware
* Interface on the host OS (`vmnet1`): **12.12.12.1**
* IP (`eth1`): **12.12.12.12**



3. Host OS

* Linux, eg. Ubuntu
* Configured network interfaces: `vboxnet0` (**10.10.10.1**) and `vmnet1` (**12.12.12.1**)



**Important note**

The network interface names: `eth1` and `eth1` given to each of the guest VMs simply refers to the local interface. The guest VMs can have the same interface name, without affecting the overall configuration— as it does **NOT** refer to the same network.

#### Configuration steps

**Guest VM**

For the sake of simplicity, let's assume that DHCP is disabled on each of the guest VM. Thus, we have to configure the static IP on the interface manually, followed by adding the appropriate routes:

{% code title="Guest VM 1" %}
```sh
$ sudo ip addr add 10.10.10.10 dev eth0

# route will be empty
$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface

# pinging the gateway will fail
$ ping 10.10.10.1
ping: connect: Network is unreachable


$ sudo ip route add 10.10.10.0 dev eth0 
$ sudo route add default gw 10.10.10.1

# we should now be able to see the new routes
$ route -n
Destination     Gateway         Genmask            Flags Metric Ref    Use Iface
0.0.0.0         10.10.10.1      255.255.255.0      UH    0      0      0   eth0
10.10.10.0      0.0.0.0         255.255.255.0      UH    0      0      0   eth0


# pinging the gateway should now succeed
$ ping 10.10.10.1
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=0.144 ms

```
{% endcode %}

Perform the same series of steps for guest VM 2 with the commands above — replacing the network address appropriately.

\
**Host OS**

{% code title="Host OS" overflow="wrap" %}
```shell
# enable routing
$ sudo sysctl -w net.ipv4.ip_forward=1

# For each individual connected guest VMs - should be configured by default by the VM hypervisors
$ sudo ip route add 10.10.10.0/24 dev eth0
$ sudo ip route add 12.12.12.0/24 dev eth1

# there should be a route configured to each guest VM subnet
$ route -n
Destination     Gateway         Genmask            Flags Metric Ref    Use Iface
10.10.10.0      0.0.0.0         255.255.255.0      UH    0      0      0   eth0
12.12.12.0      0.0.0.0         255.255.255.0      UH    0      0      0   eth1
```
{% endcode %}

**Further notes (PENDING TESTING AND REFINMENT)**

1. Ping to gateway (eg. 10.10.10.1) without adding static route
2. Ping to the gateway interface address of the other VM

* after adding local static route, but without enabling IP forwarding (routing) on host OS
* should succeed

3. Ping to address of the other VM

* after adding local static route, but without enabling IP forwarding (routing) on host OS
* should NOT succeed, unless IP forwarding is enabled

...



#### (2) TAP interface (with bridging from host OS)

{% embed url="https://www.baeldung.com/linux/create-check-network-interface" %}

...

