# Linksys WHW01-V1 Mesh - OpenWrt Upgrade and Mesh Setup

## Executive Summary

This project documents reclaiming three Linksys WHW01-V1 (Velop Dual-Band) access points by installing OpenWrt 25.12.5 and configuring an 802.11s encrypted mesh. The goal of this project is to improve the overall security of EOL consumer hardware by transitioning to open source software, as well as adding enhanced security features such as WPA3, VLAN segmentation and granular firewall policies. 

## Hardware and Software Specifications

- Devices: 3x Linksys WHW01-V1 (Velo Dual-Band)
- Stock Firmware: Linksys factory image (1.1.13.202617)
- Target Firmware: OpenWrt 25.12.5 (Latest release at time of install)

## Current Software Limitations (Stock)

- Limited to WPA2
- WPS enabled by default
- UPnP enabled by default
- Weak and limited parental controls and firewall features
- Hardware is End-of-Life (EOL) with no ongoing vendor support or vulnerability patching

## Implementation Procedure

!!! Warning "Mesh Configuration Consistency"
The following wireless and mesh configurations must be applied identically across all nodes for the 802.11s mesh and client roaming to function properly.

### Installing OpenWrt
 - Reset to factory defaults (The reset password feature can also be utilized) 
 - Download latest supported version of OpenWrt for the WHW01-V1 from the official site 
 - In the Linksys GUI, navigate to "connectivity" settings and click "CA" at the bottom of the page. (This will give you the ability to select a manual update file)
 - Upload the OpenWrt image
 - After the system updates, access the OpenWrt GUI through 192.168.1.1
 - Login to OpenWrt (username = root, password = <blank>)
 

 ### OpenWrt Interface Configurations

 - Navigate to Network -> Interfaces 
    - Create a new interface called "wan" and assign it to "ETH2" (ETH2 is the top ethernet port)
    - Edit the "lan" interface
        - Set a static IPv4 address for each node (e.g., 10.0.0.1/24 for node-1, 10.0.0.2/24 for node-2, 10.0.0.3/24 for node-3)
        - Parent node: Set IPv4 (e.g., 10.0.0.1/24) and Enable DHCP,
        - Child nodes: Set IPv4 (e.g., 10.0.0.2/24), set the Gateway and DNS to parent node IP, and Disable DHCP
- Navigate to Network -> Devices
 - Configure "br-lan" and remove "ETH2" from the bridge ports

Note: The WAN connection must now be connected to "ETH2".

- Navigate to System -> Software
 - Select Actions: Update lists...
 - Remove "wpad-basic-mbedtls" (This package must be removed as it lacks 802.11s mesh support)
 - Install "wpad-mesh-openssl" 

- Navigate to System -> System
 - Set unique hostnames for each node (e.g., node-1, node-2, node-3)
 - Set the local timezone

- Reboot the devices

### Configuring the Wireless Mesh

- Navigate to Network -> Wireless
 - Add a new 5GHz interface
  - Set Operating frequency. (Both the channel and width must be statically set)
  - Change mode to "802.11s"
  - Set Mesh Id (e.g., whw01_mesh)
  - Set Network to "lan"
  - Set Encryption to "WPA3-SAE" and configure a secure Key

Validation: At this stage, peer nodes should be visible under the "Associated Stations" table in the wireless overview. If missing, verify all mesh settings are identical and radios are enabled.

### Configuring Wireless Access Points

Setting up the client-facing wireless networks follows a similar procedure. All ESSID and security settings must be identical across nodes to facilitate device roaming.

- Under the 2.4Ghz and 5Ghz radios, click Add:
 - Channel/Width: Select statically (Note: 5GHz AP must be on the same channel as the mesh due to the lack of a dedicated backhaul radio)
 - Mode: Access Point
 - ESSID: Wirless_Name (e.g., OpenWrt)
 - Network: lan
 - Encryption: WPA3-SAE
 - Key: [WiFi-Password]
 - 802.11r Fast Transition: Enabled

 ## Performance Analysis

 During testing the max throughput observed was ~250Mbps. This represents a best case scenario as testing was done with only one device connected to the network. As devices on the network increases, the shared 5GHz radio will become overwhelmed and be a bottleneck. Latency and throughput would be affected as a result. 

 ## Operational Drawbacks

1. Decentralized Management
    Every configuration change must be done to all nodes manually and identically. Any slight variation in network or wireless settings will break the mesh topology. The addition of a centralized controller or automation system would greatly increase the usability of the system. 

2. Channel Constraints/Interference: 
    The Linksys WHW01-V1 is only a dual band system. All 5Ghz traffic (mesh backhaul and access) must use the same channel. Due to OpenWrt 802.11s requiring statically assigned channels rather than dynamic selection, this setup is susceptive to co-channel interference especially in congested wireless environments. 

3. Half-Duplex Operation
    The mesh backhaul and client AP both operate over the singular 5Ghz radio. To support this, the radio must alternate between receiving data from a client and transmitting data to the parent node. This configuration halves the available throughput for mesh connected devices.

## Future Configurations

1. VLANs
    Enabling 802.1Q VLANs instead of having a flat layer-2 network would greatly improve the security by isolating trusted devices from untrusted devices such as IoT. 

2. Advanced Firewall Policies
    While the default NAT firewall rules in OpenWrt allow for network connectivity, creating granular rules based on VLAN, device type, etc would continue to restrict exposure to critical home lab systems. 

3. Centralized Management
    Using automation such as Ansible would allow for greater ease of use for this mesh setup. 

4. VPNs
    Integrating WireGuard or OpenVPN would allow for secure out-of-band management and remote access to my internal resources. 

## Conclusion

Replacing the stock Linksys firmware with OpenWrt revived unsupported hardware, introduced modern WPA3 encryption, enabled 802.11s encrypted mesh networking, and unlocked enterprise routing capabilities. For home‑lab and portfolio purposes, this demonstrates practical hardware reclamation and the fundamentals of a secure wireless mesh topology.

## Resources

- OpenWrt device page: https://openwrt.org/toh/linksys/whw01_v1
- Linksys initial setup (vendor): https://support.linksys.com/kb/article/58-en/
 