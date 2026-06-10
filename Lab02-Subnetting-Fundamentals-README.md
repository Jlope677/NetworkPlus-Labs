# Lab 02 - Subnetting Fundamentals

## Objective

Configure multiple hosts on a switched network and demonstrate how IP addressing and subnet masks affect communication.

This lab explores:

- Same subnet communication
- Different subnet communication
- Subnet mask mismatches
- Basic network troubleshooting
- Layer 2 switching limitations

## Topology

```text
           PC1
            |
            |
PC0 ---- Switch0 ---- PC2
```

### Devices Used

| Device | Model |
|----------|----------|
| PC0 | Generic PC |
| PC1 | Generic PC |
| PC2 | Generic PC |
| Switch0 | Cisco 2960 |

## Part 1 - Same Subnet Communication

### PC0
- IP Address: 192.168.1.10
- Subnet Mask: 255.255.255.0

### PC1
- IP Address: 192.168.1.20
- Subnet Mask: 255.255.255.0

### PC2
- IP Address: 192.168.1.30
- Subnet Mask: 255.255.255.0

## Verification

From PC0:

```cmd
ping 192.168.1.20
ping 192.168.1.30
```

Result: Successful communication.

## Part 2 - Different Subnet Communication

PC2 changed to:

- IP Address: 192.168.2.30
- Subnet Mask: 255.255.255.0

From PC0:

```cmd
ping 192.168.2.30
```

Result: Request Timed Out.

### Root Cause

PC0 was on 192.168.1.0/24 and PC2 was on 192.168.2.0/24.

A Layer 2 switch cannot route between different networks.

A router is required.

## Part 3 - Subnet Mask Mismatch

PC0:
- 192.168.1.10 / 255.255.255.0

PC1:
- 192.168.1.130 / 255.255.255.128

From PC0:

```cmd
ping 192.168.1.130
```

Result: Request Timed Out.

### Root Cause

PC1 belonged to the 192.168.1.128/25 subnet while PC0 was using 192.168.1.0/24.

The subnet mask mismatch caused communication failure.

## Lessons Learned

- Devices must be in the same subnet to communicate directly through a switch.
- Subnet masks are just as important as IP addresses.
- Routers are required to communicate between networks.
- Subnet mask mismatches can cause connectivity problems.
- Understanding network, host, and broadcast addresses is critical for troubleshooting.

## Skills Demonstrated

- IPv4 Address Configuration
- Connectivity Testing
- Ping Troubleshooting
- Subnet Analysis
- Network Documentation
- Root Cause Analysis
- Packet Tracer Topology Creation
