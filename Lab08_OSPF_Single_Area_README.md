# Lab 08 — OSPF Single Area Routing

## Overview

This lab demonstrates how to configure **OSPF (Open Shortest Path First)** between two routers using a single backbone area (Area 0). The routers dynamically exchange routing information, eliminating the need for static routes and allowing end-to-end communication between remote LANs.

---

## Technologies Used

- Cisco IOS
- OSPFv2
- Packet Tracer
- IPv4 Routing
- Dynamic Routing Protocols

---

## Skills Demonstrated

- OSPF Configuration
- Dynamic Route Advertisement
- OSPF Neighbor Formation
- Route Verification
- Routing Table Analysis
- Cisco IOS CLI
- Enterprise Network Troubleshooting
- End-to-End Connectivity Testing

---

# Objective

Configure OSPF Area 0 between two routers and verify that:

- OSPF neighbors form successfully
- Routes are exchanged dynamically
- Routing tables populate automatically
- End devices communicate across networks
- OSPF converges successfully

---

# Topology

```text
PC0 (192.168.1.10)
      |
      |
      |
R1 G0/0/0 (192.168.1.1)
      |
R1 G0/0/1 (10.0.0.1/30)
      |
R2 G0/0/0 (10.0.0.2/30)
      |
R2 G0/0/1 (192.168.2.1)
      |
PC1 (192.168.2.10)
```

---

# Devices Used

| Device | Quantity |
|----------|----------|
| Cisco Router | 2 |
| PC | 2 |
| Packet Tracer | 1 |

---

# IP Addressing Scheme

## LAN 1

| Device | IP Address |
|----------|----------|
| PC0 | 192.168.1.10 |
| R1 G0/0/0 | 192.168.1.1 |

Subnet:

```text
192.168.1.0/24
```

---

## WAN Link

| Interface | IP Address |
|------------|------------|
| R1 G0/0/1 | 10.0.0.1 |
| R2 G0/0/0 | 10.0.0.2 |

Subnet:

```text
10.0.0.0/30
```

---

## LAN 2

| Device | IP Address |
|----------|----------|
| PC1 | 192.168.2.10 |
| R2 G0/0/1 | 192.168.2.1 |

Subnet:

```text
192.168.2.0/24
```

---

# Router Configuration

## Configure R1

```cisco
interface g0/0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

interface g0/0/1
 ip address 10.0.0.1 255.255.255.252
 no shutdown
```

### Screenshot

![Configure R1](images/configure-r1.png)

---

## Configure R2

```cisco
interface g0/0/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown

interface g0/0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
```

### Screenshot

![Configure R2](images/configure-r2.png)

---

# OSPF Configuration

## Configure OSPF on R1

```cisco
router ospf 1

network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0
```

### Screenshot

![OSPF R1](images/ospf-r1.png)

---

## Configure OSPF on R2

```cisco
router ospf 1

network 192.168.2.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0
```

### Screenshot

![OSPF R2](images/ospf-r2.png)

---

# Configuration Breakdown

## Why Advertise the LAN Networks?

```cisco
network 192.168.1.0 0.0.0.255 area 0
network 192.168.2.0 0.0.0.255 area 0
```

These commands:

- Enable OSPF on the LAN interfaces
- Advertise the LAN networks
- Allow remote routers to learn the routes dynamically

---

## Why Advertise the WAN Link?

```cisco
network 10.0.0.0 0.0.0.3 area 0
```

This command:

- Enables OSPF on the router-to-router connection
- Allows routers to discover each other
- Forms the OSPF neighbor relationship
- Exchanges routing information

Without this network statement, OSPF neighbors would never form.

---

# Verification

## Verify Neighbor Relationship

```cisco
show ip ospf neighbor
```

### Expected Result

```text
FULL
```

This confirms:

- OSPF adjacency formed successfully
- Database synchronization completed
- Route exchange can occur

### Screenshot

![Neighbor Verification](images/neighbor-r1.png)

---

## Verify Routing Table

```cisco
show ip route
```

### Expected Routes

```text
O 192.168.1.0/24
O 192.168.2.0/24
```

### What the "O" Means

```text
O = OSPF Learned Route
```

The route was dynamically learned from a neighboring router.

### Screenshots

![Route Table R1](images/routes-r1.png)

![Route Table R2](images/routes-r2.png)

---

## Verify End-to-End Connectivity

From PC0:

```text
ping 192.168.2.10
```

### Result

Successful communication between both LANs.

### Screenshot

![Ping Test](images/final-test.png)

---

# Troubleshooting Notes

## Initial Ping Timeout

Observed:

```text
Request Timed Out
```

Reason:

- ARP tables were being populated
- OSPF had recently converged

Resolution:

- Retried ping
- Connectivity succeeded normally

This behavior is expected in many real-world environments.

---

# Key Commands Learned

```cisco
show ip route

show ip ospf neighbor

show ip protocols

show ip ospf interface brief
```

These commands are frequently used by:

- Network Administrators
- NOC Technicians
- Network Engineers
- Infrastructure Engineers

---

# Business Value

Using OSPF instead of static routes provides:

- Faster route convergence
- Easier network scalability
- Reduced administrative overhead
- Automatic route updates
- Better support for enterprise environments

---

# Lessons Learned

- OSPF is a Link-State routing protocol.
- Area 0 serves as the OSPF backbone.
- Neighbor relationships must form before routes are exchanged.
- OSPF routes appear with an **O** in the routing table.
- Wildcard masks are the inverse of subnet masks.
- Dynamic routing reduces manual configuration and improves scalability.
- OSPF is one of the most widely deployed enterprise routing protocols.

---

# Recruiter Skills Mapping

This lab demonstrates experience with:

- Cisco Routing
- OSPF
- Dynamic Routing Protocols
- TCP/IP Networking
- Route Advertisement
- Routing Table Analysis
- Layer 3 Troubleshooting
- Enterprise Network Design
- Cisco IOS CLI
- Network Documentation

---

## Lab Outcome

Successfully configured and verified OSPF Single Area Routing between two Cisco routers.

✔ OSPF neighbors formed successfully

✔ Routes learned dynamically

✔ Routing tables populated correctly

✔ End-to-end connectivity verified

✔ Enterprise routing concepts reinforced
