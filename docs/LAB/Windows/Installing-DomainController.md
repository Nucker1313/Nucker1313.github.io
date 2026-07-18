# Installing Domain Controller

## Overview

This server is configured as a Windows Server domain controller for the lab environment. It provides Active Directory Domain Services, DNS, and DHCP for the internal production network.

## Lab resources

- CPU: 2 cores
- RAM: 4 GB
- Storage: 60 GB
- Network adapter: `vSwitch-Prod`
- Secure Boot: Enabled
- TPM: Enabled

## Installation steps

1. Boot the VM from the Windows Server evaluation ISO.
2. Install the operating system to the virtual hard disk.
3. Configure the local Administrator password.
4. Set the server hostname to `LAB-DC01`.
5. Configure a static network address:
   - IP address: `10.0.0.10`
   - Subnet mask: `255.255.255.0`
   - Default gateway: `10.0.0.1`
   - Preferred DNS server: `10.0.0.10`

## Role and feature installation

1. Install the following Windows Server roles and features:
   - Active Directory Domain Services
   - DNS Server
   - DHCP Server
2. Reboot the server after the installation completes.
3. Promote the server to a domain controller:
   - Domain name: `fakeprod.local`
   - Forest and domain functional level: `Windows Server 2025`

## DNS and DHCP configuration

- Verify the DNS forward lookup zone for `fakeprod.local` was created automatically.
- Create a reverse lookup zone for the `10.0.0.0/24` network.
- Configure a DHCP scope for the internal network:
  - Scope name: `Prod`
  - Address range: `10.0.0.1` to `10.0.0.254`
  - Subnet mask: `255.255.255.0`
  - Exclusions: `10.0.0.1` through `10.0.0.20`

## Administrative user

- Create a domain user named `Bryce`.
- Add the user to the following groups:
  - `Administrators`
  - `Domain Admins`
  - `Enterprise Admins`

## Post-installation notes

- Disable DHCP on OPNsense once the domain controller is responsible for the internal DHCP scope.
- Validate DNS resolution and Active Directory replication.
- Confirm the domain controller can service internal clients on the production network.
