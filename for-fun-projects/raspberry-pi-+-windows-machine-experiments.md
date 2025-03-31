# Raspberry pi + Windows machine experiments

{% embed url="https://chatgpt.com/c/67de8640-90bc-8008-a44f-1f35445ac0df" %}

The goal of this projec is to have the Windows machine (laptop) act as the graphical interface screen for a headless raspberry pi (running Kali Linux in this example, but works for Raspberry Pi OS, Ubuntu, etc.).

This means that I would connect my Windows machine to the Raspberry Pi via an ethernet connection that provides internet access by routing all outbound traffic through the Raspberry Pi. I will also be able to access a Linux shell via a SSH connection.

Additionally, to access the TryhackMe ([https://help.tryhackme.com/en/articles/6611809-getting-started-with-openvpn](https://help.tryhackme.com/en/articles/6611809-getting-started-with-openvpn)) VPN servers, I will setup an OpenVPN connection on my Raspberry pi, and perform networking configurations to allow the Windows machine to access the TryHackMe servers (via the VPN connection).&#x20;

In other words, from the Windows machine point-of-view, the Raspberry pi will be acting as a "network gateway" to the internet, with the benefit of being able to access a Linux shell environment.



### Overview of steps

1. Configure static IP address and DHCP server (`dnsmasq`) on the Raspberry pi

Note: The command given in this example to configure a static IP address only works for a Debian-based machine ([https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/services/networking.service](https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/services/networking.service)).

-> the contents in `/etc/network/interfaces` and /`etc/dnsmasq.conf` will persist on subsequent reboot

2. Enable IPV4 forwarding and iptables masquerading

-> add the line `net.ipv4.ip_forward = 1` in `/etc/sysctl.conf` for persistence

-> consequently, all traffic destined for the internet (from the Windows machine) will route through the Raspberry pi

-> try disabling IPV4 forwarding (`sysctl -w net.ipv4_ip_forward=1`), or removing the IP masquerading rule, and observe the changes (internet connection for the Windows machine should not work anymore)

persistence: [https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/firewall-and-security/iptables](https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/firewall-and-security/iptables)



3. Open a SSH session on the Raspberry pi from the Windows machine





