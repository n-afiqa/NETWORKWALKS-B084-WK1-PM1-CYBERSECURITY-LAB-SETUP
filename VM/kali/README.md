# Kali Linux VM

This VM runs Kali Linux on VMware Workstation Pro as the primary environment for offensive security testing.

## Purpose

- Penetration testing and vulnerability assessment
- Network reconnaissance and enumeration
- Security tools and CTF practice

## Kali Linux Setup

### 1. Download Kali Linux

- **Download:** [Kali Linux](https://www.kali.org/get-kali/)
- Select the appropriate ISO image for VMware.

### 2. Create New Virtual Machine

- Create a new virtual machine in VMware.
- Locate and select the downloaded Kali Linux ISO file.

### 3. Configure Kali VM

| Setting | Configuration |
|---|---|
| Operating System | Linux |
| Version | Debian 64-bit |
| VM Name | Kali |
| Disk Size | 80 GB |
| Memory | 4 GB |
| Processors | 2 cores (1 processor × 2 cores) |
| Network Adapter | NAT |
| Display | Accelerate 3D Graphics |
| Advanced Setting | Disable side-channel mitigations for Hyper-V-enabled hosts |

### 4. Update Package

Update the package lists and upgrade installed packages.

```bash
sudo apt update && sudo apt upgrade
```

### 5. Test Network Connectivity

```bash
ping -c 4 8.8.8.8
```

### 6. Set a Static IP Address

Configure the static IP address through NetworkManager.

| Setting | Value |
|---|---|
| IP Address | `10.0.0.2` |
| Subnet Mask | `255.255.255.0` |
| Gateway | `10.0.0.1` |
| Primary DNS | `8.8.8.8` |
| Secondary DNS | `1.1.1.1` |

```bash
nmcli connection show
sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns "8.8.8.8 1.1.1.1"
sudo nmcli connection up "Wired connection 1"
```

> **Note:** Verify that these addresses match your VMware NAT network configuration before applying them.

### 7. Verify Network Configuration

```bash
ip a show eth0
```

### 8. Test Connectivity

| Test                        | Command                | Test Purpose                                                         |
| --------------------------- | ---------------------- | -------------------------------------------------------------------- |
| Local Interface             | `ping -c 4 10.0.0.2`   | Verify the local IP address responds.                                |
| Gateway                     | `ping -c 4 10.0.0.1`   | Verify connectivity to the VMware NAT gateway.                       |
| Internet                    | `ping -c 4 8.8.8.8`    | Verify external IP connectivity without relying on DNS.              |
| DNS Resolution              | `ping -c 4 google.com` | Verify domain-name resolution and connectivity to the resolved host. |
| Android Reachability        | `ping -c 4 10.0.0.9`   | Test connectivity to the Android-x86 VM.                             |
| Windows 10 Reachability     | `ping -c 4 10.0.0.10`  | Test connectivity to the Windows 10 VM.                              |
| Windows 11 Reachability     | `ping -c 4 10.0.0.11`  | Test connectivity to the Windows 11 VM.                              |
| Windows Server Reachability | `ping -c 4 10.0.0.16`  | Test connectivity to the Windows Server VM.                          |

>Note: Ping tests ICMP reachability. A failed ping does not necessarily mean the VM is unreachable; its firewall may block ICMP.

[**Kali Connectivity Test**](image/)

### 9. Create a Snapshot

Create a snapshot after completing the initial setup and verifying network connectivity.

- **Purpose:** Preserve a known-good VM state for recovery.
- **Recommended timing:** Before changing the IP address to a static configuration.
- **Recovery:** Revert to the snapshot if a configuration change causes problems.

> A snapshot supports rollback but is not a substitute for an independent backup.


## Troubleshooting

Refer to the [Troubleshooting Guide](/Troubleshooting/README.md) for common issues and solutions.