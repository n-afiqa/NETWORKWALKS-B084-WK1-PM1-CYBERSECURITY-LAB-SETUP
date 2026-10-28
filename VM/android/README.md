# Android VM

This VM runs Android-x86 on VMware Workstation Pro for mobile security testing and application analysis.

## Purpose

- Mobile security testing
- Android application analysis
- Android OS behavior analysis

## Android Setup

### 1. Download Android-x86

- **Download:** [Android-x86 on SourceForge](https://sourceforge.net/projects/android-x86/files/)
- Navigate to **Release 9.0** and download `android-x86_64-9.0-r2.iso`.

### 2. Create a New Virtual Machine

- Create a new virtual machine in VMware.
- Select the downloaded Android-x86 ISO file.

### 3. Configure the Android VM

| Setting | Configuration |
|---|---|
| Guest Operating System | Linux |
| Version | Other Linux 5.x kernel 64-bit |
| VM Name | Android |
| Disk Size | 10 GB |
| Memory | 2 GB |
| Processors | 2 cores |
| Firmware | UEFI |
| Network Adapter | NAT |
| Display | Accelerate 3D Graphics |
| Advanced Setting | Disable side-channel mitigations for Hyper-V-enabled hosts |

### 4. Install Android-x86

- Boot the VM from the Android-x86 ISO.
- Select **Installation – Install Android-x86 to harddisk** from the boot menu.
- Select the virtual disk where Android-x86 will be installed.
- Choose **Create/Modify partitions**.
- Select **New → Primary**, allocate the available disk space, then select **Bootable**.
- Select **Write**, type `yes` to confirm, then choose **Quit**.
- Select the newly created partition and format it as **ext4**.
- Confirm installation of the bootloader:
   - Select **Yes** to install GRUB.
   - Select **Yes** to install `/system` as read-write if prompted.
- Wait for the installation to finish.
- Select **Reboot** and disconnect the ISO from the VM.
- Boot from the virtual disk and complete the Android initial setup.

### 5. Install Termux

- **Download:** [Termux on F-Droid](https://f-droid.org/packages/com.termux/)
- Alternatively, use the [official Termux GitHub repository](https://github.com/termux/termux-app).
- If installing an APK manually, allow installation from the relevant source when prompted.
- Open Termux after installation.

### 6. Test Network Connectivity

Open Termux and install the required utilities if necessary.

```bash
pkg update
pkg install iputils
ping -c 4 8.8.8.8
```

### 7. Set a Static IP Address

Configure the static IP address through NetworkManager.
- Open Settings >> Network & Internet >> Wi-fi
- Turn off DHCP / switch to Static IP
- configure as follows:

| Setting | Value |
|---|---|
| IP Address | `10.0.0.9` |
| Subnet Mask | `255.255.255.0` |
| Gateway | `10.0.0.1` |
| Primary DNS | `8.8.8.8` |
| Secondary DNS | `1.1.1.1` |

> **Note:** Verify that these addresses match your VMware NAT network configuration before applying them.

### 7. Verify Network Configuration

```bash
ip addr
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


[**Android Connectivity Test**](image/)

### 9. Create a Snapshot

Create a snapshot after completing the initial setup and verifying network connectivity.

- **Purpose:** Preserve a known-good VM state for recovery.
- **Recommended timing:** Before changing the IP address to a static configuration.
- **Recovery:** Revert to the snapshot if a configuration change causes problems.

> A snapshot supports rollback but is not a substitute for an independent backup.


## Troubleshooting

Refer to the [Troubleshooting Guide](/Troubleshooting/README.md) for common issues and solutions.