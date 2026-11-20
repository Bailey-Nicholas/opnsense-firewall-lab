# OPNsense VLAN Configuration and Testing

## Overview

This document covers the implementation and validation of VLAN 10 in an isolated OPNsense firewall lab running on Proxmox VE.

The purpose of the exercise was to practice logical network segmentation, DHCP configuration, routing, and firewall policy enforcement.

## Lab Environment

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox VE 8.4.1 |
| Firewall | OPNsense VM 100 |
| VLAN test client | Fedora Linux VM 102 |
| Virtual LAN bridge | vmbr1 |
| VLAN ID | 10 |
| VLAN subnet | 192.168.10.0/24 |
| VLAN gateway | 192.168.10.1 |
| DHCP server | OPNsense |

## 1. VLAN Interface Creation

Created VLAN 10 using the OPNsense web management interface.

Configuration:

- Parent interface: LAN-side virtual network interface
- VLAN tag: 10
- Description: VLAN10

The VLAN interface was assigned and enabled in OPNsense.

## 2. IP Addressing

Configured the VLAN interface with a static IPv4 address:

- Interface address: 192.168.10.1
- Subnet mask: /24
- Network: 192.168.10.0/24

This address serves as the default gateway for devices assigned to VLAN 10.

## 3. DHCP Configuration

Enabled DHCP service for VLAN 10.

The DHCP service provides:

- Client IPv4 addresses
- Default gateway
- DNS configuration

The DHCP lease range should be recorded after verifying the current configuration.

## 4. Fedora Test Workstation

Fedora VM 102 was used to test VLAN connectivity.

The test workstation was connected to the lab's virtual network environment.

The VLAN configuration allowed the workstation to communicate with the OPNsense VLAN 10 gateway.

## 5. Connectivity Testing

The following tests were completed during the lab.

### DHCP Verification

Confirmed the Fedora test client received an IPv4 address from the VLAN 10 network.

Example Linux command:

```bash
ip -br addr
```

### Gateway Connectivity

Tested connectivity to the VLAN gateway.

```bash
ping -c 4 192.168.10.1
```

### Internet Connectivity

Verified outbound network connectivity.

```bash
ping -c 4 1.1.1.1
```

### DNS Resolution

Verified DNS resolution.

```bash
getent hosts example.com
```

The VLAN test client successfully accessed the internet and resolved DNS queries.

## 6. Firewall Policies

Practiced creating and evaluating OPNsense firewall rules.

Topics included:

- Interface-based firewall rules
- Allow and block actions
- Rule ordering
- Default-deny behavior
- NAT
- Floating rules and rule precedence

Firewall policies were evaluated in the isolated lab environment.

Specific rule definitions and test results will be documented separately.

## 7. Security Considerations

VLANs provide logical Layer 2 segmentation but do not independently enforce all Layer 3 security boundaries.

Inter-VLAN communication must be controlled using appropriate firewall policies.

Security considerations include:

- Restricting unnecessary traffic between VLANs.
- Allowing only required services and protocols.
- Limiting administrative access.
- Monitoring firewall logs.
- Testing rules after changes.

## 8. Verification Summary

| Test | Result |
|---|---|
| VLAN 10 interface configured | Passed |
| Static gateway configured | Passed |
| DHCP enabled | Passed |
| Fedora client received VLAN address | Passed |
| Internet connectivity | Passed |
| DNS resolution | Passed |
| Detailed inter-VLAN isolation testing | Not yet documented |

## 9. Future Improvements

- Record exact DHCP lease ranges.
- Document the complete Proxmox VLAN tagging configuration.
- Add sanitized firewall rule screenshots.
- Perform explicit inter-VLAN access-control tests.
- Document expected versus actual firewall behavior.
- Expand the lab with additional VLANs.

## Lessons Learned

- VLANs allow multiple logical networks to share network infrastructure.
- DHCP must be configured for each VLAN requiring dynamic addressing.
- Firewall policies are essential for controlling traffic between VLANs.
- Connectivity testing should include IP addressing, gateway access, internet access, and DNS.
- Network segmentation should be validated rather than assumed.
