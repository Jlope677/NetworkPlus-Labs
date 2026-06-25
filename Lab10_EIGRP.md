# Lab 10 - EIGRP Configuration and Verification

## Objective

Configure EIGRP on a three-router topology, verify neighbor
relationships, examine the topology table, and confirm dynamically
learned routes.

### Skills Demonstrated

-   EIGRP configuration
-   Dynamic routing
-   Neighbor verification
-   Topology table analysis
-   Routing table analysis
-   Cisco IOS CLI
-   Troubleshooting

------------------------------------------------------------------------

## Topology

```text
          R2
         /  \
        /    \
      R1------R3
```

This lab uses the same three-router redundant topology introduced in **Lab 09 – OSPF Failover**.

### Networks

- R1 ↔ R2 : 10.0.12.0/30
- R1 ↔ R3 : 10.0.13.0/30
- R2 ↔ R3 : 10.0.23.0/30

To recreate the topology, interface IP addressing, and physical connections, refer to **Lab 09**.

Only the routing protocol changes in this lab (OSPF → EIGRP).

------------------------------------------------------------------------

## Step 1 - Remove OSPF

``` bash
configure terminal
no router ospf 1
```

Reason: - Prevent routing protocol conflicts. - Ensure EIGRP is the only
protocol advertising routes.

Verify:

``` bash
show ip protocols
```

------------------------------------------------------------------------

## Step 2 - Configure EIGRP

### R1

``` bash
router eigrp 100
no auto-summary
network 10.0.12.0 0.0.0.3
network 10.0.13.0 0.0.0.3
```

### R2

``` bash
router eigrp 100
no auto-summary
network 10.0.12.0 0.0.0.3
network 10.0.23.0 0.0.0.3
```

### R3

``` bash
router eigrp 100
no auto-summary
network 10.0.13.0 0.0.0.3
network 10.0.23.0 0.0.0.3
```

Reason: - AS 100 must match on every router. - The network statements
enable EIGRP on matching interfaces. - `no auto-summary` prevents
automatic classful summarization.

------------------------------------------------------------------------

## Verification

### show ip eigrp neighbors

Purpose: - Confirms neighboring routers formed adjacencies. - If no
neighbors exist, routes cannot be exchanged.

### show ip eigrp topology

Purpose: - Displays all learned routes. - Shows successor paths,
additional equal-cost paths, and feasible distance.

### show ip route

Purpose: - Displays the routes actually used for forwarding. - Routes
learned by EIGRP are marked with **D**. - Multiple equal-cost routes
indicate load balancing.

------------------------------------------------------------------------

## Troubleshooting Performed

-   Removed OSPF.
-   Verified neighbor adjacency.
-   Verified topology entries.
-   Verified routing table entries.
-   Confirmed equal-cost paths.

------------------------------------------------------------------------

## What I Learned

-   EIGRP is an advanced distance-vector routing protocol.
-   It uses the DUAL algorithm.
-   Neighbor relationships are required before routes are exchanged.
-   The topology table contains all known paths.
-   The routing table contains only the best forwarding paths.
-   Equal-cost paths can both be installed for load balancing.
-   The AS number must match on all routers.

------------------------------------------------------------------------


