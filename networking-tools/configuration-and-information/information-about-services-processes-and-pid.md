---
description: >-
  The compiled commands listed below utilizes a few tools: ps, ss, lsof & pgrep.
  More details about these tools can be found in the individual pages above.
---

# Information about services/processes & PID

### <mark style="color:red;">\*\* TO CONTEMPLATE ABOUT THE COMMANDS WHICH REQUIRES SUDO</mark>

### Find processes by service name

<mark style="color:green;">pgrep flags usage</mark>:

* <mark style="color:green;">-l</mark>: List the process name as well as the process ID

```sh
$ pgrep -l <service-name>

# eg. To find PID (Process ID) associated with SSHD (SSH Daesmon)
$ pgrep -l sshd
```

### Retrieve information about a specific PID

<mark style="color:green;">ps flags usage</mark>:

* <mark style="color:green;">-p</mark>: To select by PID

```sh
$ ps -p <PID> 
$ ps -p <PID> -o pid, cmd # To only display relevant information
```

### Identify the port number related to a PID or service name

**Method 1**: Using ss

<mark style="color:green;">ss flags usage</mark>:

* <mark style="color:green;">-p</mark>: Show processes using sockets&#x20;

Note: In the example below, the command `grep -P ':\d{1,}'`essentially uses the Perl regex syntax matching to look for all values with the format of <mark style="color:blue;">**:<1 or more digits>**</mark> such as <mark style="color:blue;">:4000</mark>, <mark style="color:blue;">:8000</mark>, etc.&#x20;

```shell
$ sudo ss -p | grep <PID> | grep -P ':\d{1,}'

# eg. 
$ sudo ss -p | grep 1400 | grep -P ':\d{1,}'
tcp ESTAB 0    0    
xxxx.xxx.xxx.xxx:4000 xxx.xxx.xxx.xxx:... ....,pid=1400,...

```

**Method 2**: Using lsof

<mark style="color:green;">lsof flags usage</mark>:

* <mark style="color:green;">-i</mark>: To only list the addresses associated with an internet address (prevent overload of information that are not relevant)
* <mark style="color:green;">-P</mark>: Prevent conversion of port numbers to port names. For example, listing would appear as :**53** instead of **:domain**&#x20;
* <mark style="color:green;">-n</mark>:  Prevent conversion of network numbers to host names. For example, listing would appear as **127.0.0.1** instead of **localhost**



**Direct method to look for port numbers relating to a service**

```sh
$ sudo lsof -i -P -n | grep <service-name>
```

**Look for listening sockets related to a PID or service name**

The first portion of the command retrieves all listening sockets.&#x20;

```sh
$ sudo lsof -i -P -n | grep LISTEN
```

While the second portion narrows the output down to only the specified PID or service name.

```sh
$ ... | grep <PID>
$ ... | grep <service-name>
```

Final command

<pre class="language-sh"><code class="lang-sh"><strong>$ sudo lsof -i -P -n | grep LISTEN | grep &#x3C;PID>
</strong>$ sudo lsof -i -P -n | grep LISTEN | grep &#x3C;service-name>
</code></pre>
