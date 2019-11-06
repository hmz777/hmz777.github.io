---
layout: single
title:  "NetStalker"
excerpt: "NetStalker Initial release"
header:
  teaser: "https://i.imgur.com/tasMixk.png"
date:   2019-08-16 2:07:00 +0200
tags:
  - NetStalker 
  - Software
---

<p align="center">
  <img align="center" src="https://i.imgur.com/4NdcRHF.png" alt="NetStalker">
</p>


# NetStalker
A network tool to control the bandwidth over your local network, it can block internet access form any selected device, or limit its speed using packet redirection, in addition, it can log web activity for the targeted device using a built in packet sniffer.

# Features:
- Background Scanning for newly connected devices.
- Bandwidth limitation for better distribution of internet speed across devices, both upload and download speeds can be controlled for each device separately.
- A Packet Sniffer that is intended to log addresses that each device on the network visits with the ability to decode Http headers for HTTP packets and resolve domains for HTTPS packets, also the packet direction can be chosen in order to capture requests only or requests and responses.
- A packet viewer to view the properties of a selected packet in the Packet Sniffer with the ability to expand the viewer for better visibility.
- Export the captured packets as a log file with the resolved domains included along with the timestamp for each packet.
- Spoof protection in order not to get spoofed by the same tool or any other spoofing software.
- Dark and light modes.
- Get network card vendor for every device using MacVendors API for better device identification.
- Can be locked with a password.
- Can be minimized to tray if the option is chosen.
- Integration with windows 10 notification system (works from build 17763).
- When minimized it notifies the user of newly discovered devices using Windows 10 notification system with the ability to choose from multiple options on what actions to take.
- Track disconnected devices with a timeout for each device. 

# Binaries

- NetStalker [Setup package](https://github.com/hmz777/NetStalker/releases/download/v1.6/NS.exe)

# Pictures:

### Dark Mode:
<figure>
<a href="https://i.imgur.com/CpnUqdC.jpg"><img src="https://i.imgur.com/CpnUqdC.jpg"></a>
</figure>

### Light Mode:
![MainLight](https://i.imgur.com/HOQl1kI.jpg)

### Packet Sniffer Dark:
![SnifferDark](https://i.imgur.com/6C5qkRu.jpg)

### Packet Sniffer Light:
![SnifferLight](https://i.imgur.com/RtwLAst.jpg)

### Speed Limiter:
![SpeedLimiter](https://i.imgur.com/bJdjiMX.jpg)

### Expanded Packet Viewer:
![ExpandedPacketViewer](https://i.imgur.com/dzFAQjV.jpg)

# Additional Information

If you notice any errors or have a suggestion, you're free to email me or submit a pull request on the NetStalker [Repository](https://github.com/hmz777/NetStalker).

# Author
### Hamzi Alsheikh