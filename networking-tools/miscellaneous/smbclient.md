# smbclient

### Debug

```bash
$ smbclient -d <value>
```

### View list of shares

```bash
$ smbclient -L //host_or_ip -U [username] 
```

### Example

Connect to admin (`ADMIN$`) share on the host with IP address 10.10.x.x

```bash
$ smbclient //10.10.x.x/ADMIN$ -U [username] 
```
