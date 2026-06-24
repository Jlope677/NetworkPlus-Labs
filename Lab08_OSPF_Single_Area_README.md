# Lab 08 - OSPF Single Area Routing

## Objective

Configure OSPF Area 0 between two routers and verify that routes are learned dynamically without using static routes.

This lab demonstrates:

- Dynamic routing with OSPF
- OSPF neighbor relationships
- Route advertisements
- Route learning
- Routing table verification
- End-to-end connectivity testing
- Enterprise routing fundamentals

---

# Topology

PC0 (192.168.1.10)
 |
Fa0
 |
R1 G0/0/0 (192.168.1.1)
 |
R1 G0/0/1 (10.0.0.1/30)
 |
R2 G0/0/0 (10.0.0.2/30)
 |
R2 G0/0/1 (192.168.2.1)
 |
Fa0
 |
PC1 (192.168.2.10)

---

# Devices Used

- 2 Cisco Routers
- 2 PCs
- Cisco Packet Tracer

---

# Network Design

## LAN 1
Network: 192.168.1.0/24
Gateway: 192.168.1.1

## WAN Link
Network: 10.0.0.0/30
R1 = 10.0.0.1
R2 = 10.0.0.2

## LAN 2
Network: 192.168.2.0/24
Gateway: 192.168.2.1

---

# Configuration Steps

## Configure R1

interface g0/0/0
ip address 192.168.1.1 255.255.255.0
no shutdown

interface g0/0/1
ip address 10.0.0.1 255.255.255.252
no shutdown

## Configure R2

interface g0/0/0
ip address 10.0.0.2 255.255.255.252
no shutdown

interface g0/0/1
ip address 192.168.2.1 255.255.255.0
no shutdown

---

# OSPF Configuration

## R1

router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0

## R2

router ospf 1
network 192.168.2.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0

---

# Reasoning Behind the Configuration

The network statements tell OSPF which interfaces should participate in OSPF.

Example:

network 192.168.2.0 0.0.0.255 area 0

Meaning:

- Enable OSPF on interfaces in the 192.168.2.0/24 network.
- Advertise that network to neighboring routers.
- Place the interface into Area 0.

Example:

network 10.0.0.0 0.0.0.3 area 0

Meaning:

- Enable OSPF on the router-to-router WAN link.
- Allow routers to form neighbor relationships.
- Exchange routing information.

Without the 10.0.0.0 network statement, the routers would never become OSPF neighbors.

---

# Verification Performed

## Verify Neighbor Relationship

show ip ospf neighbor

Result:

Neighbor state reached FULL.

## Verify Routing Table

show ip route

Observed:

O 192.168.1.0/24
O 192.168.2.0/24

O = OSPF Learned Route

## Verify Connectivity

PC0 successfully pinged PC1.

---

# Troubleshooting Observed

The first ping experienced a timeout.

Reason:

ARP tables were being built and OSPF had recently converged.

Subsequent pings succeeded normally.

---

# Lessons Learned

- OSPF is a Link-State routing protocol.
- OSPF uses Area 0 as the backbone area.
- Routers must become neighbors before exchanging routes.
- OSPF routes appear with an O in the routing table.
- Dynamic routing reduces manual route configuration.
- Wildcard masks are the inverse of subnet masks.
- OSPF is one of the most common enterprise routing protocols.

---

# Skills Demonstrated

- Cisco IOS CLI
- OSPF Configuration
- Dynamic Routing
- Route Verification
- Routing Table Analysis
- Neighbor Verification
- Network Troubleshooting
- Packet Tracer Design
- Enterprise Networking Fundamentals
