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
<img width="902" height="292" alt="topology" src="https://github.com/user-attachments/assets/f3aae94a-7872-4610-8800-f2d04ba0c729" />

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
<img width="837" height="370" alt="configure R1" src="https://github.com/user-attachments/assets/04dd3544-b740-4ff0-9d61-71a4f1316e51" />


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

<img width="882" height="401" alt="configure R2" src="https://github.com/user-attachments/assets/1f9f3354-37b0-4387-8008-6cadca7a4ffa" />


---

# OSPF Configuration

## Configure OSPF on R1

```cisco
router ospf 1

network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0
```

<img width="595" height="155" alt="Configure OSPF on R1" src="https://github.com/user-attachments/assets/383d5071-1939-4d9a-8327-fb8b3812aa0c" />


---

## Configure OSPF on R2

```cisco
router ospf 1

network 192.168.2.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0
```

<img width="1037" height="162" alt="Configure OSPF on R2" src="https://github.com/user-attachments/assets/a7a70dc9-643c-459a-a892-a52868557fe2" />


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


<img width="930" height="107" alt="Verify OSPF Neighbor R1" src="https://github.com/user-attachments/assets/fffb2064-0551-4c8a-8ca5-5389274f8c61" />


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

<img width="792" height="304" alt="Verify Learned Routes R1" src="https://github.com/user-attachments/assets/5f8fa58b-73ec-42bf-9ccb-f0adb2fb8fee" />
<img width="720" height="305" alt="Verify Learned Routes R2" src="https://github.com/user-attachments/assets/ed79a08e-eb5d-4824-8159-a767703e16b2" />



---

## Verify End-to-End Connectivity

From PC0:

```text
ping 192.168.2.10
```

### Result

Successful communication between both LANs.

<img width="740" height="652" alt="final test" src="https://github.com/user-attachments/assets/42f3448e-dee5-4348-af9a-50f7794d72f5" />


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
