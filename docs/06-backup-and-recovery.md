# OPNsense Backup and Recovery

## Objective

Create an encrypted backup of the OPNsense firewall configuration, verify its integrity using SHA-256, and maintain independent backup copies for disaster recovery.

## Lab Environment

- Hypervisor: Proxmox VE 8.4.1
- Firewall: OPNsense VM 100
- Transfer workstation: Fedora Live VM 101
- Backup workstation: Windows 11 with PowerShell
- Removable storage: 8 GB USB flash drive, exFAT
- USB filesystem label: HOMELAB_BAK

## 1. Export the OPNsense Configuration

Navigate to:

System > Configuration > Backups

Under Download:

1. Select "Do not backup RRD data".
2. Enable "Encrypt this configuration file".
3. Enter and confirm a strong encryption password.
4. Select "Download configuration".

The downloaded XML file contains the encrypted configuration backup.

Store the encryption password separately in a password manager.

Never commit actual configuration backups to a public Git repository.

## 2. Verify the Download

On the Fedora workstation:

```bash
ls -lh ~/Downloads/
```

Confirm the encrypted configuration file and exported public CA certificate are present.

## 3. Prepare Removable Storage

The USB flash drive was identified and formatted on Windows using PowerShell.

Identify removable disks:

```powershell
Get-Disk | Format-Table Number, FriendlyName, BusType, Size
```

Verify the selected disk before formatting.

Example formatting command:

```powershell
Format-Volume -DriveLetter E -FileSystem exFAT -NewFileSystemLabel "HOMELAB_BAK"
```

WARNING: Formatting erases existing data on the selected volume.

## 4. Configure Proxmox USB Passthrough

Connect to the Proxmox host using SSH.

Identify the USB device:

```bash
lsusb
lsblk -o NAME,SIZE,FSTYPE,LABEL,MODEL,TRAN,MOUNTPOINTS
```

Assign the USB device to Fedora VM 101.

Example command:

```bash
qm set 101 -usb0 host=abcd:1234
```

The USB vendor and product identifiers must be replaced with the actual identifiers reported by lsusb.

Verify:

```bash
qm config 101 | grep usb
```

## 5. Copy Backup Files to USB

Inside Fedora, identify the mounted USB filesystem:

```bash
lsblk -o NAME,SIZE,FSTYPE,LABEL,MOUNTPOINTS
```

Set the USB mount point:

```bash
USB="$(findmnt -no TARGET /dev/sda1)"
```

Create a backup directory:

```bash
mkdir -p "$USB/OPNsense"
```

Copy the encrypted configuration:

```bash
cp -v ~/Downloads/config-OPNsense*.xml "$USB/OPNsense/"
```

Copy the public CA certificate:

```bash
cp -v ~/Downloads/"Homelab Root CA_crt.pem" "$USB/OPNsense/"
```

Do not copy or publish the CA private key.

## 6. Verify SHA-256 Integrity

Calculate the original configuration hash:

```bash
sha256sum ~/Downloads/config-OPNsense*.xml
```

Calculate the USB copy hash:

```bash
sha256sum "$USB"/OPNsense/config-OPNsense*.xml
```

Repeat the comparison for the public CA certificate.

Both files produced matching SHA-256 hashes between the Fedora source and USB destination.

This confirms byte-for-byte integrity of the copied files.

## 7. Safely Remove the USB Device

Unmount the USB filesystem inside Fedora:

```bash
udisksctl unmount -b /dev/sda1
```

Verify it is no longer mounted:

```bash
lsblk -o NAME,SIZE,FSTYPE,LABEL,MOUNTPOINTS
```

Remove the USB passthrough assignment on Proxmox:

```bash
qm set 101 -delete usb0
```

Verify:

```bash
qm config 101 | grep usb
```

The QEMU monitor was also checked to confirm the physical USB device was no longer attached to the VM.

## 8. Verify Files on Windows

Reconnect the USB drive to the Windows workstation.

Identify the drive:

```powershell
Get-Volume | Where-Object FileSystemLabel -eq "HOMELAB_BAK"
```

List the backup files:

```powershell
Get-ChildItem "E:\OPNsense"
```

Calculate SHA-256 hashes:

```powershell
Get-ChildItem "E:\OPNsense" -File |
    Get-FileHash -Algorithm SHA256 |
    Format-List Path, Hash
```

Both hashes matched the original values calculated in Fedora.

## 9. Create an Independent Windows Copy

Create a dated backup directory:

```powershell
$BackupRoot = "$HOME\Homelab\Backups\OPNsense"
$BackupDate = Get-Date -Format "yyyy-MM-dd"
$Destination = Join-Path $BackupRoot $BackupDate

New-Item -ItemType Directory -Path $Destination -Force
```

Copy the files:

```powershell
Copy-Item "E:\OPNsense\*" -Destination $Destination
```

Verify the Windows copies against the USB originals:

```powershell
Get-ChildItem "E:\OPNsense" -File | ForEach-Object {
    $Copy = Join-Path $Destination $_.Name

    [PSCustomObject]@{
        File = $_.Name
        Match = (Get-FileHash $_.FullName).Hash -eq
                (Get-FileHash $Copy).Hash
    }
}
```

Both files returned True, confirming matching SHA-256 hashes.

## 10. Backup Storage Strategy

The following copies were established:

- Primary configuration: Running OPNsense VM
- Independent backup: Windows workstation
- Portable backup: Removable USB flash drive

The USB backup is designated for offsite storage.

The active configuration is not considered a separate restorable backup copy.

A complete 3-2-1 strategy should maintain three independent copies, use at least two storage media or failure domains, and keep at least one copy offsite.

## 11. Security Considerations

- Encrypt configuration backups before exporting them.
- Store encryption passwords separately.
- Never upload configuration backups or private keys to public GitHub repositories.
- Verify copied files using cryptographic hashes.
- Safely unmount removable storage before disconnecting it.
- Protect offsite storage against theft, damage, and unauthorized access.
- Review backup retention and rotation procedures regularly.

## 12. Recovery Testing

Status: NOT YET TESTED

Planned recovery exercise:

1. Create an isolated OPNsense test VM.
2. Obtain a verified encrypted configuration backup.
3. Restore the configuration using its encryption password.
4. Verify interface assignments and firewall rules.
5. Confirm expected network connectivity.
6. Validate certificate and management access.
7. Record recovery time and any issues encountered.

A successful integrity check confirms file consistency but does not prove that a backup can be restored.

## Lessons Learned

- USB passthrough allows removable storage to be accessed by isolated VMs.
- Network isolation can complicate direct file transfers.
- SHA-256 verification helps detect corruption during transfers.
- PowerShell and Linux provide complementary administrative tools.
- A backup strategy is incomplete without documented recovery testing.
- Clear operational procedures make infrastructure easier to maintain and recover.
