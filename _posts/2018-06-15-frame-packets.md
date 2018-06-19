---
layout: post
title: "Parsing Frames and Packets"
date: 2018-06-15
categories: Network
---
As someone who works with frames and packets on a daily basis, a summary about their inner structures and programming interfaces is long overdue. This post is intended to be practical rathr than through, as I will be skipping over things which are (IMO) less significat from a programmer's perspective. (No non-ethernet and non-ip traffic will be discussed; no preamble or SFD on the ethernet frame)

# Overall
![packet encapsulation](/assets/tcpip.png)
>> image from http://www.tcpipguide.com/free/t_IPDatagramEncapsulation.html

# Ethernet Header
```
bytes:   0 1 2 3 4 5      6 7 8 9 10 11     12 13
        +----------------+-----------------+------+
        | Dest.MAC       | Src.MAC         | Type |
        +----------------+-----------------+------+
```
* Dest.MAC: self-explanatory
* Src.MAC: self-explanatory
* Type: specify the L3 protocol. i.e. IPv4, IPv6, ARP, ...

Examplary Python code to dissect ethernet header (**Linux only**)
{% highlight python %}
import socket, sys, struct

eth_len = 14 

try:
    s = socket.socket(socket.AF_PACKET, socket.SOCK_RAW, socket.ntohs(0x0003))
except socket.error, msg:
    print 'Socket creation failed. Error Code: ', str(msg[0]), 'message: ', msg[1]
    sys.exit()

while True:
    data, addr = s.recvfrom(65565)      
    eth_header = data[:eth_length]
    eth_header = struct.unpack('!6s6sh', eth_header)

    eth_proto = socket.ntohs(eth_header[2])
    dest_mac = data[0:6]
    src_mac = data[6:12]

    if eth_proto == 8:  # IP 
        pass
{% endhighlight %}

Notes:

* ```socket.socket(socket.AF_PACKET, socket.SOCK_RAW, socket.ntohs(0x0003))```

The first argument is the **socket family**. If you've done some socket programming before, chances are you've seen code looking instead like `socket.socket(socket.AF_INET, ...)`. The field `AF_INET` denotes that our socket is intended to communicate with IPv4 addresses. That way, the kernel would then manage lower-level headers for us (automatically generate L2 headers when `send`ing packets and strip away L2 headers when `receive`ing packets). Because here we want to dissect the ethernet header, we have to opt for something else. From [man 7 packet](http://man7.org/linux/man-pages/man7/packet.7.html), "**Packet sockets are used to receive or send raw packets at the device driver (OSI Layer 2) level.**"
![raw socket](/assets/raw_socket.jpg)
>> image from https://opensourceforu.com/2015/03/a-guide-to-using-raw-sockets/

The second argument is the **socket type**. Also from the man page, when you create a packet socket, the type field is “**either `SOCK_RAW` for raw packet including the link-level header or `SOCK_DGRAM` for cooked packets with the link-level header removed**”. Since we want to dig into the header, the second filed needs to be `SOCK_RAW`.

The third argument is **protocol**. Usually there is only one supported protocol given the socket family and type, so this field can be omitted. Since we are working with raw sockets and want to capture all protocols, the special value `0x0003` is used.

* Where is the `bind` operation?

When working with a packet socket, by default all packets of the specified protocol will be passed to the socket(incoming and outgoing, on all interfaces). If we only want packets from a specific interface or address, we can use `bind` to specify.



(to be continued)

Reference:
>> http://man7.org/linux/man-pages/man7/packet.7.html
>> https://linux.die.net/man/2/socket
>> https://stackoverflow.com/questions/1593946/what-is-af-inet-and-why-do-i-need-it


