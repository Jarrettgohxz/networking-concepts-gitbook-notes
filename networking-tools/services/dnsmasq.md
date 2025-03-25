---
description: >-
  dnsmasq is free software providing services such as Domain Name System (DNS)
  caching, Dynamic Host Configuration Protocol (DHCP) server, and many more.
---

# dnsmasq

### Setup a Dynamic Host Configuration Protocol (DHCP) server

The following are the steps to setup a DHCP server with _dnsmasq_:

1.  Setup a static IP address on the machine acting as the DHCP server

    * The steps to configure a static IP address are outlined in the _**networking.service**_ sub-section under this section ([https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/services/networking.service](https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/services/networking.service))


2. &#x20;Configure the DHCP server settings from the _**/etc/dnsmasq.conf**_ file&#x20;

a) Open up _**/etc/dnsmasq.conf**_ on a text editor&#x20;

```bash
$ sudo vim /etc/dnsmasq.conf
# OR
$ sudo nano /etc/dnsmasq.conf
```

b) Add the following lines at the bottom

* Replace _iface_, _dhcp\_start\_range_, _dhcp\_end\_range_ and _lease\_time_ with the appropriate values
* The _iface_ value used should match the one that have been configured from part 1 (static IP address allocation)

<pre class="language-bash"><code class="lang-bash">...
interface=&#x3C;iface>
<strong>dhcp-range=&#x3C;dhcp_start_range>,&#x3C;dhcp_end_range>,&#x3C;lease_time>
</strong><strong>
</strong></code></pre>

c) Save the file



3. Restart the _dnsmasq_ daemon

```bash
$ sudo systemctl restart dnsmasq.service
```

#### Example

Setup a machine as a DHCP server on the interface _<mark style="color:red;">**eth0**</mark>_, with the static IP address of `88.88.88.88`, and a DHCP pool range of `88.88.88.0` to `88.88.88.87`. The lease time will be set to 24 hours.

```bash
# 1. Set static IP address (Debian-based distro)
# Notice that only a few important fields are included
$ sudo vim /etc/network/interfaces
...
auto eth0
iface eth0 inet static
  address 88.88.88.88
  netmask 255.255.255.0
  

# 2. Edit the dnsmasq configuration file
$ sudo vim /etc/dnsmasq.conf 
...
interface=eth0
dhcp-range=88.88.88.0,88.88.88.87,24h

# 3. Restart the dnsmasq daemon and check the status
$ sudo systemctl restart dnsmasq.service
$ sudo systemctl status dnsmasq.service

```

### Connecting to the DHCP server

Simply connect a physical ethernet adapter from the client interface to the interface on the DHCP server machine that was configured.&#x20;

Subsequently, verify if the client interface have been successfully configured with an IP address from the DHCP range. Perform the following on the client machine; a few troubleshooting steps will be outlined too.&#x20;

### Linux

```bash
# Verify the configuration (run either one of the commands) 
$ ifconfig
$ ip a
```

Look for the paragraph where the client interface is configured (eg. _**eth0**_)

```bash
.:eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> ... state UP  ...
    link/ether xx.xx.xx.xx.xx.xx brd ff:ff:ff:ff:ff:ff
    inet 88.88.88.xxx/24 brd 88.88.88.255 scope global eth0

```

The IP address displayed after **inet** should be within the DHCP pool range defined in the DHCP server configurations, and the state should be _**UP**_.

#### <mark style="color:orange;">Troubleshooting on Linux</mark>

The following actions can be taken to resolve any issues:

1. Reset the client interface

```bash
$ sudo ip link set eth0 down
$ sudo ip link set eth0 up
```

2. Restart the networking.service daemon

```bash
$ sudo systemctl restart networking.service
```

### Windows

```powershell
PS> ipconfig /all
```

Look for the paragraph where the client interface is configured (eg. _**Ethernet 8**_)

```powershell
Ethernet adapter Ethernet 8
Connection-specific DNS suffix .: ...
Description ....................: ...
Physical Address ...............: ...
DHCP Enabled....................: Yes
Autoconfiguration Enabled.......: Yes
IPV4 Address....................: 88.88.88.xxx(Preferred)
Subnet Mask.....................: 255.255.255.0
Lease Obtained..................: ...
Lease Expires...................: ...
Default Gateway.................: 88.88.88.88
DHCP Server.....................: 88.88.88.88
DNS Server......................: 88.88.88.88
... 
```

The IPV4 address value displayed should be within the range of the DHCP pool range defined in the DHCP server configuration. The default gateway, DHCP server and DNS server entries should be the IP address of the DHCP server.

#### <mark style="color:orange;">Troubleshooting on Windows</mark>

The following actions can be taken trigger a DHCP query:

```powershell
PS> ipconfig /release
PS> ipconfig /renew
```
