---
description: >-
  This page discusses simple tips and tricks that I have discovered while
  playing around with networking tools and concepts
---

# Simple tips & tricks

While some might not be useful or applicable to all users and situations, it can potentially provide some shortcuts.

### Retrieve public IP address

```bash
$ curl ifconfig.me

# alternatively, to allow the output to be displayed properly
$ curl ifconfig.me && printf '\n'
xxx.xxx.xxx.xxx
```

This simple command sends a HTTP GET request to the host **ifconfig.me,** which simply returns the source IP address. There are some other useful flags provided, visit [https://ifconfig.me/](https://ifconfig.me/) to learn more.

This command can be useful to discover the public IP address of a machine behind any types of connections such as a campus or VPN.
