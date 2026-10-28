# Android-x86 Internet Failure Due to Routing Misconfiguration

## Issue Overview

After configuring a static IP address on the Android-x86 VM, internet connectivity failed even though the IP configuration remained unchanged.

**Network Configuration**

| Setting | Value |
|---|---|
| Static IP | `10.0.0.9` |
| Gateway | `10.0.0.1` |
| Interface | `wlan0` |
| DNS Server | `8.8.8.8` |

## Diagnosis

The issue was traced to the routing configuration. The fix prioritizes the main routing table, reapplies the default route through the configured gateway, and sets the DNS resolver.

## Solution

Run these commands in Termux with root access:

```bash
su
ip rule add from all lookup main pref 0
ip route replace default via 10.0.0.1 dev wlan0
ndc resolver setnetdns 100 localdomain 8.8.8.8
```

**Verify Connectivity**

Check gateway connectivity:

```bash
ping -c 3 10.0.0.1
```

Check external IP connectivity:

```bash
ping -c 3 8.8.8.8
```

Check HTTPS connectivity:

```bash
curl -I --connect-timeout 5 https://1.1.1.1
```

## Lessons Learned

- A correctly configured static IP does not guarantee internet connectivity.
- Routing rules determine how packets are directed through routing tables.
- The main routing table and default gateway must be configured correctly for outbound traffic.
- DNS configuration is necessary for domain-name resolution.
- Test connectivity at multiple layers: gateway, external IP, and HTTPS.


> Note: These commands apply to the Android-x86 environment described here. The interface name, network ID, and available utilities may differ between builds. The routing-rule command may fail if an equivalent rule already exists.
