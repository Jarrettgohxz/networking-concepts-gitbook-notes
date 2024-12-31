---
description: >-
  SSH public key authentication method is more secure than traditional password
  methods, as it provides cryptographic strength.
---

# SSH public key authentication

The tools utilized in this section includes `ssh-keygen` and `ssh-copy-id`.



## Generating new keypairs with ssh-keygen

The simple command without any arguments shown below can be used to generate a keypair. This command will ask a few configuration questions as follow:

1. The file to save the private key. The default value is: `/.ssh/id_rsa` in the home directory (<mark style="color:blue;">`~/.ssh/id_rsa`</mark> or <mark style="color:blue;">`/home/<user>/.ssh/id_rsa`</mark>**).**  Press enter without typing anything to stick with the default.
2. A passphrase to encrypt the private key, providing an extra layer of security. This would prevent anyone who is unauthorized from being able to use the private key, even if they got hold of the private key file (as long as they have no knowledge of the passphrase). Press enter without typing anything to specify an empty passphrase (note that this would leave the private key vulnerable to unauthorized use if it is compromised).

Subsequently, the public key will be stored in the directory specified in the first question above, with the **.pub** extension. For example, `/<dir>/.ssh/id_rsa` -> `/<dir>/.ssh/id_rsa.pub`

```bash
$ ssh-keygen

# View the private key
$ cat ~/.ssh/id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----


# View the public key
$ cat ~/.ssh/id_rsa.pub
ssh-rsa
...
...
```

Command flag options can be passed in to the `ssh-keygen` tool, such as algorithm, key size, etc., but will not be discussed in this document. Visit the page links below for more information.

## Sending the public key to the server with ssh-copy-id

After the key generation process, the public key can be copied over to the SSH server (to which the client who generated the key-pair wants to authenticate to). The public key will be recognized as an authorized key in the server, and the corresponding private key (stored securely in the client side) can be used to authenticate with the server.&#x20;

The `ssh-copy-id` tool can be utilized:

<mark style="color:green;">**Command flags**</mark>

1. <mark style="color:green;">**-i**</mark>: Specifies the path of the public key file to be copied to the server

```bash
$ ssh-copy-id -i <path-to-public-key-file> user@ssh-host

# For example
$ ssh-copy-id -i ~/.ssh/id_rsa.pub user@xxx.xxx.xxx.xxx
```

### SSH server side authorized keys

The list of authorized SSH keys can be found in the `~/.ssh/authorized_keys` file.

```bash
$ cat ~/.ssh/authorized_keys
```

### <mark style="color:red;">Important notice</mark>

The public key is used by the SSH server to authorize a connecting user, while the private key is used by the client to authorize itself with the server. Thus, the private key should **always** be kept safely in the client side, and never copied over to anywhere.

{% embed url="https://www.ssh.com/academy/ssh/public-key-authentication" %}
SSH public key authentication overview
{% endembed %}

{% embed url="https://www.ssh.com/academy/ssh/keygen" %}
ssh-keygen
{% endembed %}

{% embed url="https://www.ssh.com/academy/ssh/copy-id" %}
ssh-copy-id
{% endembed %}

{% embed url="https://www.ssh.com/academy/ssh/authorized-key" %}
SSH authorized keys
{% endembed %}
