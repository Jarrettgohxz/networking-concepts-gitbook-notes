# RDP/VNC



### Determine if RDP (TCP port 3389) is running

```bash
$ sudo ss -tln | grep 3389
$ sudo lsof -i :3389
$ sudo netstat -tln | grep 3389 # legacy
```
