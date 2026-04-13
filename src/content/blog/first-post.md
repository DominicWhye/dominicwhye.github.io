
---
title: 'Creating Wake on Lan program'
description: 'Step by step guide on how to create and use Wake on Lan function'
pubDate: 'Apr 14 2026'
heroImage: '../../assets/blog-placeholder-3.jpg'
---
Start by creating a python file and importing the "socket" Library

<pre class="command-line">
import socket
</pre>

Followed by setting your target Mac and Ip address

<pre class="command-line">
mac = "A1-B1-C1-D1-E1-F1"
ip = "192.168.1.00"
</pre>


This is the creation of a magic packet, which wakes the target device

<pre class="command-line">
packet = bytes.fromhex("FF"*6 + mac.replace("-","")*16)
</pre>

This creates the format in IPv4 addressing and establish a UDP connection

<pre class="command-line">
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
</pre>

This command helps to send to broadcast packets in the device might not have an active Ip address yet

<pre class="command-line">
s.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, 1)
</pre>

This command helps to send the magic packet to an Ip stated above with the target port
(setting port 9 as most devices listen to this port for WOL, if it doesnt work, can try port 7 or 0)

<pre class="command-line">
s.sendto(packet, (ip, 9))  
</pre>