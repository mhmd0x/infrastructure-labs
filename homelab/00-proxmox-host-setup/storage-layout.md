# Storage Layout

## Overview

The Proxmox host uses two NVMe drives with separate roles.

| Storage Device | Capacity | Purpose |
|---|---:|---|
| Samsung 970 EVO Plus | 500 GB | Proxmox OS and VM storage |
| Kingston NVMe | 1 TB | ISO images, backups, templates, and snippets |

## System Disk

The 500 GB Samsung NVMe contains the Proxmox installation and default LVM storage layout.

Main components include:

- EFI boot partition
- `pve-root`
- `pve-swap`
- `pve-data`

The `pve-data` thin pool is exposed in Proxmox as:

```text
local-lvm
```

It is used primarily for:

- VM disk images
- Containers

## Secondary Lab Storage

The 1 TB Kingston NVMe was wiped and repurposed for dedicated lab storage.

Filesystem:

```text
ext4
```

Mount point:

```text
/mnt/pve/lab-storage
```

Proxmox storage ID:

```text
lab-storage
```

Configured content types:

- ISO images
- Backups
- Container templates
- Snippets

## Persistent Mount

The filesystem is mounted using its UUID through `/etc/fstab`.

The `/etc/fstab` entry uses the filesystem UUID rather than the device name so that the mount remains consistent across reboots.

Mount options:

```text
defaults,noatime
```

The storage configuration was validated with:

```bash
systemctl daemon-reload
mount -a
findmnt /mnt/pve/lab-storage
df -h /mnt/pve/lab-storage
```

The validation confirmed:

- Filesystem type: `ext4`
- Mount point: `/mnt/pve/lab-storage`
- Mount options include `rw,noatime`
- Total usable capacity is approximately 916 GB
- The filesystem is available and mounted successfully

## Proxmox Storage Configuration

The secondary NVMe was registered in Proxmox as directory storage.

Configuration:

| Setting | Value |
|---|---|
| Storage ID | `lab-storage` |
| Type | Directory |
| Path | `/mnt/pve/lab-storage` |
| Shared | No |
| Enabled | Yes |

Enabled content types:

- Backup
- ISO image
- Container template
- Snippets

VM disk images are not currently stored on `lab-storage`.

## Storage Strategy

The storage layout separates active VM storage from supporting lab files.

### `local-lvm`

Primary use:

- VM disk images
- Containers

### `lab-storage`

Primary use:

- Operating system ISO images
- Proxmox backups
- Container templates
- Snippets
- Supporting lab files

This layout allows the Proxmox system disk and LVM thin pool to remain focused on virtual machine workloads while the secondary NVMe provides dedicated capacity for installation media and backup-related content.

## Verification

The following commands can be used to inspect the storage configuration:

```bash
lsblk
```

```bash
lsblk -f
```

```bash
findmnt /mnt/pve/lab-storage
```

```bash
df -h /mnt/pve/lab-storage
```

```bash
pvesm status
```

Expected Proxmox storage entries include:

```text
local
local-lvm
lab-storage
```

## Future Improvements

As the lab grows, the storage design may be expanded to support:

- Dedicated backup targets
- NFS storage
- Network-attached storage
- Additional VM storage
- Snapshot and recovery testing
- Backup and restore exercises
- Storage performance testing
- Capacity monitoring and alerts
- Disaster recovery labs

## Lessons Learned

- Always verify the correct disk before running destructive storage commands.
- Filesystem UUIDs are preferable to device names for persistent mounts.
- Test `/etc/fstab` changes with `mount -a` before rebooting.
- Reload systemd after modifying mount configuration when required.
- Separating VM disks from ISO and backup storage makes the environment easier to manage.
- Storage configuration should be validated at both the Linux filesystem level and the Proxmox storage level.