# Lab 03 - VLAN Fundamentals

## Objective
Create multiple VLANs on a Cisco switch and verify communication within the same VLAN while preventing communication between different VLANs.

## VLAN Design
- VLAN 10 = Sales
- VLAN 20 = HR

## IP Addressing

### VLAN 10 - Sales
- PC0: 192.168.10.10/24
- PC1: 192.168.10.20/24

### VLAN 20 - HR
- PC2: 192.168.20.10/24
- PC3: 192.168.20.20/24

## Switch Configuration

```bash
enable
configure terminal

vlan 10
name Sales

vlan 20
name HR

interface fa0/1
switchport mode access
switchport access vlan 10

interface fa0/2
switchport mode access
switchport access vlan 10

interface fa0/3
switchport mode access
switchport access vlan 20

interface fa0/4
switchport mode access
switchport access vlan 20
```

## Verification

```bash
show vlan brief
```

Verified:
- VLAN 10 -> Fa0/1, Fa0/2
- VLAN 20 -> Fa0/3, Fa0/4

## Connectivity Tests

### Successful
- PC0 -> PC1
- PC2 -> PC3

### Failed
- PC0 -> PC2
- PC1 -> PC3

## Key Concepts Learned

- VLANs create separate broadcast domains.
- Access ports belong to a single VLAN.
- Devices in different VLANs cannot communicate without routing.
- VLANs improve security and organization.
- The show vlan brief command verifies VLAN assignments.

## Skills Demonstrated

- VLAN Creation
- Cisco IOS CLI
- Access Port Configuration
- VLAN Verification
- Network Segmentation
- Troubleshooting
