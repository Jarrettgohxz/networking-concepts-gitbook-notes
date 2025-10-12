# TUN & TAP interfaces

{% embed url="https://blog.cloudflare.com/virtual-networking-101-understanding-tap/" %}

### Comparisons between TUN and TAP interface

{% embed url="https://www.baeldung.com/linux/tun-interface-purpose" %}

|                   | TUN                            | TAP                                                                                                                             |
| ----------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| TCP/IP layer      | Network layer 3 (IP addresses) | Data link layer 2 (MAC addresses)                                                                                               |
| Routing/Switching | Routes based on IP addresses   | Switches based on MAC addresses                                                                                                 |
| Packet type       | IP packets only                | Ethernet frames (can carry IP, ARP, etc.)                                                                                       |
| Common usage      | VPN tunnels                    | <p>To act as a L2 switch: connect virtual machines, bridging, etc.<br>- Supports L2 features such as broadcasting ARP, etc.</p> |
|                   |                                |                                                                                                                                 |

