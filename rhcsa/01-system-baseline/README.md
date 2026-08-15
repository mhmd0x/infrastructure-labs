# RHCSA Lab 01 - System Baseline

## Objective

Build familiarity with core RHEL system inspection commands and establish a clean baseline for future RHCSA labs.

This lab focuses on:

- identifying the operating system and kernel
- checking the current user and groups
- inspecting files and directories
- reviewing memory and storage usage
- checking network configuration
- verifying software repositories
- using basic DNF package queries
- using simple shell pipelines and text filtering

## Environment

| Item | Value |
|---|---|
| Hostname | `rhel01.lab.local` |
| Operating System | Red Hat Enterprise Linux 10.2 |
| Architecture | `x86_64` |
| Virtualization | KVM |
| Hypervisor | Proxmox VE |
| VM CPU | 2 vCPU |
| VM Memory | 4 GB |
| VM Disk | 32 GiB |
| Network Interface | `ens18` |
| IPv4 Address | `192.168.68.84/22` |
| Default Gateway | `192.168.68.1` |
| Administrative User | `mhmd` |

## System Identification

### Host Information

```bash
hostnamectl
```

Used to inspect system identity and basic platform information.

Important fields include:

- static hostname
- operating system
- kernel
- architecture
- virtualization type

Example values from the lab:

```text
Static hostname: rhel01.lab.local
Operating System: Red Hat Enterprise Linux 10.2
Architecture: x86-64
Virtualization: kvm
```

### Kernel Version

```bash
uname -r
```

Displays the currently running kernel release.

Example:

```text
6.12.0-211.7.3.el10_2.x86_64
```

### RHEL Release

```bash
cat /etc/redhat-release
```

Displays the installed Red Hat Enterprise Linux release.

This is useful for identifying the operating system version independently from the kernel version.

## User and Identity Information

### Current User

```bash
whoami
```

Displays the current effective username.

Example:

```text
mhmd
```

### User and Group Information

```bash
id
```

Displays information about the current user, including:

- UID
- primary GID
- supplementary groups

Membership in the `wheel` group allows the user to perform administrative tasks using `sudo`.

### Current Working Directory

```bash
pwd
```

Prints the full path of the current working directory.

Example:

```text
/home/mhmd
```

## File Listing

```bash
ls -lah
```

Options used:

| Option | Meaning |
|---|---|
| `-l` | Long listing format |
| `-a` | Include hidden files |
| `-h` | Human-readable file sizes |

The `-a` option includes files beginning with a period, such as:

```text
.bashrc
.ssh
```

The `-h` option displays sizes using readable units such as:

```text
4.0K
12M
2.1G
```

## Storage Inspection

### Block Devices

```bash
lsblk
```

Displays the system's block storage layout.

This can include:

- physical or virtual disks
- partitions
- LVM logical volumes
- mount points

### Filesystem Usage

```bash
df -h
```

Displays mounted filesystem usage in human-readable units.

Useful fields include:

- filesystem
- total size
- used space
- available space
- usage percentage
- mount point

Key distinction:

```text
lsblk  -> storage device layout
df -h  -> filesystem space usage
```

## Memory Inspection

```bash
free -h
```

Displays RAM and swap usage in human-readable units.

Important fields include:

- total
- used
- free
- shared
- buff/cache
- available
- swap

Key distinction:

```text
df -h   -> disk/filesystem usage
free -h -> memory and swap usage
```

## Network Inspection

### Interface Addresses

```bash
ip addr
```

Displays network interfaces and their assigned addresses.

Important information includes:

- interface names
- interface state
- MAC addresses
- IPv4 addresses
- IPv6 addresses

The main interface in this lab is:

```text
ens18
```

The IPv4 address is:

```text
192.168.68.84/22
```

### Routing Table

```bash
ip route
```

Displays the system routing table.

The important default route in this lab is:

```text
default via 192.168.68.1 dev ens18
```

This means traffic for destinations outside the directly connected network is sent through the gateway:

```text
192.168.68.1
```

using interface:

```text
ens18
```

## RHEL Repository Configuration

The system initially had no enabled DNF repositories because it had not yet been registered with Red Hat.

### Check Subscription Status

```bash
sudo subscription-manager status
```

### Check System Identity

```bash
sudo subscription-manager identity
```

### Register the System

```bash
sudo subscription-manager register
```

The Red Hat Developer account credentials were used for system registration.

Local Linux accounts such as `root` and `mhmd` are separate from the Red Hat account used for subscription registration.

After successful registration, the system gained access to the standard RHEL repositories.

## Enabled Repositories

```bash
sudo dnf repolist
```

Enabled repositories included:

```text
rhel-10-for-x86_64-baseos-rpms
rhel-10-for-x86_64-appstream-rpms
```

These repositories provide the core RHEL packages and additional application packages used by DNF.

## Checking for Updates

```bash
sudo dnf check-update
```

Checks whether newer versions of installed packages are available.

This command does not install the updates.

Examples of packages with available updates included:

```text
qemu-guest-agent
redhat-release
selinux-policy
systemd
vim-enhanced
```

## DNF Package Queries

### Package Information

```bash
dnf info qemu-guest-agent
```

Displays package metadata such as:

- package name
- version
- release
- architecture
- repository
- installed size
- summary
- description

The `qemu-guest-agent` package runs inside a virtual machine and allows communication between the guest operating system and the virtualization platform.

### Check Whether a Package Is Installed

```bash
dnf list installed qemu-guest-agent
```

Checks whether the specified package is installed and displays the installed version and architecture.

### Search for Packages

```bash
dnf search cockpit
```

Searches package metadata for packages related to the specified term.

Key distinction:

```text
dnf info <package>            -> package details
dnf list installed <package>  -> check installed package
dnf search <term>             -> search available packages
```

## Shell Pipelines

The pipe operator:

```text
|
```

takes the output of one command and sends it as input to another command.

General pattern:

```bash
command1 | command2
```

## Using `head`

```bash
dnf list installed | head
```

Displays the first 10 lines of the output by default.

To specify a different number of lines:

```bash
dnf list installed | head -n 5
```

This displays only the first five lines.

## Using `grep`

```bash
dnf list installed | grep qemu
```

Filters the package list and displays only lines containing:

```text
qemu
```

### Case-Insensitive Search

```bash
dnf list installed | grep -i qemu
```

The `-i` option makes the search case-insensitive.

This allows values such as:

```text
qemu
QEMU
QeMu
```

to match.

Example demonstrating the difference:

```bash
echo QEMU | grep qemu
```

This does not normally match because the search is case-sensitive.

Using:

```bash
echo QEMU | grep -i qemu
```

matches successfully.

## Counting Lines

```bash
dnf list installed | grep -i qemu | wc -l
```

The `wc` command performs counting operations.

The `-l` option specifically counts lines.

This pipeline:

1. lists installed packages
2. filters for lines containing `qemu`
3. counts the matching lines

## Counting Installed Packages

```bash
dnf list installed | wc -l
```

Counts the lines produced by `dnf list installed`.

Because DNF output can contain headers, the result may not represent the exact package count.

A cleaner method is:

```bash
rpm -qa | wc -l
```

## RPM Query Basics

```bash
rpm -qa
```

Options:

| Option | Meaning |
|---|---|
| `-q` | Query |
| `-a` | All installed packages |

Therefore:

```bash
rpm -qa | wc -l
```

queries all installed RPM packages and counts them directly.

## Key Command Comparisons

| Command | Purpose |
|---|---|
| `hostnamectl` | System identity and platform summary |
| `uname -r` | Running kernel release |
| `cat /etc/redhat-release` | Installed RHEL release |
| `whoami` | Current effective user |
| `id` | UID, GID, and group membership |
| `pwd` | Current working directory |
| `ls -lah` | Detailed directory listing |
| `lsblk` | Block device layout |
| `df -h` | Filesystem disk usage |
| `free -h` | Memory and swap usage |
| `ip addr` | Network interfaces and addresses |
| `ip route` | Routing table and default gateway |
| `dnf repolist` | Enabled software repositories |
| `dnf info` | Package information |
| `dnf search` | Search package metadata |
| `grep` | Filter matching text |
| `head` | Display first lines of output |
| `wc -l` | Count lines |
| `rpm -qa` | Query all installed RPM packages |

## What I Learned

This lab reinforced the difference between several commonly confused areas:

- operating system version and kernel version are separate
- hostname identifies the system
- `whoami` shows the current user while `id` provides detailed identity information
- `lsblk` shows storage layout while `df` shows filesystem usage
- `df` is for disk usage while `free` is for memory usage
- `ip addr` shows interface addressing while `ip route` shows routing decisions
- DNF repositories must be available before packages can be installed or updated
- package information, package searching, and installed-package checks use different DNF commands
- shell pipelines allow small commands to be combined into more useful operations
- `grep`, `head`, and `wc` are useful tools for filtering and summarizing command output

## Next Lab

Continue with package and RPM administration, starting with:

```bash
rpm -q qemu-guest-agent
```

Future exercises will include:

- querying RPM packages
- installing and removing packages
- inspecting package files
- working with DNF history
- users and groups
- permissions
- systemd services
- logs
- storage and LVM
- networking
- SELinux
- firewalld