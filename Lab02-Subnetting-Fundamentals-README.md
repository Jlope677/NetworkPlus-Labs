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

<img width="827" height="520" alt="lab2-1" src="https://github.com/user-attachments/assets/efc7b8e2-4a61-4b58-9fae-a1734b2a6a76" />

## Part 1 - Same Subnet Communication

### PC0
- IP Address: 192.168.1.10
- Subnet Mask: 255.255.255.0

<img width="895" height="880" alt="pc0" src="https://github.com/user-attachments/assets/6d446bee-5f4c-4d78-8e5f-7a0c8bf6456d" />

### PC1
- IP Address: 192.168.1.20
- Subnet Mask: 255.255.255.0
<img width="867" height="817" alt="pc1" src="https://github.com/user-attachments/assets/f19f56b0-7349-4798-bb32-06051a72f449" />

### PC2
- IP Address: 192.168.1.30
- Subnet Mask: 255.255.255.0

<img width="880" height="891" alt="pc2" src="https://github.com/user-attachments/assets/350a6660-1553-44dc-9ffa-b12ef929defb" />

## Verification

From PC0:

```cmd
ping 192.168.1.20
ping 192.168.1.30
```

Result: Successful communication.

<img width="555" height="557" alt="ping1" src="https://github.com/user-attachments/assets/97c37156-e99c-49f0-8b71-dbe9b092fce2" />

## Part 2 - Different Subnet Communication

PC2 changed to:

- IP Address: 192.168.2.30
- Subnet Mask: 255.255.255.0

<img width="882" height="892" alt="pc2-1" src="https://github.com/user-attachments/assets/2987105a-8d3d-42ab-b023-aa925ad7b327" />

From PC0:

```cmd
ping 192.168.2.30
```

Result: Request Timed Out.

<img width="545" height="415" alt="ping2" src="https://github.com/user-attachments/assets/b6a724e8-b627-45b6-857a-4928170d765a" />

### Root Cause

PC0 was on 192.168.1.0/24 and PC2 was on 192.168.2.0/24.

A Layer 2 switch cannot route between different networks.

A router is required.

## Part 3 - Subnet Mask Mismatch

PC0:
- 192.168.1.10 / 255.255.255.0

PC1:
- 192.168.1.130 / 255.255.255.128

<img width="877" height="892" alt="pc1-3" src="https://github.com/user-attachments/assets/8222b98c-c7cd-49ca-83b0-7e0ade31b544" />

From PC0:

```cmd
ping 192.168.1.130
```

Result: Request Timed Out.

<img width="632" height="202" alt="ping4" src="https://github.com/user-attachments/assets/82c38e53-481d-4424-a829-3434c1b933f1" />

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
