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

<img width="767" height="416" alt="topology" src="https://github.com/user-attachments/assets/0718f013-79f3-4b24-9c83-7eab98a927cc" />

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

<img width="896" height="506" alt="R1 configuration" src="https://github.com/user-attachments/assets/85029dc6-5ba3-452c-bfe6-fda183b174f2" />

<img width="857" height="567" alt="R2 configuration" src="https://github.com/user-attachments/assets/dfd188f7-ab0b-4404-8b9c-58d60b2578a0" />

<img width="917" height="577" alt="R3 configuration" src="https://github.com/user-attachments/assets/cbcc5c84-c1fe-44df-bc4a-0810b0665c1b" />



------------------------------------------------------------------------

## Step 2 -- Configure OSPF

Enable OSPF process 1 and advertise each point-to-point network.

### R1

``` bash
router ospf 1
network 10.0.12.0 0.0.0.3 area 0
network 10.0.13.0 0.0.0.3 area 0
```

<img width="592" height="157" alt="Configure OSPF R1" src="https://github.com/user-attachments/assets/89252119-1953-40be-9330-c7b17c2e7fcb" />

### R2

``` bash
router ospf 1
network 10.0.12.0 0.0.0.3 area 0
network 10.0.23.0 0.0.0.3 area 0
```

<img width="942" height="172" alt="Configure OSPF R2" src="https://github.com/user-attachments/assets/8e362129-bc28-4eb7-8042-05f9c4907527" />

### R3

``` bash
router ospf 1
network 10.0.13.0 0.0.0.3 area 0
network 10.0.23.0 0.0.0.3 area 0
```

<img width="1047" height="246" alt="Configure OSPF R3" src="https://github.com/user-attachments/assets/32c3b96b-05f4-4018-88f7-42ed1de58797" />

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

<img width="840" height="201" alt="Verify R1" src="https://github.com/user-attachments/assets/aa9cd79a-2f3d-4411-b1e7-e6975192744a" />
<img width="867" height="147" alt="Verify R2" src="https://github.com/user-attachments/assets/9d47432b-c3fb-4d32-a773-22d7377bac14" />
<img width="857" height="150" alt="Verify R3" src="https://github.com/user-attachments/assets/8dd1fd0b-81d7-4e8c-9cb6-b0afb6fbb2a7" />



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

<img width="1261" height="832" alt="Failover Test" src="https://github.com/user-attachments/assets/7e3cfb59-d9a6-4072-beb1-4a7d83fce301" />

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
