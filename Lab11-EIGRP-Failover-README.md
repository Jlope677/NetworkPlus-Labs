# Lab 11 - EIGRP Failover and Convergence

## Objective

Demonstrate EIGRP failover by simulating a link failure and verifying that EIGRP automatically removes the failed path and continues routing through an alternate path.

This lab demonstrates:

- EIGRP neighbor loss detection
- EIGRP convergence
- Dynamic route recalculation
- Alternate path selection
- Routing table verification
- Network redundancy
- Failover troubleshooting

---

## Lab Dependency

This lab builds on previous routing labs:

- **Lab 09 - OSPF Failover**: Created the three-router redundant topology.
- **Lab 10 - EIGRP Configuration and Verification**: Removed OSPF and configured EIGRP.

The following items were reused from Lab 09 and Lab 10:

- Three-router triangle topology
- Router interface IP addressing
- Physical connections
- EIGRP AS 100 configuration

This lab focuses only on the failover test and verification.

---

## Topology

```text
        R2
       /  \
      /    \
     /      \
   R1 ------ R3
```

After the simulated failure, the R1-to-R2 link is shut down:

```text
        R2
         \
          \
           R3
          /
         /
       R1
```

Traffic must use the remaining path:

```text
R1 -> R3 -> R2
```

---

## Pre-Failure Verification

Before shutting down any interface, the following commands were used:

```bash
show ip eigrp neighbors
show ip eigrp topology
show ip route
```

### Reasoning

Before testing failover, it is important to confirm that:

- EIGRP neighbors are established.
- The topology table contains known routes.
- The routing table contains EIGRP-learned routes.
- The network has multiple paths available.

---

## Pre-Failure Results

### R1

R1 had two EIGRP neighbors:

- 10.0.12.2 through G0/0/0
- 10.0.13.2 through G0/0/1

This confirmed that R1 had neighbor relationships with both R2 and R3.

### R2

R2 had two EIGRP neighbors:

- 10.0.12.1 through G0/0/0
- 10.0.23.2 through G0/0/1

This confirmed that R2 had neighbor relationships with both R1 and R3.

### R3

R3 had two EIGRP neighbors:

- 10.0.13.1 through G0/0/1
- 10.0.23.1 through G0/0/0

This confirmed that R3 had neighbor relationships with both R1 and R2.

---

## EIGRP Topology Table Review

Command used:

```bash
show ip eigrp topology
```

The topology table showed the routes EIGRP knew about, including successor paths and available alternate paths.

Important terms:

| Term | Meaning |
|------|---------|
| Successor | Best route currently selected by EIGRP |
| Feasible Successor | Backup route that can be used if the successor fails |
| FD | Feasible Distance, or the total metric to reach a network |
| P | Passive, meaning the route is stable and not currently recalculating |

---

## Routing Table Review

Command used:

```bash
show ip route
```

EIGRP-learned routes appeared with the letter:

```text
D
```

The **D** route code means the route was learned through EIGRP using the DUAL algorithm.

---

## Simulated Link Failure

To simulate a failure, the R1-to-R2 link was shut down on R1.

```bash
configure terminal
interface g0/0/0
shutdown
```

### Reasoning

This disabled the direct link between R1 and R2.

The goal was to verify that EIGRP could detect the neighbor loss and continue routing using the remaining R1-to-R3-to-R2 path.

---

## Failure Event Observed

After shutting down the interface, the router displayed a neighbor change message:

```text
DUAL-5-NBRCHANGE: IP-EIGRP 100: Neighbor 10.0.12.2 is down
```

This confirmed that EIGRP detected the failed neighbor relationship.

---

## Post-Failure Verification

The following commands were run again:

```bash
show ip eigrp neighbors
show ip eigrp topology
show ip route
```

---

## Post-Failure Results

### R1

After the failure, R1 lost the neighbor relationship with R2:

- Removed neighbor: 10.0.12.2
- Remaining neighbor: 10.0.13.2

R1 continued routing through R3.

### R2

After the failure, R2 lost the neighbor relationship with R1:

- Removed neighbor: 10.0.12.1
- Remaining neighbor: 10.0.23.2

R2 continued routing through R3.

### R3

R3 remained connected to both routers:

- Neighbor to R1 remained active.
- Neighbor to R2 remained active.

R3 became the transit path between R1 and R2.

---

## What Happened During Failover

1. The R1-to-R2 interface was shut down.
2. EIGRP detected that neighbor 10.0.12.2 was no longer reachable.
3. DUAL removed the failed path.
4. EIGRP selected the remaining available path through R3.
5. The routing table updated automatically.
6. Traffic could continue using the path R1 -> R3 -> R2.

---

## Before and After Comparison

| Verification | Before Failure | After Failure |
|-------------|----------------|---------------|
| R1 Neighbors | R2 and R3 | R3 only |
| R2 Neighbors | R1 and R3 | R3 only |
| R3 Neighbors | R1 and R2 | R1 and R2 |
| Routing Protocol | EIGRP AS 100 | EIGRP AS 100 |
| Path Availability | Multiple paths | Remaining alternate path |
| Manual Route Changes Needed | No | No |

---

## Why This Matters

This lab demonstrates one of the main benefits of dynamic routing.

With static routing, a failed link may require manual route changes.

With EIGRP, the routing protocol automatically detects the failure, removes the invalid path, and installs the next best path.

This is important in enterprise networks because it supports:

- Redundancy
- High availability
- Faster recovery
- Less manual intervention
- Better network resilience

---

## Key Concepts Learned

### EIGRP

EIGRP is an advanced distance-vector routing protocol that uses the DUAL algorithm.

### DUAL

DUAL stands for Diffusing Update Algorithm. It allows EIGRP to calculate loop-free routes and quickly recover from failures.

### Neighbor Table

Stores directly connected EIGRP neighbors.

Command:

```bash
show ip eigrp neighbors
```

### Topology Table

Stores EIGRP-learned routes, successor routes, and possible backup paths.

Command:

```bash
show ip eigrp topology
```

### Routing Table

Stores the routes actually used to forward packets.

Command:

```bash
show ip route
```

### Convergence

Convergence is the process of updating routing information after a topology change.

### Failover

Failover occurs when traffic automatically uses a backup path after the primary path fails.

---

## Troubleshooting Performed

Verified:

- EIGRP neighbor relationships before the failure
- EIGRP topology table before the failure
- Routing table before the failure
- Link shutdown on R1
- Neighbor loss message
- EIGRP neighbor relationships after the failure
- Routing table after convergence
- Remaining alternate path through R3

---

## Lessons Learned

- EIGRP automatically detects failed neighbor relationships.
- EIGRP routes appear in the routing table with the letter D.
- The topology table shows routes EIGRP knows about.
- The routing table shows routes actually being used.
- Dynamic routing allows the network to recover without manually adding new routes.
- R3 acted as the alternate path after the R1-to-R2 link failed.
- EIGRP failover demonstrates why dynamic routing is valuable in redundant network designs.

---

## Skills Demonstrated

- Cisco IOS CLI
- EIGRP Verification
- EIGRP Failover Testing
- Neighbor Table Analysis
- Topology Table Analysis
- Routing Table Analysis
- Link Failure Simulation
- Dynamic Routing Troubleshooting
- Network Redundancy Testing
- Packet Tracer Lab Documentation

---

## Screenshot Checklist

- Original EIGRP verification on R1
- Original EIGRP verification on R2
- Original EIGRP verification on R3
- Simulated link failure on R1
- Updated topology after link failure
- Post-failure verification on R1
- Post-failure verification on R2
- Post-failure verification on R3
