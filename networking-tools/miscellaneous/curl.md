---
description: >-
  cURL which stands for "client for URL" is a useful networking tool that can be
  used in command lines or scripts for transferring data. It supports multiple
  protocols.
---

# cURL

The most used protocol I had work with when using cURL are the common HTTP/HTTPS methods: `GET`, `POST`, `DELETE`, etc.&#x20;

### Basic command:

```bash
# Basic
$ curl -X <method> <HTTP_URL>

# With headers
$ curl -X <method> -H <headers> <HTTP_URL>

# GET: no need to specify the -X flag
$ curl -H <headers> <HTTP_URL>

# POST with data
$ curl -X POST -H <headers> -d/--data <POST_data> <HTTP_URL>
 
# VERBOSE: increasing level of output
$ curl ... -v
$ curl ... -vv
$ curl ... -vvv
```

### Examples:

#### Sending a GET request to `https://domain.com`with query data

```bash
$ curl https://domain.com?key=value
```

**Sending a POST request to** `https://domain.com`**to post some data:**

```bash
$ curl -X POST https://domain.com -H "content-type:application/x-www-form-urlencoded" -d "key1=value1&key2=value2"
# OR
$ curl -X POST https://domain.com -H "content-type:application/x-www-form-urlencoded" --data "key1=value1&key2=value2"

```

{% embed url="https://curl.se/" %}
curl documentation
{% endembed %}
