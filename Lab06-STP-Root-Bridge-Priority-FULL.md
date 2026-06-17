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

```text
       Switch0
       /     \
      /       \
     /         \
Switch1-------Switch2
```

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

# Questions Answered

1. Which switch was Root before?
   - Switch0

2. Which switch became Root after?
   - Switch1

3. Which port became blocked?
   - Alternate Port on the non-root switch

4. Did the blocked port change?
   - Yes, STP recalculated port roles after the Root Bridge changed.

5. Why did STP recalculate?
   - Because the Bridge ID changed when the priority was lowered.

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

# Screenshots

1. Initial Topology
2. Initial Root Bridge Election
3. Switch0 STP Output
4. Switch1 STP Output
5. Switch2 STP Output
6. Priority Configuration
7. New Root Bridge Election
8. Updated STP Output
9. Convergence Verification
