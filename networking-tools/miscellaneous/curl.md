---
description: >-
  cURL which stands for "client for URL" is a useful networking tool that can be
  used in command lines or scripts for transferring data. It supports multiple
  protocols.
---

# cURL

### Basic commands:

```bash
# Basic
$ curl -X <method> <HTTP_URL>

# With headers
$ curl -X <method> -H <headers> <HTTP_URL>

# GET: no need to specify the -X flag
$ curl -H <headers> <HTTP_URL>

# VERBOSE: increasing level of output
$ curl ... -v
$ curl ... -vv
$ curl ... -vvv
```

### Examples

#### Sending a GET request to `https://domain.com` with query data

```bash
$ curl https://domain.com?key=value
```

### POST&#x20;

#### x-www-form-urlencoded

```bash
# no need to specify -X POST
$ curl -H "content-type:application/x-www-form-urlencoded" -d/--data <POST_data> <HTTP_URL> 
```

Eg. Sending a POST request to `https://domain.com` with some **x-www-form-urlencoded** data.&#x20;

```bash
$ curl https://domain.com -H ... -d "key1=value1&key2=value2"
# OR
$ curl https://domain.com -H ... --data "key1=value1&key2=value2"
```

#### json

```bash
# no need to specify -X POST
$ curl -H 'application/json' -d/--data <POST_data> <HTTP_URL> 
```

Eg. Sending a POST request to `https://domain.com` with some **json** data.&#x20;

```bash
$ curl https://domain.com -H ... -d '{"key1":"value1","key2":"value2"}'
# OR
$ curl https://domain.com -H ... --data '{"key1":"value1","key2":"value2"}'

# from file
$ curl https://domain.com -H ... -d @file.json
```

#### Form-data

The `@` and `<` symbols can be used to read contents from a file for file upload and text field respectively.

From the cURL documentation:

> To force the 'content' part to be a file, prefix the file name with an @ sign. To just get the content part from a file, prefix the file name with the symbol <. The difference between @ and < is then that @ makes a file get attached in the post as a file upload, while the < makes a text field and just get the contents for that text field from a file.

```bash
# no need to specify -X POST
$ curl -H <headers> -F/--form <form_data> <HTTP_URL>

# Eg.
$ curl -H <headers> -F "fileToUpload=@example.php;type=text/x-php" -F "submit=Upload" 
```

### SOCKS5 proxy

```bash
$ curl --socks5 [addr]:[port] [url]
$ curl -x socks5://[addr]:[port] [url]

# eg. Connect to https://example.com while proxying traffic through 
# SOCKS5 proxy at 10.10.10.10 port 8888
$ curl --socks5 10.10.10.10:8888 https://example.com
$ curl -x socks5://10.10.10.10:8888 https://example.com
```

To make the proxy handle DNS resolutions, for privacy concerns, etc. This means that there will be no local address resolution.

The `--socks5-hostname` or `-x socks5h://` options can be used instead.

```bash
$ curl --socks5-hostname [addr]:[port] [url]
$ curl -x socks5h://[addr]:[port] [url]
```



{% embed url="https://everything.curl.dev/" %}

{% embed url="https://curl.se/" %}
curl documentation
{% endembed %}

{% embed url="https://linux.die.net/man/1/curl" %}
