# Lab 07 - Static Routing

## Objective

Configure static routes between two routers to allow communication between hosts located on different networks.

This lab demonstrates:

- Router interface configuration
- Directly connected routes
- Static routes
- Next-hop routing
- Routing table verification
- End-to-end connectivity testing
- Network troubleshooting

---

# Topology

<img width="1157" height="382" alt="topology" src="https://github.com/user-attachments/assets/9df3244f-0282-443d-a73a-50436b7e0746" />


---

# Physical Connections

| Device A | Interface | Device B | Interface |
|-----------|-----------|-----------|-----------|
| PC0 | Fa0 | R1 | G0/0/0 |
| R1 | G0/0/1 | R2 | G0/0/0 |
| R2 | G0/0/1 | PC1 | Fa0 |

---

# IP Addressing Plan

## PC0

| Setting | Value |
|----------|----------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |

## R1

| Interface | IP Address |
|------------|------------|
| G0/0/0 | 192.168.1.1/24 |
| G0/0/1 | 10.0.0.1/30 |

## R2

| Interface | IP Address |
|------------|------------|
| G0/0/0 | 10.0.0.2/30 |
| G0/0/1 | 192.168.2.1/24 |

## PC1

| Setting | Value |
|----------|----------|
| IP Address | 192.168.2.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.2.1 |

---

# Configure R1

```bash
enable
configure terminal

hostname R1

interface g0/0/0
ip address 192.168.1.1 255.255.255.0
no shutdown

interface g0/0/1
ip address 10.0.0.1 255.255.255.252
no shutdown
```

<img width="857" height="377" alt="configure router1" src="https://github.com/user-attachments/assets/4a709b73-037f-42e0-8883-2918a09f28e1" />

---

# Configure R2

```bash
enable
configure terminal

hostname R2

interface g0/0/0
ip address 10.0.0.2 255.255.255.252
no shutdown

interface g0/0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
```

<img width="1072" height="425" alt="configure R2" src="https://github.com/user-attachments/assets/ff8507a3-6207-4123-8123-5341e71036dd" />

---

# Verify Direct Connectivity

From R1:

```bash
ping 10.0.0.2
```

Result: Successful

From R2:

```bash
ping 10.0.0.1
```

Result: Successful


<img width="787" height="147" alt="verify direct connectivity r1" src="https://github.com/user-attachments/assets/f2e47d0e-980d-4a75-a0e8-46efdbc2b95f" />

<img width="742" height="167" alt="verify direct connectivity r2" src="https://github.com/user-attachments/assets/3ad88ea7-4e0a-442f-9e5a-2576e8e8e2ea" />

---

# Initial End-to-End Test

From PC0:

```cmd
ping 192.168.2.10
```

Result:

Destination Host Unreachable

Reason:

The routers only knew their directly connected networks and had no route to the remote LAN.


<img width="936" height="397" alt="Test End-to-End" src="https://github.com/user-attachments/assets/3ed57a44-1c1a-4df4-9276-a879161984da" />

---

# Configure Static Routes

## On R1

```bash
ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

Meaning:

Traffic destined for the 192.168.2.0/24 network is forwarded to R2.

## On R2

```bash
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

Meaning:

Traffic destined for the 192.168.1.0/24 network is forwarded to R1.


<img width="672" height="137" alt="Configure Static Routes R1" src="https://github.com/user-attachments/assets/99b12016-94a3-4705-bba9-66dae9827b28" />

<img width="591" height="97" alt="Configure Static Routes R2" src="https://github.com/user-attachments/assets/2382daa3-6b09-46d8-a217-11e08d7c7d4b" />


---

# Verify Routing Tables

```bash
show ip route
```

Expected Entries:

```text
R1:
S 192.168.2.0/24 [1/0] via 10.0.0.2

R2:
S 192.168.1.0/24 [1/0] via 10.0.0.1
```

<img width="706" height="307" alt="Verify Routes R1" src="https://github.com/user-attachments/assets/6204fe97-5b7c-49a9-af78-72450e2ef8b5" />
<img width="860" height="337" alt="Verify Routes R2" src="https://github.com/user-attachments/assets/189f3921-824e-44d8-9e3d-e85dc3297a7d" />


---

# Final Connectivity Test

From PC0:

```cmd
ping 192.168.2.10
```

Result:

✅ Successful


<img width="576" height="282" alt="test again pc0" src="https://github.com/user-attachments/assets/f04b561e-fee4-40de-bc5c-4d42f98e9f79" />

---

# Routing Process

1. PC0 sends traffic to its default gateway (192.168.1.1).
2. R1 checks its routing table.
3. R1 forwards traffic to next hop 10.0.0.2.
4. R2 receives the packet.
5. R2 sees 192.168.2.0 is directly connected.
6. R2 forwards the packet to PC1.
7. PC1 replies back using the reverse route.

---

# Key Concepts Learned

## Routing

The process of forwarding packets between different networks.

## Directly Connected Route

A route automatically learned when an interface is configured and active.

## Static Route

A manually configured route added by an administrator.

## Next-Hop Address

The IP address of the router that will receive the packet next.

## Routing Table

The table used by routers to determine where traffic should go.

## Administrative Distance

A value used to determine the most trusted route.

Connected Route = 0

Static Route = 1

---

# Troubleshooting Performed

- Verified interface IP addressing
- Verified interface status with `no shutdown`
- Verified PC addressing
- Verified default gateways
- Verified router-to-router connectivity
- Verified routing table entries
- Tested connectivity before and after static route configuration

---

# Lessons Learned

- Routers only know directly connected networks by default.
- Remote networks require routes.
- Static routes provide a manual path to remote destinations.
- Communication requires routing information in both directions.
- The next-hop address tells the router where to send traffic.
- `show ip route` is one of the most important troubleshooting commands.

---

# Skills Demonstrated

- Cisco IOS CLI
- Router Configuration
- Static Routing
- Routing Table Analysis
- Default Gateway Configuration
- Network Troubleshooting
- End-to-End Connectivity Testing
- Packet Tracer Design

---

- Static Route Configuration on R2
- show ip route on R1
- show ip route on R2
- Successful End-to-End Ping
