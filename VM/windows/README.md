
# Windows VM

This VM runs Windows on VMware Workstation Pro for Windows administration, security testing, and Active Directory practice.

## Purpose

- Windows administration and security testing
- Active Directory and domain attack-path practice
- Windows security hardening

## Windows Setup

## Windows VM Setup

### 1. Download Windows ISO

- Windows 10: [Download ISO](https://www.microsoft.com/software-download/windows10ISO)
- Windows 11: [Download ISO](https://www.microsoft.com/software-download/windows11)
- Windows Server: [Evaluation Center](https://www.microsoft.com/evalcenter/)

### 2. Create a New Virtual Machine

- Create a new virtual machine in VMware.
- Select the appropriate Windows ISO.

### 3. Configure Windows VM

| Setting | Configuration |
|---|---|
| Operating System | Windows |
| Version | Match the selected Windows ISO |
| VM Name | Windows 10 / Windows 11 / Windows Server |
| Disk Size | Based on OS requirements |
| Memory | Based on OS requirements |
| Processors | 2 cores |
| Network Adapter | NAT |
| Display | Accelerate 3D Graphics |
| Advanced Setting | Disable side-channel mitigations for Hyper-V-enabled hosts |

> **Recommendation:** Allocate 4 cores, 4 GB RAM, and 80 GB storage as a starting configuration for Windows 11 and Windows Server. Increase RAM to 8 GB when running resource-intensive workloads or server roles.

### 4. Install Windows

- Follow the installation instructions.
- Complete the initial configuration.
- Verify that Windows boots successfully.

### 5. Test Network Connectivity

Open Command Prompt and test internet connectivity.

```cmd
ping -n 4 8.8.8.8
```

### 6. Set a Static IP Address

Configure the static IP address through **Network Properties**

| Setting | Value |
|---|---|
| IP Address (Windows 10)| `10.0.0.10` |
| IP Address (Windows 11)| `10.0.0.11` |
| IP Address (Windows Server)| `10.0.0.16` |
| Subnet Mask | `255.255.255.0` |
| Gateway | `10.0.0.1` |
| Primary DNS | `8.8.8.8` |
| Secondary DNS | `1.1.1.1` |

> **Note:** Verify that these addresses match your VMware NAT network configuration before applying them.

### 7. Verify Network Configuration

Open Command Prompt and check the network configuration.

```cmd
ipconfig
```

### 8. Test Connectivity

| Test                        | Command                | Test Purpose                                                         |
| --------------------------- | ---------------------- | -------------------------------------------------------------------- |
| Local Interface             | `ping -c 4 10.0.0.9`   | Verify the local IP address responds.                                |
| Gateway                     | `ping -c 4 10.0.0.1`   | Verify connectivity to the VMware NAT gateway.                       |
| Internet                    | `ping -c 4 8.8.8.8`    | Verify external IP connectivity without relying on DNS.              |
| DNS Resolution              | `ping -c 4 google.com` | Verify domain-name resolution and connectivity to the resolved host. |
| Kali Reachability        | `ping -c 4 10.0.0.2`   | Test connectivity to the Kali VM.                             |

>Note: Ping tests ICMP reachability. A failed ping does not necessarily mean the VM is unreachable; its firewall may block ICMP.

[**Windows 10 Connectivity Test**](image/w10-connectivity-test/)

[**Windows 10 Connectivity Test**](image/w11-connectivity-test/)

[**Windows Server Connectivity Test**](image/ws-connectivity-test/)

> **Note:** Temporarily disable Windows Firewall when testing host reachability in the isolated lab environment. Re-enable it after testing.

### 9. Create a Snapshot

Create a snapshot after completing the initial setup and verifying network connectivity.

- **Purpose:** Preserve a known-good VM state for recovery.
- **Recommended timing:** Before changing the IP address to a static configuration.
- **Recovery:** Revert to the snapshot if a configuration change causes problems.

> A snapshot supports rollback but is not a substitute for an independent backup.

## Troubleshooting

Refer to the [Troubleshooting Guide](/Troubleshooting/README.md) for common issues and solutions.