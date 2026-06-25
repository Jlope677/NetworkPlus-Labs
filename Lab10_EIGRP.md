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


<img width="761" height="252" alt="erase the ospf R1" src="https://github.com/user-attachments/assets/b2c67ccb-45e8-49c4-ba70-4abe73598f1b" />

<img width="707" height="172" alt="erase the ospf R2" src="https://github.com/user-attachments/assets/2f20509d-9775-4bcf-b815-9ffcd90bf2a7" />


<img width="747" height="227" alt="erase the ospf R3" src="https://github.com/user-attachments/assets/538dbeee-47d0-42a2-b8e3-ac42fd8ae4f7" />



------------------------------------------------------------------------

## Step 2 - Configure EIGRP

### R1

``` bash
router eigrp 100
no auto-summary
network 10.0.12.0 0.0.0.3
network 10.0.13.0 0.0.0.3
```


<img width="592" height="125" alt="Configure EIGRP R1" src="https://github.com/user-attachments/assets/728f4869-4f85-4ac7-8fa9-a08c9a2d184a" />



### R2

``` bash
router eigrp 100
no auto-summary
network 10.0.12.0 0.0.0.3
network 10.0.23.0 0.0.0.3
```

<img width="952" height="147" alt="Configure EIGRP R2" src="https://github.com/user-attachments/assets/b6a25b2f-a57a-4caa-9fb8-2e518d781526" />


### R3

``` bash
router eigrp 100
no auto-summary
network 10.0.13.0 0.0.0.3
network 10.0.23.0 0.0.0.3
```
<img width="872" height="221" alt="Configure EIGRP R3" src="https://github.com/user-attachments/assets/b68104e3-172f-42d4-9b36-dfdcec6b3d86" />

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

<img width="877" height="674" alt="Verify eigrp R1" src="https://github.com/user-attachments/assets/5a91edc6-ed0a-456e-82d3-efb20d53cd71" />
<img width="771" height="665" alt="Verify eigrp R2" src="https://github.com/user-attachments/assets/4a60e099-ace7-47b0-88d4-8b4f6597bc2f" />
<img width="737" height="684" alt="Verify eigrp R3" src="https://github.com/user-attachments/assets/fd8d724e-3f69-4c2c-9fa8-b04382fdf039" />



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


