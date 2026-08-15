# Hardware Inventory

## Proxmox Host

| Component | Details |
|---|---|
| Hostname | `pve01.lab.local` |
| Motherboard | ASUS PRIME B550M-K |
| CPU | AMD Ryzen 7 3700X |
| CPU Cores / Threads | 8 cores / 16 threads |
| Virtualization | AMD-V |
| Memory | 16 GB DDR4 |
| System Disk | Samsung 970 EVO Plus 500 GB NVMe |
| Lab Storage Disk | Kingston NVMe 1 TB |
| Network Adapter | Realtek Ethernet |
| Hypervisor | Proxmox VE 9.2 |

## Memory

Installed memory:

- 2 × 8 GB DDR4
- Total: 16 GB

## Storage Devices

### System NVMe

Samsung 970 EVO Plus 500 GB

Used for:

- Proxmox OS
- `local`
- `local-lvm`
- VM disks

### Secondary NVMe

Kingston 1 TB NVMe

Used for:

- ISO images
- Backups
- Container templates
- Snippets

Mounted at:

```text
/mnt/pve/lab-storage
```

## Capacity Considerations

The current 16 GB RAM configuration is sufficient for Linux, RHCSA, networking, and lightweight infrastructure labs.

Larger nested virtualization environments, multi-node Kubernetes clusters, or VMware vSphere labs may require additional memory.

## Upgrade Notes

Potential future upgrades include:

- Additional RAM for larger multi-VM labs
- Dedicated hardware for VMware ESXi / vSphere testing
- Additional network interfaces for VLAN and routing labs
- Dedicated backup or network storage

## Validation

Hardware information was verified using tools such as:

```bash
lscpu
free -h
lsblk
dmidecode
```

The host currently provides:

- 8 CPU cores / 16 threads
- AMD-V virtualization support
- 16 GB RAM
- Two NVMe storage devices