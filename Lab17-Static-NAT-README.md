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

> <img width="617" height="717" alt="new topology" src="https://github.com/user-attachments/assets/184872dd-69b0-4790-b36a-a48c9b8d16d8" />


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

> <img width="887" height="275" alt="configure server" src="https://github.com/user-attachments/assets/09537ce7-bce2-4740-a4b9-6f5a95bca0fe" />


> <img width="1917" height="397" alt="enable http" src="https://github.com/user-attachments/assets/5ec87d05-bc74-4a46-896d-3947f67f71c2" />


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

>  <img width="431" height="227" alt="verify internal connectivity" src="https://github.com/user-attachments/assets/2292fadd-c908-42d1-accc-6dd5c33d714f" />

>  <img width="702" height="717" alt="verify internal connectivity2" src="https://github.com/user-attachments/assets/8855ab71-3acb-4d58-b890-cfcdc52c0536" />


---

# Step 3 – Remove PAT

```cisco
no ip nat inside source list 1 interface g0/0/1 overload
no access-list 1
```

> <img width="527" height="137" alt="Remove PAT from Lab 16" src="https://github.com/user-attachments/assets/8b781fda-e683-4bd9-a626-ef699c83a685" />


---

# Step 4 – Configure Static NAT

```cisco
ip nat inside source static 192.168.1.100 203.0.113.100
```

> <img width="542" height="77" alt="configure static nat" src="https://github.com/user-attachments/assets/12ac443d-0030-46e3-9120-1d2a455811a9" />


---

# Step 5 – Reconfigure the Public Network

Because an Outside PC was added, the original /30 public network no longer had enough usable IP addresses. The outside network was redesigned as **203.0.113.0/24**.

## Reconfigure R1

```cisco
interface g0/0/1
 ip address 203.0.113.1 255.255.255.0
 no shutdown
```

> <img width="477" height="122" alt="r1 fix" src="https://github.com/user-attachments/assets/3d9547d9-2f8f-4b71-a0dc-852c8b5cd76e" />


## Reconfigure the ISP Router

```cisco
interface g0/0/0
 ip address 203.0.113.2 255.255.255.0
 no shutdown
```

> <img width="486" height="166" alt="isp fix" src="https://github.com/user-attachments/assets/08c5cc2e-67a4-4ab3-81e7-2c68484920fd" />


## Configure the Outside PC

| Setting | Value |
|---------|-------|
| IP Address | 203.0.113.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 203.0.113.2 |

> <img width="865" height="325" alt="configure pc0" src="https://github.com/user-attachments/assets/4f4b8d20-66c6-4c15-a039-b258fa972c55" />


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

> <img width="697" height="477" alt="verify mapping" src="https://github.com/user-attachments/assets/83cf13a0-4610-4a93-86b9-03cfb08786e9" />


---

# Step 7 – Configure the ISP Static Route

Configure the ISP router so packets destined for the translated public IP are forwarded to R1.

```cisco
ip route 203.0.113.100 255.255.255.255 203.0.113.1
```

> <img width="577" height="107" alt="configure isp router" src="https://github.com/user-attachments/assets/946c8b0a-93ec-4471-b1ae-c771267dda8e" />


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

> <img width="457" height="652" alt="verify fix " src="https://github.com/user-attachments/assets/52b3c706-4d67-45eb-b4be-8bf3048d0eb1" />



> <img width="711" height="735" alt="verify static nat worked" src="https://github.com/user-attachments/assets/d11caec5-c782-4b94-8a13-83259d7e5a3b" />


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

> <img width="637" height="126" alt="nat translation" src="https://github.com/user-attachments/assets/254c1b1d-4c33-4cdd-9f0f-3325e0c57a7d" />


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
