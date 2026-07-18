# Installing OPNsense

## Overview

OPNsense is deployed as the perimeter firewall and gateway for the lab. It provides WAN connectivity, LAN routing, and DHCP for the internal network until the domain controller takes over DHCP services.

## Lab resources

- CPU: 1 core
- RAM: 3 GB
- Storage: 40 GB
- Network adapters:
  - `NIC1`: `vSwitch-WAN`
  - `NIC2`: `vSwitch-Prod`
- Secure Boot: Disabled
- TPM: Enabled

## Installation steps

1. Boot the VM from the OPNsense ISO image.
2. Assign the network interfaces manually when prompted.
3. Log in to the installer using the default credentials:
   - username: `installer`
   - password: `opnsense`
4. Complete the installation and set a secure root password.
5. Configure the interface addresses:
   - `WAN`: DHCP
   - `LAN`: `10.0.0.1/24`

## Post-install configuration

- Enable the LAN DHCP server for the `10.0.0.0/24` network during initial setup.
- Reserve the production DHCP range or configure exclusions for infrastructure addresses.
- Once the Windows domain controller is configured, disable OPNsense DHCP on the LAN to avoid conflicts.

## Notes

- Use the OPNsense web interface for firewall rule management and service monitoring.
- Confirm WAN connectivity before promoting internal services.

