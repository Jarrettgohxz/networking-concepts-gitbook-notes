# Dynamic SSH tunnel

> Dynamic SSH tunnel, also known as dynamic port forwarding allows the creation of a `SOCKS` proxy, allowing traffic to be relayed through the SSH connection. `SOCKS`, which stands for Socket Secure, is a network protocol that facilitates exchanges of network packets between a client and a server through a proxy server.

Dynamic tunneling is enabled with the `-D` flag, with the supplied values: `[local_address:]local_port`

```bash
$ ssh user@server -D [local_address:]local_port
```

To test the configurations, we can use the [cURL](https://jarrettgxz-sec.gitbook.io/networking-concepts/networking-tools/miscellaneous/curl) command with the `--socks5` or `-x` options.&#x20;

Eg. Suppose we have a remote server at the address **10.10.10.10** (running SSH daemon at default port 22). We want to configure a proxy on our localhost at port **8888**, that tunnels and proxies all traffic through the remote server.

```bash
$ ssh user@10.10.10.10 -D 127.0.0.1:8888

# Test the configurations
# The source address as seen from the destination address server will be that of 
# the proxy server
$ curl [destination_addr] --socks5 127.0.0.1:8888
$ curl [destination_addr] -x socks5://127.0.0.1:8888
```
