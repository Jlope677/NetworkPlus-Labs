# Lab 09 - OSPF Failover and Convergence

## Objective

Configure a resilient three-router OSPF topology and demonstrate both
**Equal-Cost Multi-Path (ECMP)** and **automatic failover** after a link
failure.

This lab demonstrates:

-   OSPF neighbor formation
-   OSPF route learning
-   Equal-Cost Multi-Path (ECMP)
-   OSPF convergence
-   Automatic failover
-   Routing table analysis
-   Network redundancy

------------------------------------------------------------------------

## Topology

``` text
        R2
       /  \
      /    \
     /      \
   R1 ------ R3
```

R1 connects to both R2 and R3, creating two equal paths to the R2-R3
network.

------------------------------------------------------------------------

## Physical Connections

  Device   Interface   Connected To
  -------- ----------- --------------
  R1       G0/0/0      R2 G0/0/0
  R1       G0/0/1      R3 G0/0/1
  R2       G0/0/1      R3 G0/0/0

------------------------------------------------------------------------

## Step 1 -- Configure Interfaces

Configure all router interfaces with /30 addressing and enable them
using `no shutdown`.

------------------------------------------------------------------------

## Step 2 -- Configure OSPF

Enable OSPF process 1 and advertise each point-to-point network.

### R1

``` bash
router ospf 1
network 10.0.12.0 0.0.0.3 area 0
network 10.0.13.0 0.0.0.3 area 0
```

### R2

``` bash
router ospf 1
network 10.0.12.0 0.0.0.3 area 0
network 10.0.23.0 0.0.0.3 area 0
```

### R3

``` bash
router ospf 1
network 10.0.13.0 0.0.0.3 area 0
network 10.0.23.0 0.0.0.3 area 0
```

### Why the wildcard mask?

`0.0.0.3` matches a `/30` network (255.255.255.252), which is commonly
used on point-to-point router links.

------------------------------------------------------------------------

## Step 3 -- Verify Neighbors

``` bash
show ip ospf neighbor
```

All routers reached the **FULL** state, confirming successful adjacency
formation.

------------------------------------------------------------------------

## Step 4 -- Verify Routing Before Failure

``` bash
show ip route
```

R1 displayed:

``` text
O 10.0.23.0/30 [110/2] via 10.0.13.2
               [110/2] via 10.0.12.2
```

### Why are there two routes?

This is **Equal-Cost Multi-Path (ECMP)**.

Both paths have the same OSPF metric (cost 2), so OSPF installs **both
next hops** into the routing table.

Traffic can be load-balanced across either path.

This is expected behavior---not an error.

------------------------------------------------------------------------

## Step 5 -- Simulate Link Failure

On R1:

``` bash
interface g0/0/0
shutdown
```

This disconnects the R1--R2 link.

OSPF immediately detects the neighbor loss.

------------------------------------------------------------------------

## Step 6 -- Verify Failover

Run:

``` bash
show ip route
```

Now the routing table shows:

``` text
O 10.0.23.0/30 via 10.0.13.2
```

The route through **10.0.12.2** has been removed.

OSPF recalculated the topology and kept the remaining path through R3.

No manual configuration was required.

------------------------------------------------------------------------

## Reasoning

Before failure:

-   Two equal-cost paths existed.
-   OSPF installed both (ECMP).

After failure:

-   The failed next hop was removed.
-   The remaining path stayed active.
-   Connectivity continued automatically.

This demonstrates **OSPF convergence** and **network redundancy**.

------------------------------------------------------------------------

## Troubleshooting Performed

-   Verified interface status (`show ip interface brief`)
-   Verified neighbors (`show ip ospf neighbor`)
-   Verified routing table (`show ip route`)
-   Shut down one interface
-   Verified neighbor loss
-   Verified routing table updated automatically
-   Confirmed alternate path remained

------------------------------------------------------------------------

## Lessons Learned

-   OSPF is a **link-state** routing protocol.
-   OSPF supports **Equal-Cost Multi-Path (ECMP)**.
-   Multiple next hops do not mean duplicate routes---they indicate load
    balancing.
-   OSPF automatically recalculates routes after a topology change.
-   Dynamic routing provides redundancy without manual intervention.
-   `show ip route` and `show ip ospf neighbor` are essential
    troubleshooting commands.

------------------------------------------------------------------------

## Skills Demonstrated

-   Cisco IOS CLI
-   OSPF Configuration
-   OSPF Neighbor Verification
-   ECMP Analysis
-   OSPF Convergence
-   Link Failure Testing
-   Routing Table Analysis
-   Enterprise Troubleshooting
-   Packet Tracer
