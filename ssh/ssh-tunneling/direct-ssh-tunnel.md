# Direct SSH tunnel

> A direct SSH tunnel, also known as local port forwarding, allows the creation of a tunnel that redirects traffic from a local address and port, to a specified port on a remote server, via an SSH connection

Direct tunneling is enabled with the `-L` flag, with the supplied values: `[local_address:]local_port:destination_address:destination_port`.

```bash
$ ssh user@server -L [local_address:]local_port:destination_address:destination_port
```

Eg. Direct tunneling from localhost at port 8000 to the localhost on the remote server side at port 8888.

```bash
$ ssh user@13.10.10.10 -L 127.0.0.1:8000:127.0.0.1:8888
$ ssh user@13.10.10.10 -L 8000:127.0.0.1:8888
```

Notice that the only 3 values are provided, as the first value would default to bind on all interfaces (`0.0.0.0`) if omitted.
