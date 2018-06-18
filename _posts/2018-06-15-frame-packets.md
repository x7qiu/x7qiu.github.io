---
layout: post
title: "Parsing Frames and Packets"
date: 2018-06-15
categories: Network
---
As someone who works with frames and packets on a daily basis, a summary about their inner structures and programming interfaces is long overdue. This post is intended to be practical rathr than through, as I will be skipping over things which are (IMO) less significat from a programmer's perspective. (No non-ethernet and non-ip traffic will be discussed; no preamble or SFD on the ethernet frame)

# Overall
![packet encapsulation](/assets/tcpip.png)

# Ethernet Header
```
bytes:   0 1 2 3 4 5      6 7 8 9 10 11     12 13
        +----------------+-----------------+------+
        | Dest.MAC       | Src.MAC         | Type |
        +----------------+-----------------+------+
```
* Dest.MAC: self-explanatory
* Src.MAC: self-explanatory
* Type: 

Examplary Python code to dissect ethernet header
{% highlight python %}
import socket, sys, struct

eth_len = 14 

try:
    s = socket.socket(socket.AF_PACKET, socket.SOCK_RAW, socket.ntohs(0x0003))
except socket.error, msg:
    print 'Socket creation failed. Error Code: ', str(msg[0]), 'message: ', msg[1]
    sys.exit()

while True:
    packet = s.recvfrom(65565)
    packet = packet[0]
    eth_header = packet[:eth_length]
    eth_header = struct.unpack('!6s6sh', eth_header)

    eth_proto = socket.ntohs(eth_header[2])
    dest_mac = packet[0:6]
    src_mac = packet[6:12]

    if eth_proto == 8:  # IP 
        pass
{% endhighlight %}

Notes:

* ```socket.socket(socket.AF_PACKET, socket.SOCK_RAW, socket.ntohs(0x0003))```

The first argument is the **socket family**. If you've done some socket programming before, chances are you've seen code looking instead like `socket.socket(socket.AF_INET, ...)`. The field `AF_INET` denotes that our socket is intended to communicate with IPv4 addresses. Because here we want to dissect the ethernet header, which is one level below, we can't use it because the kernel would then take care of things for us and  strip away anyting below the IP layer. From [man 7 packet](http://man7.org/linux/man-pages/man7/packet.7.html), "**Packet sockets are used to receive or send raw packets at the device driver (OSI Layer 2) level.**"

The second argument is the **socket type**. Also from the man page, when you create a packet socket, the type field is “**either `SOCK_RAW` for raw packet including the link-level header or `SOCK_DGRAM` for cooked packets with the link-level header removed**”. Since we want to delve into the header, the second filed needs to be `SOCK_RAW`.

The third argument is **protocol**. `0x0003` means traffic of all protocols will be captured. 

* Where is the `bind` operation?





Reference:
>> http://www.tcpipguide.com/free/t_IPDatagramEncapsulation.htm
>> https://stackoverflow.com/questions/1593946/what-is-af-inet-and-why-do-i-need-it
>> http://man7.org/linux/man-pages/man7/packet.7.html

