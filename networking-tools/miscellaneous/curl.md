---
description: >-
  cURL which stands for "client for URL" is a useful networking tool that can be
  used in command lines or scripts for transferring data. It supports multiple
  protocols.
---

# cURL

The most used protocol I had work with when using cURL are the common HTTP/HTTPS methods: `GET`, `POST`, `DELETE`, etc.&#x20;

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

#### Sending a GET request to `https://domain.com`with query data

```bash
$ curl https://domain.com?key=value
```

### POST&#x20;

#### Text data

```bash
# no need to specify -X POST
$ curl -H <headers> -d/--data <POST_data> <HTTP_URL> 
```

Eg. Sending a POST request to `https://domain.com` with some data. Specify the `content-type` header with: `-H "content-type:application/x-www-form-urlencoded"`

```bash
$ curl https://domain.com -H ... -d "key1=value1&key2=value2"
# OR
$ curl https://domain.com -H ... --data "key1=value1&key2=value2"
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





{% embed url="https://curl.se/" %}
curl documentation
{% endembed %}

{% embed url="https://linux.die.net/man/1/curl" %}
