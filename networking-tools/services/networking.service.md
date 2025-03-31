# networking.service

Configuration file: `/etc/network/interfaces`

## Configuring static ip on Debian based Linux distributions (Ubuntu, Kali, Raspberry Pi OS, etc.)

1. Edit the configuration file: `/etc/network/interfaces`&#x20;

#### General configuration

```bash
auto <iface>
iface <iface> inet static
    address xxx.xxx.xx.xxx
    netmask xxx.xxx.xx.xxx
    gateway xxx.xxx.xx.xxx
    broadcast xxx.xxx.xx.xxx

    # Optional: not needed for a basic static ip address configuration
    dns-nameservers <nameserver-address>
```

**Example**&#x20;

Configure network interface _<mark style="color:red;">**eth0**</mark>_ to have the static IP address of `192.168.1.88/24` with the default gateway address of `192.168.1.254`, and broadcast address of `192.168.1.255`. Optionally, the DNS nameservers can be set too (`1.1.1.1` in this case).

<pre class="language-shell"><code class="lang-shell"><strong>auto eth0
</strong>iface eth0 inet static
  address 192.168.1.88
  netmask 255.255.255.0
  gateway 192.168.1.254
  broadcast 192.168.1.255
  dns-nameservers 1.1.1.1
  

</code></pre>

2. Restart the `networking.service` daemon

```bash
$ sudo systemctl restart networking.service
```

3. Verify the configuration

```bash
$ ip address show dev eth0
# or simply:
$ ip a show eth0
```



#### Troubleshooting

If the interface fails to display the new configurations, try the following:

1. Physically unlplug and plug back in the network adapter

a) Take note of the bright green/orange flashing light on the hardware. The connection is working if it's present.



2. Manually restart the interface using the _**ip**_ or _**ifconfig**_ tool

a) Bring the interface down and up again

* The bright green/orange flashing lights should be turned off when the interface is brought down, and will come back up again when the interface is brought up

```bash
# ip (preferred method on the latest Linux versions)
$ sudo ip link set <iface> down
$ sudo ip link set <iface> up

# ifconfig 
$ sudo ifconfig <iface> down
$ sudo ifconfig <iface> up
```
