# Lab 06 - STP Root Bridge Election and Priority Control

## Objective

Demonstrate how to control the Root Bridge election process in Spanning Tree Protocol (STP) by manually modifying bridge priority values.

This lab demonstrates:

- Root Bridge election
- Bridge Priority
- Bridge ID
- STP convergence
- Root Port selection
- Designated Port selection
- Alternate Port selection
- Manual Root Bridge control

---

# Topology

<img width="695" height="510" alt="tapology" src="https://github.com/user-attachments/assets/63c4b8fa-f27a-42ad-a8a7-d2cf3e476fa6" />


Three Cisco 2960 switches were connected in a triangle topology.

---

# Devices Used

| Device | Model |
|----------|----------|
| Switch0 | Cisco 2960 |
| Switch1 | Cisco 2960 |
| Switch2 | Cisco 2960 |

---

# Objective Explanation

By default, STP elects the Root Bridge using the lowest Bridge ID.

Bridge ID consists of:

```text
Priority + MAC Address
```

The switch with the lowest Bridge ID becomes the Root Bridge.

---

# Initial STP State

Before modification, Switch0 was elected as the Root Bridge.

Verification Command:

```bash
show spanning-tree
```

Output:

```text
This bridge is the root
```

---

# Root Bridge Information

Initial Root Bridge:

```text
Switch0
```

Default Priority:

```text
32768
```

---

<img width="756" height="340" alt="Verification Command switch0" src="https://github.com/user-attachments/assets/53b9fabf-a0c4-4dd2-ac85-43726f57a4a5" />

<img width="712" height="362" alt="Verification Command switch1" src="https://github.com/user-attachments/assets/60a7c5bc-c767-40f7-bb30-19ac81ffce54" />

<img width="682" height="352" alt="Verification Command switch2" src="https://github.com/user-attachments/assets/5b6e229b-bf3a-4a08-8016-7d02c52a9cb1" />



# Configuration

## Method 1 - Manual Priority

```bash
enable
configure terminal
spanning-tree vlan 1 priority 4096
```

## Method 2 - Cisco Shortcut

```bash
spanning-tree vlan 1 root primary
```

---

<img width="872" height="277" alt="step2" src="https://github.com/user-attachments/assets/711becec-e337-41c8-a1f9-cb25b0459491" />

# Verification

```bash
show spanning-tree
```

---

# Results

After modifying the priority, Switch1 became the Root Bridge.

Output:

```text
This bridge is the root
```

appeared on Switch1.

---
<img width="807" height="352" alt="verify switch1" src="https://github.com/user-attachments/assets/71e5ec88-e81a-4747-bc49-9a5d2ec8bb2b" />

# STP Topology Changes

Before:

```text
Switch0 = Root Bridge
```

After:

```text
Switch1 = Root Bridge
```

STP recalculated the network topology and updated port roles.

---

<img width="677" height="485" alt="after convergence" src="https://github.com/user-attachments/assets/c0661467-b288-4dc7-9887-b9c6d9d9f714" />
<img width="807" height="352" alt="verify switch1" src="https://github.com/user-attachments/assets/2b457d19-2d3c-4b0c-96d5-4b819f119866" />
<img width="742" height="621" alt="verify switch0" src="https://github.com/user-attachments/assets/e32a04a2-a227-4711-8622-4b79824be853" />
<img width="917" height="462" alt="verify switch2" src="https://github.com/user-attachments/assets/bfb68178-3257-4d8e-a820-a945d150e8c1" />




# Key Concepts Learned

## Bridge ID

```text
Priority + MAC Address
```

## Root Bridge

The central reference point for all STP calculations.

## Priority

Lower values are preferred during Root Bridge election.

## Root Port

Best path toward the Root Bridge.

## Designated Port

Forwarding port selected for a network segment.

## Alternate Port

Backup path that remains blocked until needed.

## Convergence

The process of recalculating the STP topology after a change.

---


# Troubleshooting Performed

Verified:

- Current Root Bridge
- Priority values
- Root Port assignments
- Designated Port assignments
- Alternate Port assignments
- Successful Root Bridge transition
- STP convergence

---

# Lessons Learned

- STP elects a Root Bridge using the lowest Bridge ID.
- Bridge ID consists of Priority and MAC Address.
- Lowering bridge priority influences Root Bridge election.
- Administrators should manually control Root Bridge placement.
- STP automatically recalculates topology after changes.
- Port roles can change after convergence.

---

# Skills Demonstrated

- Cisco IOS CLI
- STP Verification
- Root Bridge Election
- Priority Configuration
- Layer 2 Troubleshooting
- STP Convergence Analysis
- Network Redundancy Design
- Packet Tracer Design

---


