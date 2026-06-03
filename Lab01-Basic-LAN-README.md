# Lab 01 - Basic LAN Connectivity

## Objective

Configure two PCs on the same Local Area Network (LAN) using a Layer 2 switch and verify connectivity through ICMP testing (ping).

---

## Topology

```text
PC0 ---- Switch0 ---- PC1
```

### Devices Used

| Device | Model |
|----------|----------|
| PC0 | Generic PC |
| PC1 | Generic PC |
| Switch0 | Cisco 2960 |

---

## Network Configuration

### PC0

| Setting | Value |
|----------|----------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |

### PC1

| Setting | Value |
|----------|----------|
| IP Address | 192.168.1.20 |
| Subnet Mask | 255.255.255.0 |

---

## Verification

### Connectivity Test

From PC0:

```cmd
ping 192.168.1.20
```

Expected Result:

```text
Reply from 192.168.1.20
Packets: Sent = 4, Received = 4, Lost = 0
```

Result:

✅ Successful communication between PC0 and PC1.

---

## Troubleshooting Exercise

### Issue Introduced

PC1 IP address was changed to:

```text
192.168.2.20
255.255.255.0
```

### Test Performed

From PC0:

```cmd
ping 192.168.2.20
```

### Result

❌ Ping failed.

### Root Cause Analysis

PC0 and PC1 were configured on different networks:

| Device | Network |
|----------|----------|
| PC0 | 192.168.1.0/24 |
| PC1 | 192.168.2.0/24 |

A Layer 2 switch can only forward traffic within the same subnet. Communication between different networks requires a router.

### Resolution

Changed PC1 back to:

```text
192.168.1.20
255.255.255.0
```

Connectivity was restored successfully.

---

## Key Networking Concepts

- IPv4 Addressing
- Subnet Masks
- Layer 2 Switching
- ICMP (Ping)
- Basic Network Troubleshooting

---

## Lessons Learned

- Devices connected to the same switch must be in the same subnet to communicate directly.
- IP addressing mistakes are a common cause of connectivity issues.
- The `ping` command is an effective first step when troubleshooting network connectivity.
- Layer 2 switches forward frames based on MAC addresses and do not perform routing functions.

---

## Skills Demonstrated

- IP Configuration
- Connectivity Testing
- Basic Troubleshooting
- Network Documentation
- Packet Tracer Topology Creation
