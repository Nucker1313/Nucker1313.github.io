# Hyper-V Environment Setup

## Overview

The Hyper-V lab environment is built using two virtual switches to separate the internet-facing network from the internal production network. This structure supports a secure firewall perimeter and a dedicated domain controller network.

## Virtual switches

- `vSwitch-WAN` - External network connected to the host's physical NIC. This switch carries WAN traffic for the firewall.
- `vSwitch-Prod` - Private internal network for lab infrastructure, including the domain controller and internal services.

## Recommended configuration

- Create the external switch with the host adapter connected to your physical network.
- Create the private switch as an isolated internal network with no direct host access.
- Attach the OPNsense VM with two NICs: one on `vSwitch-WAN` and one on `vSwitch-Prod`.
- Attach the Windows Server VM to `vSwitch-Prod` only.

## Notes

- Use descriptive switch names to keep the environment easy to maintain.
- Keep the lab network isolated from production or home networks when possible.

