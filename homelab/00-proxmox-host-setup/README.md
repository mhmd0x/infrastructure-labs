# Proxmox Host Setup

## Objective

Build a clean Proxmox VE host to support hands-on labs for Linux administration, virtualization, networking, cloud infrastructure, automation, and platform engineering.

## Environment

| Component | Details |
|---|---|
| Hypervisor | Proxmox VE 9.2 |
| Base OS | Debian 13 (Trixie) |
| CPU | AMD Ryzen 7 3700X |
| CPU Cores / Threads | 8 cores / 16 threads |
| Memory | 16 GB DDR4 |
| System Disk | Samsung 970 EVO Plus 500 GB NVMe |
| Lab Storage | Kingston 1 TB NVMe |

## Host Configuration

| Setting | Value |
|---|---|
| Hostname | `pve01.lab.local` |
| Management IP | `192.168.68.83/22` |
| Default Gateway | `192.168.68.1` |
| Linux Bridge | `vmbr0` |
| Physical NIC | `nic0` |

## Storage Layout

The host uses two NVMe drives with separate roles.

### System Storage

The 500 GB Samsung NVMe contains:

- Proxmox VE
- `local`
- `local-lvm`
- VM disk storage
- Container storage

### Lab Storage

The 1 TB Kingston NVMe is configured as dedicated supporting storage.

Proxmox storage ID:

```text
lab-storage
```

Mount point:

```text
/mnt/pve/lab-storage
```

Content types:

- ISO images
- Backups
- Container templates
- Snippets

See [Storage Layout](storage-layout.md) for the detailed configuration.

## Work Completed

1. Performed a clean Proxmox VE installation.
2. Verified CPU, memory, disks, and virtualization support.
3. Configured static management networking.
4. Configured the Proxmox no-subscription repository.
5. Updated the host packages and kernel.
6. Wiped and repurposed the secondary NVMe.
7. Created an `ext4` filesystem for dedicated lab storage.
8. Configured persistent mounting through `/etc/fstab`.
9. Registered the secondary disk as Proxmox directory storage.
10. Validated networking, storage, and system status.

## Validation

The following checks were performed during the build:

```bash
free -h
lsblk
lsblk -f
findmnt /mnt/pve/lab-storage
df -h /mnt/pve/lab-storage
pvesm status
```

Validation confirmed:

- 16 GB RAM detected correctly
- Proxmox host networking operational
- Secondary NVMe mounted successfully
- `lab-storage` available in Proxmox
- Storage mounted with the expected filesystem and options

## Documentation

Detailed configuration is available in:

- [Hardware Inventory](hardware-inventory.md)
- [Network Layout](network-layout.md)
- [Storage Layout](storage-layout.md)

## Lessons Learned

- Verify hardware and disk layout before deploying workloads.
- Confirm disk identity before destructive storage operations.
- Use filesystem UUIDs for persistent mounts.
- Validate `/etc/fstab` changes before rebooting.
- Separate active VM storage from supporting ISO and backup storage where practical.
- Validate infrastructure changes at both the operating system and hypervisor levels.

## Next Steps

- Upload RHEL installation media
- Deploy the first RHEL virtual machine
- Begin RHCSA administration labs
- Add additional networking and virtualization exercises