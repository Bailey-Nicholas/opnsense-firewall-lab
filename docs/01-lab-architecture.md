# OPNsense Lab Architecture

## Overview

This project uses Proxmox VE to host an isolated OPNsense firewall environment for hands-on network administration and cybersecurity training.

The lab provides a controlled environment for testing routing, firewall policies, DHCP, NAT, and VLAN segmentation without modifying the production home network.

## Infrastructure

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox VE 8.4.1 |
| Firewall | OPNsense VM 100 |
| Test workstation | Fedora Linux VM 101 |
| VLAN test workstation | Fedora Linux VM 102 |
| WAN bridge | vmbr0 |
| LAN bridge | vmbr1 |
| Firewall LAN | 192.168.100.1/24 |
| VLAN 10 gateway | 192.168.10.1/24 |

## OPNsense Virtual Interface Mapping

The OPNsense firewall uses VirtIO network interfaces connected to Proxmox virtual bridges.

| OPNsense Interface | Proxmox Bridge | Function |
|---|---|---|
| vtnet0 | vmbr0 | WAN connectivity |
| vtnet2 | vmbr1 | Isolated lab LAN |
| VLAN 10 on vtnet2 | vmbr1 | Tagged VLAN traffic |

The WAN interface connects to the upstream network.

The LAN interface provides connectivity to the isolated lab network.

VLAN 10 uses IEEE 802.1Q tagging on the LAN-side interface.

These mappings reflect the lab configuration and should be reverified after any changes to the VM's virtual network adapters.

## Virtual Network Design

### WAN Bridge — vmbr0

The WAN bridge connects the OPNsense virtual firewall to the upstream network.

OPNsense uses this interface to reach external networks and the internet.

The WAN interface is connected to the Proxmox bridge through a virtual network adapter.

### LAN Bridge — vmbr1

The LAN bridge provides an isolated virtual network for lab devices.

The OPNsense LAN interface connects to this bridge.

Fedora test workstations also connect to this bridge, allowing them to communicate with the firewall without requiring physical network connections.

The lab LAN uses:

- Network: 192.168.100.0/24
- Gateway: 192.168.100.1
- DHCP server: OPNsense

### VLAN 10

VLAN 10 provides a separate logical network for testing segmentation.

Configuration:

- VLAN ID: 10
- Network: 192.168.10.0/24
- Gateway: 192.168.10.1
- DHCP server: OPNsense

The VLAN was created on the OPNsense LAN-side virtual interface.

## Virtual Machines

### VM 100 — OPNsense

Provides:

- Firewall policy enforcement
- Network routing
- DHCP services
- NAT
- VLAN interfaces
- HTTPS management

### VM 101 — Fedora Test Workstation

Used for:

- LAN connectivity testing
- Accessing the OPNsense management interface
- Certificate validation
- Backup file transfer
- Linux network diagnostics

### VM 102 — Fedora VLAN Test Workstation

Used for:

- VLAN 10 connectivity testing
- DHCP verification
- DNS testing
- Firewall policy validation
- Network segmentation exercises

## Security Design

The lab uses virtual network separation to reduce the risk of experimental configurations affecting production systems.

Security principles include:

1. Separating lab and production traffic.
2. Using dedicated virtual network bridges.
3. Enforcing inter-network access through firewall policies.
4. Restricting management access.
5. Validating configuration changes through connectivity tests.

## Verification

The following activities have been completed:

- Confirmed OPNsense LAN DHCP functionality.
- Verified Fedora VM 101 received a LAN address.
- Verified VLAN 10 DHCP functionality.
- Tested internet and DNS connectivity from VLAN 10.
- Practiced firewall rule creation and evaluation.
- Verified HTTPS access to the OPNsense management interface.

## Future Improvements

- Add a sanitized network topology diagram.
- Document the complete Proxmox bridge configuration.
- Record VLAN tagging and interface assignments.
- Expand inter-VLAN firewall testing.
- Develop repeatable network validation procedures.

## Lessons Learned

- Virtualization enables realistic network security testing without dedicated physical hardware.
- Virtual bridges can separate network traffic without requiring additional switches.
- VLANs provide logical network segmentation when configured consistently across network interfaces.
- Firewall rules determine which traffic may cross network boundaries.
- Isolated lab environments make troubleshooting and experimentation safer.
