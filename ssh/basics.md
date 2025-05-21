# Basics

**Basic connection (to get a remote shell)**

1.  Verify that SSH is running on the server

    * Refer to the section on [sshd](https://jarrettgxz-sec.gitbook.io/networking-concepts/ssh/sshd)


2. Retrieve a remote shell&#x20;

```bash
$ ssh -i [identity_file] user@server

# eg. Connect to server address 10.10.10.10 using a key stored 
# in key.pm for the user jarrett
$ ssh -i key.pem jarrett@10.10.10.10
```

`-i` _identity\_file_

> Selects a file from which the identity (private key) for public key authentication is read.



**Execute remote commands on the SSH server**

```bash
# basic command
$ ssh user@server command1
$ ssh user@server 'command2'
```

\
\
**Other common flags**

1. `-p` Specifies which port to connect to (default is 22)



{% embed url="https://man7.org/linux/man-pages/man1/ssh.1.html" %}

{% embed url="https://www.cyberciti.biz/faq/unix-linux-execute-command-using-ssh/#google_vignette" %}
