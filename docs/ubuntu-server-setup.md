# Ubuntu Server Setup

## Goal

Build a Linux server for administration, networking, and future automation projects.

## Environment

| Component | Configuration |
|---|---|
| Host | Windows |
| Hypervisor | VirtualBox |
| Guest OS | Ubuntu Server 26.04.1 LTS |
| Hostname | lab-ubuntu-01 |
| Memory | 4 GB |
| CPUs | 2 |
| Disk | 30 GB VDI, dynamically allocated |
| Storage | LVM with ext4 |
| Networking | Bridged adapter with DHCP |

## Installation

- Created the VM and attached the Ubuntu Server ISO.
- Disabled unattended installation to configure Ubuntu manually.
- Selected standard Ubuntu Server.
- Used the VM's virtual disk with guided LVM partitioning.
- Created a standard user account.
- Installed OpenSSH Server.
- Skipped Ubuntu Pro and optional server snaps.
- Completed installation and rebooted with the ISO detached.

## Updates and Baseline

Verified the hostname and operating system:

<img width="642" height="810" alt="ChatGPT Image Nov 5, 2026, 01_15_01 AM" src="https://github.com/user-attachments/assets/3fb81e28-a1f7-4008-8882-d31e39ff83e5" />

```bash
hostnamectl
```

Updated packages and rebooted:

```bash
sudo apt update
sudo apt upgrade
sudo reboot
```

Confirmed successful login after reboot. Created a VirtualBox snapshot named `01-Clean-Install-Updated` before further configuration.

## SSH Verification

Checked the SSH service and socket:

```bash
systemctl status ssh --no-pager
systemctl status ssh.socket --no-pager
```

The SSH service initially showed `inactive (dead)`, while `ssh.socket` showed `active (listening)` on TCP port 22. Socket activation allows systemd to start the SSH service when a connection arrives.

The `--no-pager` option prints output directly instead of opening an interactive viewer.

## Firewall

Configured UFW to deny incoming connections by default, allow outgoing connections, and permit SSH:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status verbose
```

UFW was active and SSH was allowed for IPv4 and IPv6.

<img width="1150" height="551" alt="ufw status verbose" src="https://github.com/user-attachments/assets/a7e0ff97-de00-4b10-9401-c705154e6f9b" />

## Remote Access

Changed the VirtualBox network adapter from NAT to bridged mode, allowing the VM to receive an address on the home network.

Checked the assigned IPv4 address:

```bash
ip -4 addr show enp0s3
```

Connected from Windows PowerShell using the Ubuntu username and assigned address:

```powershell
ssh andrew@<VM-IP>
```

Successfully authenticated and reached the Ubuntu shell from Windows.

<img width="1619" height="972" alt="ssh-login-redacted png" src="https://github.com/user-attachments/assets/8462e070-f8d9-43b5-9a7c-9091fc90ac04" />


The address is assigned through DHCP and may change.

## Basic File Permissions

Created a practice directory and file:

```bash
mkdir -p ~/home-lab/practice
cd ~/home-lab/practice
touch notes.txt
ls -l
pwd
```

The file initially had permissions `-rw-rw-r--`: owner and group could read/write; others could read.

Restricted access to the owner:

```bash
chmod 600 notes.txt
ls -l notes.txt
```

Confirmed permissions changed to `-rw-------`.

## Next Steps

- Practice users, groups, processes, and services.
- Deploy a web service.
- Introduce a controlled failure and document troubleshooting.
- Use Git locally to track documentation changes.
