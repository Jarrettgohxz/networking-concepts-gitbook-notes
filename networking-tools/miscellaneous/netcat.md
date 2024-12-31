---
description: Listen, connect, ...
---

# netcat

### Basic command to listen on a TCP/UDP port:

**TCP**

```bash
$ nc -l -p <port>
$ nc -lp <port> # or simply

# eg. listen on local address TCP port 8000
$ nc -lp 8000
```

#### UDP

```bash
$ nc -u -l -p <port>
$ nc -ulp <port>

# eg. listen on local address UDP port 8000
$ nc -ulp 8000
```

_**Flags:**_

1. `-l`: listen mode, for inbound connects
2. `-p`: local port number
3. -u: UDP mode

### Connect to TCP/UDP server at a port

**Connect to a TCP server:**

```bash
$ nc <address> <port>

# eg. connect to TCP server at 88.88.88.88 port 8000
$ nc 88.88.88.88 8000
```

**Connect to a UDP server:**

```bash
$ nc -u <address> <port> 

# eg. connect to UDP server at 88.88.88.88 port 8000
$ nc -u 88.88.88.88 8000
```

###

{% embed url="https://linux.die.net/man/1/nc" %}



