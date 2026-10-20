# WPA-Disruption-and-Cracking

## 1. Overview
This lab will demonstrate a brute force attack against the Wi-Fi Protected Access (WPA) protocol that uses a pre-shared key (PSK). The goal is to authenticate to a wireless access point by disrupting the connection of an existing device, forcing it to re-authenticate.  During the re-authentication process, the client and access point perform a four-way handshake that reveals information about the keys being used.  If this information is collected, a brute-force attack can determine what passphrase generates the authentication keys.

## 2. Environment Setup
- The aircrack-ng tool suite will be utilized for this lab.
- It can be installed on Linux machines with the commands:
  - `sudo apt-get update`
  - `sudo apt-get install -y aircrack-ng`
- MacOS users might consider using the application JamWiFi, which has several comparable tools.
- Its source code is available on GitHub: https://github.com/0x0XDev/JamWiFi

- In order to sniff the authentication packets in this lab, your wireless interface must be set to monitor mode.
- Monitor mode enables a wireless network interface to capture all traffic over a wireless channel.
- Some devices already have wireless adapters that can be set to monitor mode, but others may require an external wireless network adapter.
- An external wireless network adapter will likely require drivers to be installed as well.
- Running the command **iwconfig** can help determine if an interface is in managed mode or in monitor mode.
- Before putting the interface in monitor mode, you may need to kill existing processes with the command:
  - `sudo airmon-ng check kill`
 
- The following commands can then be run to set up the external wireless adapter in monitoring mode.
  - `sudo ifconfig <interface> down`
  - `sudo iwconfig <interface> mode monitor`
  - `sudo ifconfig <interface> up`


## 3. Find and Disrupting the Existing Connection
