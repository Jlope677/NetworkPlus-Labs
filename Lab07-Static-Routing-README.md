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

```text
PC0 ---- R1 ---- R2 ---- PC1

PC0 Fa0 -> R1 G0/0/0
R1 G0/0/1 -> R2 G0/0/0
R2 G0/0/1 -> PC1 Fa0
```

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

---

# Final Connectivity Test

From PC0:

```cmd
ping 192.168.2.10
```

Result:

✅ Successful

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

# Screenshot Checklist

- Topology Diagram
- PC0 Configuration
- PC1 Configuration
- R1 Interface Configuration
- R2 Interface Configuration
- Successful Router-to-Router Ping
- Failed End-to-End Ping
- Static Route Configuration on R1
- Static Route Configuration on R2
- show ip route on R1
- show ip route on R2
- Successful End-to-End Ping
