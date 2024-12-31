# dig/nslookup

## dig

#### Basic command:

<pre class="language-bash"><code class="lang-bash">$ dig @&#x3C;nameserver> &#x3C;DOMAIN> [query_type]

# eg.
$ dig @8.8.8.8 google.com A #(a) - equivalent to nslookup
<strong>$ dig @8.8.8.8 google.com AAAA #(b) - equivalent to nslookup
</strong>$ dig @8.8.8.8 google.com NS #(c) - equivalent to nslookup
$ dig @8.8.8.8 google.com MX #(d) - equivalent to nslookup
</code></pre>

#### Other useful options:

```bash
$ dig ... +[no]<options>

# eg. 
$ dig ... +short
```

{% embed url="https://linux.die.net/man/1/dig" %}

## nslookup

#### Basic command:

```bash
$ nslookup -type=<query_type> <DOMAIN> [nameserver]

# eg. 
$ nslookup -type=A google.com 8.8.8.8 #(a)
$ nslookup -type=AAAA google.com 8.8.8.8 #(b)
$ nslookup -type=NS google.com 8.8.8.8 #(c)
$ nslookup -type=MX google.com 8.8.8.8 #(d)
```

{% embed url="https://linux.die.net/man/1/nslookup" %}
