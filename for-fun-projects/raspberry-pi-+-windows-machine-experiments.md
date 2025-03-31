# Raspberry pi + Windows machine experiments

{% embed url="https://chatgpt.com/c/67de8640-90bc-8008-a44f-1f35445ac0df" %}

The goal of this project is to allow the Windows machine (laptop) to access the capabilities of a headless raspberry pi (running Kali Linux in this example, but works for Raspberry Pi OS, Ubuntu, etc.).

This can be achieved by connecting my Windows machine to the Raspberry Pi via an ethernet connection that provides internet access by routing all outbound traffic through the default interface. I will also be able to access a Linux shell from the Windows machine via a SSH connection.

Additionally, to allow my Windows machine to access the TryhackMe ([https://help.tryhackme.com/en/articles/6611809-getting-started-with-openvpn](https://help.tryhackme.com/en/articles/6611809-getting-started-with-openvpn)) VPN servers, I will first setup an OpenVPN connection on my Raspberry pi, before performing appropriate networking configurations to route traffic to the VPN interface.&#x20;

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

Assume that we want the Raspberry Pi interface `eth1` to have a static IP address value of `22.22.22.22`. The DHCP range will be `22.22.22.0` to `22.22.22.21` .

Add the following entries at the bottom of the `/etc/network/interfaces`  and `/etc/dnsmasq.conf` files respectively:

```bash
auto eth1
iface eth1 inet static
  address 22.22.22.22
  netmask 255.255.255.0
```

```bash
interface=eth1
dhcp-range=22.22.22.0,22.22.22.21
```

Restart the `networking.service` and `dnsmasq.conf` services:

```bash
$ systemctl restart networking.service
$ systemctl restart dnsmasq.service
```

```powershell
...
   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 22.22.22.x
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 22.22.22.22
```

#### Persist the configurations

```bash
$ systemctl enable networking.service
$ systemctl enable dnsmasq.service
```

-> the command above ensures that both services will be enabled on reboot.



2. **Enable IPV4 forwarding and iptables masquerading**

-> consequently, all traffic destined for the internet (from the Windows machine) will route through the Raspberry pi

-> try disabling IPV4 forwarding (`sysctl -w net.ipv4_ip_forward=1`), or removing the IP masquerading rule, and observe the changes (internet connection for the Windows machine should not work anymore)

#### Persist the configurations

-> Add the line `net.ipv4.ip_forward = 1` in `/etc/sysctl.conf` for persistence



persistence for iptables rule: [https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/firewall-and-security/iptables](https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/firewall-and-security/iptables)



3. **Open a SSH session on the Raspberry pi from the Windows machine**





