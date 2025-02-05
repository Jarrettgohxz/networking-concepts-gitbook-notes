# Python

### Simple projects I have worked on:

{% embed url="https://github.com/Jarrettgohh/network-offensive-security-experimentations" %}

### Simple socket programming with Python

_Create a TCP client:_

```python
import socket

HOST=<HOST>
PORT=<PORT>

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT)) # connect to a socket at HOST:PORT
    s.sendall(b"Hello friend") # send data to the socket
    data = s.recv(1024) # receive data from the socket
```

_Brute-force with threading_:

{% embed url="https://realpython.com/intro-to-python-threading/" %}

```python
import socket 

... PENDING COMPLETION
```
