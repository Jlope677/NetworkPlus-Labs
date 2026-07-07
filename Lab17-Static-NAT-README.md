# Lab 17 – Static NAT (Publishing an Internal Web Server)

## Objective

Configure **Static NAT** to publish an internal web server using a dedicated public IP address. Verify external access, troubleshoot connectivity issues, validate NAT translations, and compare Static NAT with PAT.

---

# Real-World Scenario

A company hosts an internal web server that must be accessible from the Internet. Unlike PAT, which allows many internal devices to share one public IP for outbound traffic, Static NAT permanently maps one private IP address to one public IP address so external clients can reach the service.

---

# Skills Demonstrated

- Static NAT
- Cisco IOS NAT Configuration
- Static Routing
- HTTP Services
- IPv4 Addressing
- Packet Flow Analysis
- NAT Verification
- Enterprise Troubleshooting
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

- Inside NAT interface
- Outside NAT interface
- Default route on R1

PAT was removed and replaced with Static NAT.

---

# Lab Design Changes

To better simulate an enterprise environment, the topology was expanded.

Changes made:

- Added an internal Web Server
- Added an ISP Switch
- Added an Outside PC
- Expanded the outside network from **203.0.113.0/30** to **203.0.113.0/24**

## Why the Topology Changed

The original /30 network only supported two usable IP addresses (R1 and the ISP router).

To simulate an Internet client, an additional public device was required. Expanding the subnet to **/24** allowed the ISP Router, R1, the Outside PC, and the published Static NAT address to exist on the same public network.

> Insert updated topology screenshot.

---

# IP Addressing

| Device | Interface | Address |
|---------|-----------|---------|
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

**Services → HTTP → On**

> Insert Server IP configuration screenshot.

> Insert HTTP services screenshot.

---

# Step 2 – Verify Internal Connectivity

From PC0:

```text
ping 192.168.1.100
```

Open:

```text
http://192.168.1.100
```

Verify the Packet Tracer web page loads.

> Insert internal ping screenshot.

> Insert internal browser screenshot.

---

# Step 3 – Remove PAT

```cisco
no ip nat inside source list 1 interface g0/0/1 overload
no access-list 1
```

> Insert PAT removal screenshot.

---

# Step 4 – Configure Static NAT

```cisco
ip nat inside source static 192.168.1.100 203.0.113.100
```

> Insert Static NAT configuration screenshot.

---

# Step 5 – Reconfigure the Public Network

Because an Outside PC was added, the original /30 public network no longer had enough usable IP addresses. The outside network was redesigned as **203.0.113.0/24**.

## Reconfigure R1

```cisco
interface g0/0/1
 ip address 203.0.113.1 255.255.255.0
 no shutdown
```

> Insert R1 outside interface screenshot.

## Reconfigure the ISP Router

```cisco
interface g0/0/0
 ip address 203.0.113.2 255.255.255.0
 no shutdown
```

> Insert ISP interface screenshot.

## Configure the Outside PC

| Setting | Value |
|---------|-------|
| IP Address | 203.0.113.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 203.0.113.2 |

> Insert Outside PC configuration screenshot.

---

# Step 6 – Validate Existing NAT Configuration

The inside/outside NAT interfaces from Lab 16 were reused.

Verify:

```cisco
show running-config
```

Confirm:

- `ip nat inside`
- `ip nat outside`
- Static NAT mapping
- Default route

> Insert running-config screenshot.

---

# Step 7 – Configure the ISP Static Route

Configure the ISP router so packets destined for the translated public IP are forwarded to R1.

```cisco
ip route 203.0.113.100 255.255.255.255 203.0.113.1
```

> Insert ISP route screenshot.

## Why This Was Necessary

Static NAT only translates packets **after they reach R1**.

The ISP router must first know where to forward traffic destined for **203.0.113.100**.

Packet flow:

```text
Outside PC
      │
      ▼
203.0.113.100
      │
      ▼
ISP Router
      │
      ▼
R1
      │
203.0.113.100 → 192.168.1.100
      │
      ▼
Internal Web Server
```

---

# Step 8 – Verification

From the Outside PC:

```text
ping 203.0.113.2
ping 203.0.113.1
ping 203.0.113.100
```

Open:

```text
http://203.0.113.100
```

Verify:

- All pings succeed.
- The web page loads using the public IP.

> Insert Outside PC ping screenshot.

> Insert Outside browser screenshot.

---

# Step 9 – Verify NAT Translation

```cisco
show ip nat translations
```

Expected:

- Static mapping:
  - Inside Local: **192.168.1.100**
  - Inside Global: **203.0.113.100**
- TCP translation after opening the webpage.

> Insert NAT translation screenshot.

| Field | Meaning |
|------|---------|
| Inside Local | Private server IP |
| Inside Global | Public IP assigned by Static NAT |
| Outside Local | External client |
| Outside Global | External client address |

---

# Troubleshooting

## Issue Encountered

After adding the Outside PC, the published web server could not initially be reached.

Symptoms:

- ICMP to **203.0.113.100** failed.
- Browser displayed **Request Timeout**.
- Internal access to **192.168.1.100** continued to work.

## Root Cause

The original /30 public network did not support an additional public host.

The ISP router also required a static host route directing traffic for **203.0.113.100** toward R1 before Static NAT could occur.

## Resolution

- Expanded the public network from /30 to /24.
- Reconfigured the R1 outside interface.
- Reconfigured the ISP router interface.
- Added the Outside PC.
- Configured the ISP static host route.
- Verified HTTP connectivity.
- Verified translations with `show ip nat translations`.

---

# Static NAT vs PAT

| Static NAT | PAT |
|------------|-----|
| One private IP maps to one public IP | Many private IPs share one public IP |
| Used for servers | Used for client Internet access |
| Permanent mapping | Dynamic overload |
| Supports inbound connections | Primarily outbound connections |

---

# Lessons Learned

- Static NAT permanently publishes internal resources.
- PAT and Static NAT solve different networking problems.
- Routing determines where packets travel before NAT occurs.
- Static NAT does not replace routing.
- Always verify internal connectivity before testing NAT.
- Expanding a topology may require redesigning the addressing scheme.

---

# Interview Takeaways

Be prepared to explain:

- Static NAT vs PAT.
- Why Static NAT is used for public-facing servers.
- Why the public network changed from /30 to /24.
- How routing and NAT work together.
- How you verified the NAT translation table.
- How you isolated the routing issue.

---

# Recruiter Notes

- Built upon a previous PAT deployment by replacing overload translation with Static NAT.
- Redesigned the public network to support additional Internet-facing devices.
- Published an internal web server using a dedicated public IP address.
- Validated packet flow using ICMP, HTTP, static routing, and Cisco NAT translation tables.
- Documented troubleshooting from initial failure through final resolution using an enterprise-style workflow.
