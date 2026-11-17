# OPNsense HTTPS Certificate Management

## Objective

Configure certificate-based HTTPS for the OPNsense web management interface using an internal Certificate Authority (CA).

## Lab Environment

- Hypervisor: Proxmox VE 8.4.1
- Firewall: OPNsense virtual machine
- Certificate Authority: Homelab Root CA
- Client: Fedora Linux workstation
- Management network: Isolated virtual LAN

## Certificate Authority

Created an internal root CA using OPNsense's certificate management interface.

Configuration:
- Description: Homelab Root CA
- Type: Internal Certificate Authority
- Digest algorithm: SHA-256
- Validity: 10 years
- Organization: Bailey Homelab
- Organizational unit: Infrastructure Security

The CA is responsible for signing certificates used by internal homelab services.

## Web GUI Server Certificate

Created a server certificate signed by the Homelab Root CA.

Configuration:
- Description: OPNsense Web GUI TLS 2026
- Certificate type: Server Certificate
- Key algorithm: RSA-4096
- Digest algorithm: SHA-256
- Issuer: Homelab Root CA
- Validity: 397 days
- Common Name: opnsense.home.arpa

Subject Alternative Names:
- DNS: opnsense.home.arpa
- IP: 192.168.100.1

The SAN entries allow the certificate to identify the firewall using either its DNS hostname or management IP address.

## HTTPS Configuration

Configured the OPNsense web management interface:

1. Navigated to System > Settings > Administration.
2. Selected HTTPS as the management protocol.
3. Assigned the newly generated server certificate.
4. Retained TCP port 443.
5. Applied the configuration.
6. Reconnected to the management interface using HTTPS.

## Certificate Verification

Downloaded the public CA certificate and inspected it using OpenSSL.

Example verification command:

```bash
openssl x509 -in root-ca.crt -noout -subject -issuer -dates -fingerprint -sha256
```

Verified:
- Certificate subject and issuer
- Validity dates
- SHA-256 fingerprint

The server certificate was also inspected through Firefox.

## Security Considerations

- The internal CA is not automatically trusted by operating systems or browsers.
- Client systems must explicitly trust the CA to establish a trusted certificate chain.
- The CA private key must never be published to GitHub.
- The server certificate must be renewed before expiration.
- HTTPS encrypts management traffic but does not replace firewall access controls.

## Lessons Learned

- Certificate trust and HTTPS encryption are separate concepts.
- Subject Alternative Names are important for hostname and IP validation.
- An internal CA provides control over certificates for private infrastructure.
- Certificate deployment should be followed by client-side validation.

## Future Improvements

- Configure trusted CA distribution to authorized management devices.
- Verify HTTPS access without browser certificate exceptions.
- Implement a certificate renewal procedure.
- Test certificate replacement and recovery.
