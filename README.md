# OPNsense Firewall & Network Security Lab

A hands-on network security and firewall administration lab built using Proxmox VE and OPNsense.

This project documents the design, configuration, testing, troubleshooting, and security hardening of a virtualized firewall environment.

## Project Objectives

- Deploy and administer an OPNsense firewall.
- Implement VLAN-based network segmentation.
- Configure firewall rules and network access controls.
- Secure administrative access using HTTPS and an internal Certificate Authority.
- Develop encrypted backup and disaster recovery procedures.
- Practice infrastructure troubleshooting and technical documentation.

## Lab Technologies

| Technology | Purpose |
|---|---|
| Proxmox VE | Virtualization platform |
| OPNsense | Firewall, routing, and network security |
| Fedora Linux | Network testing and administration |
| Windows PowerShell | Remote administration and backup verification |
| OpenSSL | Certificate inspection |
| Git and GitHub | Version control and technical documentation |

## Completed Lab Activities

- Deployed OPNsense in an isolated Proxmox environment.
- Configured LAN addressing and DHCP.
- Created VLAN 10 and verified network connectivity.
- Practiced firewall rule configuration and NAT.
- Created an internal Certificate Authority.
- Generated an RSA-4096 TLS server certificate.
- Configured HTTPS for the OPNsense management interface.
- Exported an encrypted firewall configuration backup.
- Used Proxmox USB passthrough to transfer backup files.
- Verified backup integrity using SHA-256 on Linux and Windows.
- Created independent backup copies on Windows and removable storage.

## Technical Documentation

| Document | Description |
|---|---|
| [Certificate Management](docs/05-certificate-management.md) | Internal CA, TLS certificates, HTTPS configuration, and validation |
| [Backup and Recovery](docs/06-backup-and-recovery.md) | Encrypted configuration backups, USB passthrough, SHA-256 verification, and recovery planning |

## Network Architecture

The lab uses virtual network bridges within Proxmox to separate the firewall's WAN and LAN interfaces.

VLAN 10 provides an additional isolated network for segmentation and firewall testing.

Detailed network diagrams and interface documentation will be added in a future update.

## Security Practices

- Use encrypted configuration backups.
- Verify file integrity using SHA-256.
- Protect private keys and authentication credentials.
- Restrict management access to authorized systems.
- Avoid publishing sensitive infrastructure configurations.
- Document changes and validation procedures.

## Planned Improvements

- Document the full Proxmox network architecture.
- Expand VLAN segmentation and firewall policy testing.
- Complete trusted CA deployment to management devices.
- Test restoration of encrypted OPNsense configurations.
- Develop a repeatable backup and recovery SOP.
- Add sanitized network diagrams and troubleshooting examples.

## Project Status

Active homelab project — configuration, testing, and documentation ongoing.

---

This repository is intended for educational purposes and as a technical portfolio demonstrating practical infrastructure administration and cybersecurity skills.
