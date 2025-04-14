# Raspberry pi + Windows machine experiments

{% embed url="https://chatgpt.com/c/67de8640-90bc-8008-a44f-1f35445ac0df" %}

The goal of this project is to allow a Windows machine (laptop) to access the capabilities of a headless raspberry pi (running Kali Linux in this example, but works for Raspberry Pi OS, Ubuntu, etc.), without an external monitor screen.

This can be achieved by connecting the Windows machine to the Raspberry Pi via an ethernet connection that provides internet access by routing all outbound traffic through the default interface. I will also be able to access a Linux shell from the Windows machine via a SSH connection.

Additionally, to allow my Windows machine to access the TryhackMe ([https://help.tryhackme.com/en/articles/6611809-getting-started-with-openvpn](https://help.tryhackme.com/en/articles/6611809-getting-started-with-openvpn)) VPN servers, I will first setup an OpenVPN connection on my Raspberry pi, before performing appropriate networking configurations to route traffic through the VPN interface.&#x20;

In other words, from the Windows machine point-of-view, the Raspberry pi will be acting as a "network gateway" to the internet, with the benefit of being able to access a Linux shell environment. This removes the need for an external monitor screen for the Raspberry Pi.

### Hardware setup

1. <mark style="color:red;">Raspberry Pi</mark>

* Default ethernet interface (`eth0`): connected directly to the router via the LAN port
* USB-A port (`eth1`): connected directly to the Windows machine (USB-A to ethernet)

2. <mark style="color:blue;">Windows machine</mark>

* USB-C port: connected directly to the Raspberry Pi (USB-C to ethernet)

Take note of the interface names (`eth0` and `eth1`) for each connection.

### Overview of steps

1. **Configure static IP address and DHCP server (`dnsmasq`) on the Raspberry pi**

Note: The command given in this example (to configure a static IP address) only works for a Debian-based machine.

{% embed url="https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/services/networking.service)" %}
Configuring static IP address
{% endembed %}

{% embed url="https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/services/dnsmasq" %}
Setting up a DHCP server with dnsmasq
{% endembed %}

Assume we want the Raspberry Pi interface `eth1` to have a static IP address value of `22.22.22.22`. Additionally, the DHCP server will provide the addresses from `22.22.22.0` to `22.22.22.21` .

In the Raspberry Pi, add the following entries at the bottom of the `/etc/network/interfaces` and `/etc/dnsmasq.conf` files respectively:

<pre class="language-bash"><code class="lang-bash"><strong># ```/etc/network/interfaces
</strong><strong>auto eth1
</strong>iface eth1 inet static
  address 22.22.22.22
  netmask 255.255.255.0
</code></pre>

````bash
# ```/etc/dnsmasq.conf
interface=eth1
dhcp-range=22.22.22.0,22.22.22.21
````

Restart the `networking.service` and `dnsmasq.service` services:

````bash
# ```raspbberypi
$ systemctl restart networking.service
$ systemctl restart dnsmasq.service
````

**Persist the configurations**

````bash
# ```raspbberypi
$ systemctl enable networking.service
$ systemctl enable dnsmasq.service
````

-> the command above ensures that both services will be enabled on reboot.

**Test the configurations**

As of now, the Windows machine will have an IP address (within the configured DHCP range) assigned to its interface, but will not be able access the internet. Without further configurations, the traffic coming from the Windows machine will simply be received and dropped by the Raspberry Pi.&#x20;

The following image shows the traffic through the `eth1` interface (Raspberry Pi interface that is directly connected to the Windows machine), with the filter value `ip.addr==146.190.62.39`, when visiting the address `http://146.190.62.39:80` (_http://httpforever.com_) on the browser.&#x20;

The traffic displays the source address of `22.22.22.8`, which happens to be the address assigned to the interface on the Windows machine (given by the DHCP server). The destination address is `146.190.62.39` . Notice that 3 consecutive TCP SYN requests were sent to the destination, with no reply. Subsequently a series of TCP retransmissions were initiated, with no replies too.&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Wireshark capture from the Raspberry Pi <code>eth1</code> interface</p></figcaption></figure>

This behavior happens continuously.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

2. **Enable IPV4 forwarding and `iptables` masquerading**

Enable IPV4 forwarding on the Raspberry pi:

````bash
# ```raspbberypi
$ sysctl -w net.ipv4.ip_forward = 1
````

Looking at the two commands listed below, the first enables the routing of traffic bound for the internet: from the Windows machine interface (`eth1`), to the default interface (`eth0`). The second commands works the same way, but routes traffic through the VPN interface (`tun0`) instead.

````bash
# ```raspbberypi
$ iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
$ iptables -t nat -A POSTROUTING -o tun0 -j MASQUERADE
````

-> consequently, all traffic destined for the internet (from the Windows machine) will route through the Raspberry pi

**To better understand the configurations**

Perform the following actions, and observe the changes (internet connection for the Windows machine should not work anymore):

a) Try disabling IPV4 forwarding

```bash
$ sysctl -w net.ipv4.ip_forward = 0
```

B) Try removing the IP masquerading rule

```bash
$ iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
```

**Persist the configurations**

a) Persist the IPV4 forward rules

* Add the line `net.ipv4.ip_forward = 1` in `/etc/sysctl.conf` for persistence

b) Persist the iptables rules

&#x20;[https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/firewall-and-security/iptables](https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/firewall-and-security/iptables)

**Test the configurations**

a) Try connecting to a random website from the Windows machine. The connection should succeed, similar to when the machine is directly connected to the router.

b) Try connecting to the TryHackMe's VPN server

_Raspberry Pi_&#x20;

```bash
$ openvpn [vpn_file].ovpn
```

_Windows machine_

The following command should return the local IP address of our machine within the VPN server (`10.x.x.x`)

```bash
$ curl 10.10.10.10/whoami # test connection
```

A script can be utilized to enable the `openvpn` connection on the Raspberry pi on startup, by creating a  `systemd` service. Refer to the following documentation for more information: [https://jarrettgxz-sec.gitbook.io/linux/system/systemd](https://jarrettgxz-sec.gitbook.io/linux/system/systemd).



3. **Open a SSH session on the Raspberry pi from the Windows machine**

Enable the SSH service on the Raspberry Pi:

````bash
# ```raspberrypi
$ systemctl start ssh.service
````

````bash
# ```windows-machine
$ ssh [username]@22.22.22.22
...
````



### Additional setup steps

1. Setup the Linux machine as the DNS server for the Windows machine

````powershell
```windows
> netsh interface ipv4 set dns name=[interface_name] static [linux_ip_addr]
> netsh interface ipv4 show dnsservers
````

````bash
```linux
$ systemctl restart dnsmasq.conf
````

The records defined in `/etc/hosts` should be resolvable from the Windows machine.

````powershell
```windows
> nslookup [addr] [linux_dns_server_addr]
````

### Important troubleshooting steps

1. Ensure Windows machine is not connected to any VPN or external network services.
2. ...
