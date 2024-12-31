---
description: tracert (Windows) and traceroute (Linux).
---

# tracert/traceroute

A trace-route works by sending **Internet Control Message Protocol  (ICMP) echo request** (_Windows_) or **UDP datagrams** (_Linux/macOS_) packets to be able to discover the route that a packet traverses to a destination. It relies on the _Time-To-Live (TTL)_ value present in the _**IP (TCIP/IP layer 3)**_ header to discover the number of routers traversed along a route, as well as the IP address of each successive router.&#x20;

Given an IP packet (collectively for ICMP or UDP packet) with TTL value set to `10`, the first router which receives it will decrement the value to `9`, before passing it on to the next router. This process happens until the router TTL value is received by the 10th router, where it will decrement it to `0`, before sending a ICMP _time exceed in-tran&#x73;_&#x69;t error message back to the originating client.

Thus, the _tracert/traceroute_ tool utilizes this feature by first sending an ICMP packet with TTL value of `1`, which causes the first router to decrement the TTL to `0` where the packet will be dropped, before sending a ICMP _time exceed in-tran&#x73;_&#x69;t error message back to the client; this first step will typically happen for 3 packets (can be controlled with the `-q` flag for traceroute). This allows the client to view the IP address of the router. Note that some routers may be configured to not respond with an error message.

Subsequently, another IP packet with TTL value set to 2 will be sent, causing the second router in route to respond with the similar ICMP _time exceed in-tran&#x73;_&#x69;t error message. This process happens until the IP address of the final router matches that of the destination or the maximum TTL is reached (typically 30). The final value of the TTL indicates the number of successive routers in the route.

Note that a packet may take different routes each time, and the IP address of each successive router may be different.

## tracert

```powershell
PS> tracert <host> 
```

{% embed url="https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/tracert" %}

## traceroute

```bash
$ traceroute <host>

# Sets the number of probe packets per hop. The default is 3.
$ traceroute <host> -q [num_probes]

# Use ICMP ECHO for probe
$ traceroute <host> -I

# Use TCP SYN for probes
$ traceroute <host> -T
```

The `-I` and `-T` flag options have the potential to bypass firewalls.

{% embed url="https://linux.die.net/man/8/traceroute" %}

## Example

```bash
$ traceroute xx.xx.xx.xx
 1  11.11.11.11  3.912 ms 11.11.12.12 4.12 ms 11.11.13.13 4.56 ms
 2  22.22.22.22  3.074 ms * *
 3  33.33.33.33  3.051 ms * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8 xx.xx.xx.xx 22.2 ms 23.4 ms 20.2 ms
```

a) The number of line indicates the number of successive routers in the route

b) **Line 1**: For _TTL_ value of `0`, 3 different routers responded with ICMP time exceeded in-transit error message, with the time in `ms` indicating the round-trip value

c) **Line 2**: For _TTL_ value of `1`, only a single router responded with ICMP time exceeded in-transit error message. The asterisk value (`*`) indicates a no response.

d) From **line 4 to 7**, all of the routers didn't respond with an error message.

e) **Line 8** shows that the final destination is reached, where the round-trip value are 2`2.2 ms`, `23.3 ms` and `20.2 ms` for each probe respectively.
