# Kali No Internet Access due to VMware NAT Gateway Conflict

## Issue Overview

After configuring a static IP address (`10.0.0.2`) on the Kali Linux VM, the VM could reach its configured gateway (`10.0.0.1`), but internet connectivity failed.

### Observed Symptoms

- Kali Linux was configured with a static IP address.
- The gateway responded to local connectivity tests.
- External IP connectivity to `8.8.8.8` failed.
- DNS-based connectivity to `google.com` also failed.

### Network Configuration

| Setting | Value |
|---|---|
| Network Adapter | VMware NAT |
| Interface | `eth0` |
| Kali IP Address | `10.0.0.2/24` |
| Gateway | `10.0.0.1` |
| Primary DNS | `8.8.8.8` |
| Secondary DNS | `1.1.1.1` |

## Diagnosis

### Inspect the Gateway's ARP Responses

The `arping` utility was used to check which MAC addresses responded to requests for the gateway IP.

```bash
sudo arping -c 4 -I eth0 -S 10.0.0.2 10.0.0.1
```

### Observed Output

```bash
ARPING 10.0.0.1
60 bytes from 00:50:56:c0:00:08 (10.0.0.1): index=0 time=419.358 usec
60 bytes from 00:50:56:f7:63:22 (10.0.0.1): index=1 time=853.715 usec
```

### Findings

Two different MAC addresses responded to ARP requests for `10.0.0.1`.

| MAC Address | Observation |
|---|---|
| `00:50:56:c0:00:08` | Responded for `10.0.0.1` |
| `00:50:56:f7:63:22` | Responded for `10.0.0.1` |

This indicates conflicting ARP responses for the gateway IP. Further verification is required to identify the legitimate gateway and determine why both addresses respond.

## Solution

### Option 1: Immediate Workaround

A static neighbor-table entry was added to associate the gateway IP with the selected MAC address.

```bash
sudo ip neigh replace 10.0.0.1 lladdr 00:50:56:f7:63:22 dev eth0
```

This overrides Kali's local neighbor-table mapping for the gateway.

Verify the entry:

```bash
ip neigh show 10.0.0.1
```

Expected mapping:

```bash
10.0.0.1 dev eth0 lladdr 00:50:56:f7:63:22 PERMANENT
```

The `PERMANENT` state indicates a manually configured neighbor entry.

> **Note:** Confirm that `00:50:56:f7:63:22` belongs to the actual VMware NAT gateway before relying on this mapping.

### Option 2: Persistent Workaround

To reapply the gateway mapping whenever NetworkManager brings `eth0` up, create a dispatcher script.

```bash
sudo nano /etc/NetworkManager/dispatcher.d/99-lock-gateway-mac
```

Add the following contents:

```bash
#!/bin/bash

IFACE="$1"
ACTION="$2"

if [ "$IFACE" = "eth0" ] && [ "$ACTION" = "up" ]; then
    /usr/sbin/ip neigh replace 10.0.0.1 lladdr 00:50:56:f7:63:22 dev eth0
fi
```

Save the file and exit the editor.

```bash
sudo chown root:root /etc/NetworkManager/dispatcher.d/99-lock-gateway-mac
sudo chmod 700 /etc/NetworkManager/dispatcher.d/99-lock-gateway-mac
```

The script reapplies the static neighbor entry when NetworkManager reports that `eth0` is up.

### Verify the Workaround

**Check the Gateway Mapping**

```bash
ip neigh show 10.0.0.1
```

Confirm that the entry uses the selected MAC address.

**Test Gateway Connectivity**

```bash
ping -c 4 10.0.0.1
```

**Test Internet Connectivity**

```bash
ping -c 4 8.8.8.8
```

**Test DNS Resolution**

```bash
ping -c 4 google.com
```

**Verify After Reboot**

Restart Kali Linux and run:

```bash
ip neigh show 10.0.0.1
ping -c 4 8.8.8.8
ping -c 4 google.com
```

Confirm that the mapping persists and internet connectivity works.

## Lessons Learned

- Successful gateway connectivity does not guarantee successful internet routing.
- ARP inspection helps identify conflicting MAC address responses.
- Static neighbor entries can help isolate Layer 2 routing problems.
- Persistent workarounds should be verified after network reconnection and reboot.
- Correcting the underlying VMware network configuration is preferable to permanently overriding gateway resolution.