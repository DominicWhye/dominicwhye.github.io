
<style>
.command-line {
  display: inline-flex;
  gap: 0.6rem;
  padding: 0.65rem 0.8rem;
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 8px;
  background: #0d1117;
  color: #00ffcc;
  font-family: monospace;
}
.command-line span {
  color: #ff5f56;
}
</style>


---
title: 'Creating Wake on Lan program'
description: 'Step by step guide on how to create and use Wake on Lan function'
pubDate: 'Apr 14 2026'
heroImage: '../../assets/blog-placeholder-3.jpg'
---
Start by creating a python file and importing the "socket" Library

<div class="command-line">
import socket
</div>

Followed by setting your target Mac and Ip address

<div class="command-line">
mac = "A1-B1-C1-D1-E1-F1"      # (input ur MAC address here) 
ip = "192.168.1.00"          # (input ur IP address here)
</div>


This is the creation of a magic packet, which wakes the target device

<div class="command-line">
packet = bytes.fromhex("FF"*6 + mac.replace("-","")*16)
</div>

This creates the format in IPv4 addressing and establish a UDP connection

<div class="command-line">
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
</div>

This command helps to send to broadcast packets in the device might not have an active Ip address yet

<div class="command-line">
s.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, 1)
</div>

This command helps to send the magic packet to an Ip stated above with the target port

<div class="command-line">
s.sendto(packet, (ip, 9))   #(setting port 9 as most devices listen to this port for WOL, if it doesnt work, can try port 7 or 0)
</div>