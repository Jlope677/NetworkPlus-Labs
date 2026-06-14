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

## PC1

| Setting | Value |
|----------|----------|
| IP Address | 192.168.20.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.20.1 |

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

## Configure Trunk Port

```bash
interface fa0/24
switchport mode trunk
```

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

---

# Router Configuration

## Enable Physical Interface

```bash
enable
configure terminal

interface g0/0/0
no shutdown
```

## Configure VLAN 10 Gateway

```bash
interface g0/0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
```

## Configure VLAN 20 Gateway

```bash
interface g0/0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
```

---

# Verification

## Test 1 - VLAN 10 Gateway

From PC0:

```cmd
ping 192.168.10.1
```

Result: Successful

## Test 2 - VLAN 20 Gateway

From PC1:

```cmd
ping 192.168.20.1
```

Result: Successful

## Test 3 - Inter-VLAN Communication

From PC0:

```cmd
ping 192.168.20.10
```

Result: Successful

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

## VLAN

A logical network created on a switch.

## Access Port

A switch port assigned to a single VLAN.

## Trunk Port

A switch port that carries multiple VLANs.

## 802.1Q

The VLAN tagging protocol used on trunk links.

## Default Gateway

The device used to reach networks outside the local subnet.

## Router Subinterface

A logical interface configured on a router physical interface.

Examples:

```text
G0/0/0.10
G0/0/0.20
```

## Inter-VLAN Routing

The process of routing traffic between VLANs.

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

# Screenshots

1. Topology Diagram
2. VLAN Creation
3. Access Port Configuration
4. Trunk Configuration
5. Router Interface Activation
6. Router Subinterface Configuration
7. show vlan brief
8. show interfaces trunk
9. show ip interface
10. Gateway Ping Tests
11. Successful Inter-VLAN Ping
