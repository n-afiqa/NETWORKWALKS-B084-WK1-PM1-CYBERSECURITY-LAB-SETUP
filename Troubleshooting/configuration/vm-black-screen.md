
# VMware VM Black Screen due to Misconfiguration

## 1. Issue Overview

Kali Linux, Windows 11, and Android-x86 encountered black-screen or startup issues during VM boot or installation. Each issue had a different cause related to VM graphics settings, host power settings, or installation choices.

## 2. Root Cause and Solution

### Kali Linux

**Root Cause:** 3D graphics acceleration was disabled in VMware settings.

**Solution:** Re-enable **Accelerate 3D Graphics** in the VM's display settings. Kali booted normally afterward.

### Windows 11

**Root Cause:** 3D graphics acceleration was enabled, and the installation was interrupted by the laptop's screen timeout after approximately five minutes.

**Solution:**
- Disabled **Accelerate 3D Graphics** in VMware display settings.
- Disable the laptop's screen timeout temporarily.
- Allow the Windows 11 installation to finish completely.

### Android-x86

**Root Cause:** The wrong installation option was selected during setup.

**Solution:** Repeat the installation and select the correct option based on the VM configuration, including the appropriate virtual disk and filesystem.

## 3. Lessons Learned

- VMware graphics settings can affect VM startup and display behavior.
- Host power and screen-timeout settings can interrupt lengthy OS installations.
- Selecting the correct installation options is essential for successful OS setup.
- Troubleshoot each VM based on its configuration and observed symptoms rather than assuming all black-screen issues share the same cause.
