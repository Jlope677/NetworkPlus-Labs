# Lab 03 - VLAN Fundamentals

## Objective
Create multiple VLANs on a Cisco switch and verify communication within the same VLAN while preventing communication between different VLANs.

<img width="760" height="431" alt="lab3-1" src="https://github.com/user-attachments/assets/7d32fbac-712c-45f3-84ce-874911bd44c5" />


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

<img width="1917" height="962" alt="pc0" src="https://github.com/user-attachments/assets/2e0b59e0-d5ad-49e0-bd0d-c02249429f37" />
<img width="1917" height="715" alt="pc1" src="https://github.com/user-attachments/assets/0d9e4644-aba3-443d-b02a-0ffb131911b8" />
<img width="1917" height="621" alt="pc2" src="https://github.com/user-attachments/assets/ca750741-96b8-45e4-924f-bb7a567bd425" />
<img width="1917" height="622" alt="pc3" src="https://github.com/user-attachments/assets/98b0c0c7-1512-4e29-90ea-59d546a5c325" />




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

<img width="532" height="155" alt="step 1" src="https://github.com/user-attachments/assets/abf3cd10-5c6d-4374-9121-a4833ef83c9d" />
<img width="602" height="281" alt="step 2" src="https://github.com/user-attachments/assets/481339e1-42fa-430c-8d72-0e0056276c5a" />


## Verification

```bash
show vlan brief
```

Verified:
- VLAN 10 -> Fa0/1, Fa0/2
- VLAN 20 -> Fa0/3, Fa0/4

<img width="770" height="282" alt="step 3" src="https://github.com/user-attachments/assets/8b211513-fa5a-4a96-8d42-529c3f89dbaf" />

## Connectivity Tests

### Successful
- PC0 -> PC1
- PC2 -> PC3

### Failed
- PC0 -> PC2
- PC1 -> PC3

<img width="571" height="261" alt="test1" src="https://github.com/user-attachments/assets/e41d52c6-acca-4585-817f-28aafb53a4e5" />
<img width="592" height="322" alt="test2" src="https://github.com/user-attachments/assets/7d8b51b6-5b9f-4788-8691-6033c606b357" />
<img width="547" height="252" alt="test3" src="https://github.com/user-attachments/assets/f34d8607-9ebb-47d6-8518-f53a6165706b" />
<img width="702" height="287" alt="test4" src="https://github.com/user-attachments/assets/c0039828-2575-4ca3-b7e5-7512e1019a84" />


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
