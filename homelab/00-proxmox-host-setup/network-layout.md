# Network Layout

## Proxmox Management Network

| Setting | Value |
|---|---|
| Hostname | `pve01.lab.local` |
| Management IP | `192.168.68.83/22` |
| Default Gateway | `192.168.68.1` |
| Linux Bridge | `vmbr0` |
| Physical NIC | `nic0` |

## Topology

```text
Internet
   |
Home Router
192.168.68.1
   |
LAN
   |
Proxmox Host
192.168.68.83
   |
vmbr0
   |
Virtual Machines
```

## Bridge Configuration

`vmbr0` provides network connectivity between the physical LAN and virtual machines hosted on Proxmox.

The physical interface `nic0` is attached to `vmbr0`.

## Addressing Notes

The Proxmox management interface uses a static IP address:

```text
192.168.68.83/22
```

The default gateway is:

```text
192.168.68.1
```

A future improvement is to verify that `192.168.68.83` is either reserved for the Proxmox host in the router DHCP configuration or outside the DHCP allocation pool.

This helps prevent possible IP address conflicts.

## Validation

The network configuration can be checked with:

```bash
ip addr
ip route
cat /etc/network/interfaces
```

Key items to verify:

- `vmbr0` has the expected management IP
- the default route points to `192.168.68.1`
- `nic0` is attached to `vmbr0`
- the Proxmox web interface is reachable from the LAN

## Future Lab Expansion

As the lab grows, additional network segmentation may be introduced for:

- server workloads
- management traffic
- container or Kubernetes networks
- isolated testing environments
- VLAN labs