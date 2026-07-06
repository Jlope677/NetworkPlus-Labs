
# Lab 17 – Static NAT (Publishing an Internal Web Server)

## Objective

Configure **Static NAT** to publish an internal web server to a public IP address. Verify external access, troubleshoot connectivity issues, and compare Static NAT with PAT.

---

# Real-World Scenario

A company hosts an internal web server for customers. The server must be reachable from the Internet using a dedicated public IP address. Unlike PAT, which allows many devices to share one public address for outbound traffic, Static NAT permanently maps one private IP to one public IP.

---

# Skills Demonstrated

- Static NAT
- Cisco IOS NAT Configuration
- Static Routing
- HTTP Services
- IPv4 Addressing
- NAT Verification
- Troubleshooting
- Topology Design

---

# Technologies Used

- Cisco ISR4331 Routers
- Cisco Catalyst 2960 Switches
- Cisco Packet Tracer
- Static NAT
- HTTP
- IPv4

---

# Prerequisites

This lab builds directly on **Lab 16 – PAT (NAT Overload)**.

The following configuration from Lab 16 was reused:

- Inside/Outside NAT interfaces
- Router interface IP addressing
- Default route on R1
- Return route on ISP router

PAT configuration was removed and replaced with Static NAT.

---

# Lab Design Changes

To better simulate a real enterprise environment, the topology was expanded.

Changes made:

- Added an internal Web Server
- Added an ISP Switch
- Added an Outside PC
- Expanded the public network from **203.0.113.0/30** to **203.0.113.0/24**

## Why the Topology Changed

The original /30 network from Lab 16 only supports two usable IP addresses—one for R1 and one for the ISP router.

To simulate an external client, an additional host was required. The public network was expanded to a /24, allowing:

- ISP Router
- R1 Outside Interface
- Outside Test PC

to exist on the same network.

> Insert updated topology screenshot here.

---

# IP Addressing

| Device | Interface | Address |
|--------|-----------|---------|
| PC0 | Fa0 | 192.168.1.10/24 |
| Server0 | Fa0 | 192.168.1.100/24 |
| R1 G0/0/0 | Inside | 192.168.1.1/24 |
| R1 G0/0/1 | Outside | 203.0.113.1/24 |
| ISP G0/0/0 | Public | 203.0.113.2/24 |
| Outside PC | Fa0 | 203.0.113.10/24 |

---

# Step 1 – Configure the Web Server

Configure:

- IP Address: 192.168.1.100
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.1.1

Enable **HTTP** under:

Services → HTTP → On

> Insert Server IP screenshot.

> Insert HTTP Services screenshot.

---

# Step 2 – Verify Internal Access

From PC0:

```text
ping 192.168.1.100
```

Browse to:

```text
http://192.168.1.100
```

The Cisco Packet Tracer default webpage should load.

> Insert PC ping screenshot.

> Insert Internal browser screenshot.

---

# Step 3 – Remove PAT

Since this lab demonstrates Static NAT, remove the PAT configuration created in Lab 16.

```cisco
no ip nat inside source list 1 interface g0/0/1 overload
no access-list 1
```

> Insert Remove PAT screenshot.

---

# Step 4 – Configure Static NAT

```cisco
ip nat inside source static 192.168.1.100 203.0.113.100
```

> Insert Static NAT configuration screenshot.

---

# Step 5 – Verify Existing Configuration

Verify the reused configuration from Lab 16.

```cisco
show running-config
```

Confirm:

- Inside interface
- Outside interface
- Static NAT entry
- Default route

> Insert R1 configuration screenshot.

Verify the ISP router still contains the return route.

> Insert ISP route screenshot.

---

# Step 6 – Configure the Outside PC

Configure:

- IP Address: 203.0.113.10
- Mask: 255.255.255.0
- Gateway: 203.0.113.2

> Insert Outside PC screenshot.

---

# Verification

From the Outside PC:

```text
ping 203.0.113.2
ping 203.0.113.1
ping 203.0.113.100
```

All three pings should succeed.

Open:

```text
http://203.0.113.100
```

The internal web page should now load using the public IP.

> Insert Outside PC ping screenshot.

> Insert Outside browser screenshot.

---

# Verify NAT Translation

```cisco
show ip nat translations
```

Expected:

- Static mapping for 192.168.1.100 ⇄ 203.0.113.100
- TCP port 80 translation after opening the webpage

> Insert NAT translation screenshot.

## Translation Table

| Field | Meaning |
|------|---------|
| Inside Local | Private server IP |
| Inside Global | Public IP assigned by Static NAT |
| Outside Local | External client |
| Outside Global | External client public address |

---

# Troubleshooting

## Issue Encountered

Initially, the Outside PC could not reach the web server.

The browser displayed **Request Timeout**.

## Root Cause

The original /30 addressing scheme from Lab 16 could not support an ISP router, R1, and an Outside PC simultaneously.

## Resolution

The outside network was redesigned to a /24 network.

After updating:

- Router IP addressing
- Outside PC configuration
- Routing

the web page became reachable through the Static NAT mapping.

---

# Static NAT vs PAT

| Static NAT | PAT |
|------------|-----|
| One private IP maps to one public IP | Many private IPs share one public IP |
| Used for servers | Used for client Internet access |
| Permanent mapping | Dynamic translation |
| Supports inbound connections | Primarily outbound connections |

---

# Lessons Learned

- Static NAT permanently publishes internal resources.
- PAT and Static NAT solve different networking problems.
- Internal connectivity should always be verified before testing NAT.
- Routing and addressing must be validated before assuming NAT is the issue.
- Expanding a topology may require redesigning the IP addressing scheme.

---

# Interview Takeaways

Be prepared to explain:

- The difference between Static NAT and PAT.
- Why Static NAT is used for public-facing servers.
- Why the outside network changed from /30 to /24.
- How you verified the NAT translation table.
- How you isolated the routing issue during troubleshooting.

---

# Recruiter Notes

- Configured Cisco Static NAT to publish an internal web server.
- Reused and modified an existing PAT deployment.
- Redesigned the outside addressing scheme to support additional public devices.
- Verified functionality using ICMP, HTTP, routing, and NAT translation tables.
- Documented troubleshooting methodology and final resolution.
