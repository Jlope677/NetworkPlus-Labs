# Lab 04 - Inter-VLAN Routing (Router-on-a-Stick)

## Objective

Configure inter-VLAN routing using a Cisco router and a trunk connection to allow communication between VLAN 10 and VLAN 20.

This lab demonstrates:

- VLAN creation
- Access port configuration
- Trunk port configuration
- Router subinterfaces
- 802.1Q tagging
- Default gateways
- Inter-VLAN routing

---

# Topology

```text
PC0
192.168.10.10
     |
     |
 Fa0/1
     |
  Switch0
     |
 Fa0/24
     |
  Trunk
     |
 G0/0
 Router0
     |
     |
PC1
192.168.20.10
```

<img width="452" height="437" alt="lab4" src="https://github.com/user-attachments/assets/9ec7c3c7-167d-4ed9-82ed-27e6f09917fb" />

---

# Devices Used

| Device | Model |
|----------|----------|
| Router0 | Cisco ISR4331 |
| Switch0 | Cisco 2960 |
| PC0 | Generic PC |
| PC1 | Generic PC |

---

# Network Design

## VLAN 10 - Sales

| Setting | Value |
|----------|----------|
| Network | 192.168.10.0/24 |
| Gateway | 192.168.10.1 |

## VLAN 20 - HR

| Setting | Value |
|----------|----------|
| Network | 192.168.20.0/24 |
| Gateway | 192.168.20.1 |

---

# PC Configuration

## PC0

| Setting | Value |
|----------|----------|
| IP Address | 192.168.10.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.10.1 |

<img width="1387" height="642" alt="pc0" src="https://github.com/user-attachments/assets/a7f00fb3-4e1b-4ccc-9b57-b2efeeeb475e" />


## PC1

| Setting | Value |
|----------|----------|
| IP Address | 192.168.20.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.20.1 |


<img width="1452" height="652" alt="pc1" src="https://github.com/user-attachments/assets/3c70b52c-e31f-456c-963f-23708578b8d6" />

---

# Switch Configuration

## Create VLANs

```bash
enable
configure terminal

vlan 10
name Sales

vlan 20
name HR
```

<img width="570" height="116" alt="create vlans" src="https://github.com/user-attachments/assets/565106ba-3e25-4a10-978c-1d2383c16eda" />

## Configure Access Ports

### VLAN 10

```bash
interface fa0/1
switchport mode access
switchport access vlan 10
```

### VLAN 20

```bash
interface fa0/2
switchport mode access
switchport access vlan 20
```

<img width="622" height="172" alt="assign ports" src="https://github.com/user-attachments/assets/a51f98fd-7f3c-4034-9c13-a4c6edaa548b" />

## Configure Trunk Port

```bash
interface fa0/24
switchport mode trunk
```

<img width="380" height="47" alt="configure trunk" src="https://github.com/user-attachments/assets/8b7d51ae-1f1d-4df9-84b9-5f8422262903" />

---

# Verification Commands

## Verify VLANs

```bash
show vlan brief
```

Verified:

```text
VLAN 10 Sales -> Fa0/1
VLAN 20 HR    -> Fa0/2
```

## Verify Trunk

```bash
show interfaces trunk
```

Verified:

```text
Fa0/24
Mode: Trunk
Encapsulation: 802.1Q
Allowed VLANs: 1-1005
```

<img width="771" height="464" alt="verify" src="https://github.com/user-attachments/assets/811d9fb3-e194-45bd-a373-76295e1fe9a3" />

---

# Router Configuration

## Enable Physical Interface

```bash
enable
configure terminal

interface g0/0/0
no shutdown
```
<img width="862" height="172" alt="activate interface" src="https://github.com/user-attachments/assets/3e5f5b1c-d439-4001-be21-aac2ed356389" />

## Configure VLAN 10 Gateway

```bash
interface g0/0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
```
<img width="872" height="196" alt="vlan 10 gateway" src="https://github.com/user-attachments/assets/bae2b92a-9621-4326-b6fe-0002e5c6ef8b" />

## Configure VLAN 20 Gateway

```bash
interface g0/0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
```

<img width="827" height="141" alt="vlan 20 gateway" src="https://github.com/user-attachments/assets/7fcb9166-b1e2-4120-85f7-345f4a83ef5b" />

---

# Verification

## Test 1 - VLAN 10 Gateway

From PC0:

```cmd
ping 192.168.10.1
```

Result: Successful

<img width="777" height="452" alt="ping from pc 0" src="https://github.com/user-attachments/assets/987f7c3d-998c-46bb-acc5-1ddd8419edba" />

## Test 2 - VLAN 20 Gateway

From PC1:

```cmd
ping 192.168.20.1
```

Result: Successful

<img width="892" height="510" alt="ping from pc 1" src="https://github.com/user-attachments/assets/777207c5-c019-4d94-982e-8d6cbf07559e" />

## Test 3 - Inter-VLAN Communication

From PC0:

```cmd
ping 192.168.20.10
```

Result: Successful

<img width="577" height="305" alt="inter vlan test" src="https://github.com/user-attachments/assets/a5e0cc02-1bf0-452c-911c-a47b664dd970" />

---

# How Router-on-a-Stick Works

1. PC0 determines the destination is on another network.
2. PC0 sends traffic to its default gateway (192.168.10.1).
3. The switch forwards the traffic over the trunk link.
4. The router receives the VLAN-tagged frame.
5. The router routes the packet to VLAN 20.
6. The packet is delivered to PC1.

---

# Key Concepts Learned

* Key Concepts Learned
* VLANs create logical network separation.
* Access ports carry traffic for a single VLAN.
* Trunk ports carry traffic for multiple VLANs.
* 802.1Q provides VLAN tagging.
* Default gateways enable communication outside the local subnet.
* Router subinterfaces allow multiple VLANs to use one physical interface.
* Inter-VLAN routing enables communication between VLANs.

---

# Troubleshooting Performed

Verified:

- VLAN assignments
- Trunk status
- Default gateways
- Router subinterfaces
- Physical interface status
- Successful gateway connectivity
- Successful inter-VLAN communication

---

# Lessons Learned

- Devices in different VLANs cannot communicate without routing.
- Router-on-a-Stick uses one physical router interface and multiple subinterfaces.
- Trunk ports carry VLAN traffic using 802.1Q tagging.
- Default gateways are required for communication outside the local subnet.
- Inter-VLAN routing allows separate VLANs to communicate securely.

---

# Skills Demonstrated

- Cisco IOS CLI
- VLAN Configuration
- Access Port Configuration
- Trunk Configuration
- Router Configuration
- 802.1Q Tagging
- Default Gateway Configuration
- Inter-VLAN Routing
- Network Troubleshooting
- Packet Tracer Design

---
