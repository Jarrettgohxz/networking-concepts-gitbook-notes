# nfs

Network File System (NFS) is a networking protocol for distributed file sharing.&#x20;

### Basic commands

**Discover mountable remote shares**

1. nmap

```bash
# port 111 - rpcbind
# port 2049 - NFS
$ nmap -sV -p 111,2049 [remote_ip] 
...
PORT     STATE SERVICE VERSION
111/tcp  open  rpcbind ...
2049/tcp open  nfs     ...

# optional: using NSE script
$ nmap --script=nfs-showmount -p 111 "$TARGET"
...
```

2. showmount

* This command allows us to view the mountable share points on the remote server

```bash
# show all the mountable share points
$ showmount -e [remote_ip]
Export list for [remote_ip]:
[server_mount_location] [whitelisted_ip_addr]

# eg. client ip addr 10.10.10.10 whitelisted to mount the share /volume/test 
# from remote address 8.8.8.8
$ showmount -e 8.8.8.8

Export list for 8.8.8.8:
/volume/test 10.10.10.10
```

**Mount the remote shares**

<pre class="language-bash"><code class="lang-bash"><strong>$ mount -t nfs [nfs_server_ip_add]:[server_mount_location] [local_mount_point]
</strong>
# Eg. mount the /volume/test share onto the local directory /mnt/testa
$ mkdir /mnt/test # create the dir to mount the filesystem
$ mount -t nfs x.x.x.x:/volume/test /mnt/test
</code></pre>

### Important notes

1. Modifying the file permissions for a mounted folder (using `chmod`, etc.) locally may potentially affect the permission settings on the NFS server. Specifically, if a NFS client has sufficient privileges and the server permits it, file permission changes made to a shared mount locally will be reflected on the NFS server.

