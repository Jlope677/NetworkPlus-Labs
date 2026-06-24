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

<img width="902" height="292" alt="topology" src="https://github.com/user-attachments/assets/425e6e58-37e3-4860-ae93-cdf965000267" />

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

<img width="837" height="370" alt="configure R1" src="https://github.com/user-attachments/assets/0813717a-b07b-47bf-a140-682c155c647d" />

## Configure R2

interface g0/0/0
ip address 10.0.0.2 255.255.255.252
no shutdown

interface g0/0/1
ip address 192.168.2.1 255.255.255.0
no shutdown

<img width="882" height="401" alt="configure R2" src="https://github.com/user-attachments/assets/6eccc929-58a6-42df-b4bb-dccf038d74eb" />

---

# OSPF Configuration

## R1

router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0

<img width="595" height="155" alt="Configure OSPF on R1" src="https://github.com/user-attachments/assets/4dbdb1b0-bb85-4d6c-bca0-6582c62a7440" />

## R2

router ospf 1
network 192.168.2.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0

<img width="1037" height="162" alt="Configure OSPF on R2" src="https://github.com/user-attachments/assets/0b6d7300-feff-4908-a1a7-b92892fe3614" />

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

<img width="930" height="107" alt="Verify OSPF Neighbor R1" src="https://github.com/user-attachments/assets/d817a407-3144-497a-bece-866c6a9c4f20" />

## Verify Routing Table

show ip route

Observed:

O 192.168.1.0/24
O 192.168.2.0/24

O = OSPF Learned Route

<img width="792" height="304" alt="Verify Learned Routes R1" src="https://github.com/user-attachments/assets/73e3c8be-3abe-44f0-a5e9-405f7a40dc9d" />
<img width="720" height="305" alt="Verify Learned Routes R2" src="https://github.com/user-attachments/assets/086d493d-d056-40ff-a79e-fcf2bca5e43c" />


## Verify Connectivity

PC0 successfully pinged PC1.

<img width="740" height="652" alt="final test" src="https://github.com/user-attachments/assets/b442c892-4718-496e-b3a6-ce01e62033d0" />

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
