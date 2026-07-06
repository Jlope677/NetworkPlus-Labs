# Lab 16 – NAT/PAT Configuration

## Objective

Configure PAT (NAT Overload) so multiple internal private devices can access an external network using one public IP address.

---

## Real-World Scenario

A small company has multiple internal users on a private LAN. The company edge router must allow those users to reach an outside network using a single public-facing IP address.

PAT is configured on the edge router so private IP addresses are translated before traffic leaves the LAN.

---

## Skills Demonstrated

- Network Address Translation (NAT)
- Port Address Translation (PAT / NAT Overload)
- Inside and Outside NAT Interfaces
- Standard ACLs for NAT
- Static Default Routes
- Return Routes
- Cisco IOS Verification Commands
- Basic WAN Edge Troubleshooting

---

## Technologies Used

- Cisco Packet Tracer
- Cisco ISR Routers
- Cisco 2960 Switch
- Cisco IOS CLI
- IPv4 Addressing
- NAT/PAT
- ICMP

---

## Topology

```text
                 ISP Router
              G0/0 203.0.113.2/30
                     |
                     |
              G0/1 203.0.113.1/30
                  R1 NAT Router
              G0/0 192.168.1.1/24
                     |
                   Switch
                /          \
              PC0          PC1
```

<img src="images/topology.jpg" alt="NAT PAT topology" />

---

## IP Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| PC0 | Fa0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC1 | Fa0 | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| R1 | G0/0 | 192.168.1.1 | 255.255.255.0 | N/A |
| R1 | G0/1 | 203.0.113.1 | 255.255.255.252 | N/A |
| ISP | G0/0 | 203.0.113.2 | 255.255.255.252 | N/A |

---

## Lab Workflow

1. Build the topology.
2. Configure PC IP addressing.
3. Configure R1 inside and outside interfaces.
4. Configure the ISP router interface.
5. Test basic connectivity before NAT.
6. Create a standard ACL to identify inside addresses.
7. Configure NAT inside and outside interfaces.
8. Configure PAT using the outside interface.
9. Add routing.
10. Verify successful connectivity and NAT translations.

---

## Configure the PCs

### PC0

| Setting | Value |
|---------|-------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |

<img src="images/configure-pc0.jpg" alt="PC0 IP configuration" />

### PC1

| Setting | Value |
|---------|-------|
| IP Address | 192.168.1.20 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |

<img src="images/configure-pc1.jpg" alt="PC1 IP configuration" />

---

## Configure R1

```cisco
enable
configure terminal
hostname R1

interface g0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

interface g0/1
 ip address 203.0.113.1 255.255.255.252
 no shutdown
```

<img src="images/configure-r1.jpg" alt="R1 interface configuration" />

---

## Configure the ISP Router

```cisco
enable
configure terminal
hostname ISP

interface g0/0
 ip address 203.0.113.2 255.255.255.252
 no shutdown
```

<img src="images/configure-isp.jpg" alt="ISP router configuration" />

---

## Basic Connectivity Test Before NAT

Before NAT was configured, PC0 could reach its default gateway but could not reach the ISP router.

### PC0 to R1

```text
ping 192.168.1.1
```

Result: Successful

### PC0 to ISP

```text
ping 203.0.113.2
```

Result: Failed before NAT and routing were completed.

<img src="images/test-connectivity-pc-before-nat.jpg" alt="PC connectivity test before NAT" />

### R1 to ISP

```cisco
ping 203.0.113.2
```

Result: Successful

<img src="images/test-connectivity-r1-to-isp.jpg" alt="R1 ping to ISP router" />

---

## Configure the NAT ACL

The ACL identifies which inside addresses are allowed to be translated.

```cisco
access-list 1 permit 192.168.1.0 0.0.0.255
```

<img src="images/acl-nat-list.jpg" alt="NAT ACL configuration" />

### Why This ACL Is Used

The ACL does not block traffic in this lab. It identifies the private inside network that should be translated by NAT.

```text
192.168.1.0 0.0.0.255
```

This matches the full 192.168.1.0/24 LAN.

---

## Configure NAT Inside and Outside Interfaces

On R1:

```cisco
interface g0/0
 ip nat inside

interface g0/1
 ip nat outside
```

### Enable PAT

```cisco
ip nat inside source list 1 interface g0/1 overload
```

<img src="images/configure-nat-pat.jpg" alt="Configure PAT NAT overload" />

### Command Breakdown

| Command Part | Meaning |
|-------------|---------|
| `ip nat inside source` | Translate inside source addresses |
| `list 1` | Use ACL 1 to identify inside addresses |
| `interface g0/1` | Use R1's outside interface IP address |
| `overload` | Allow many private devices to share one public IP |

---

## Configure Routing

### Default Route on R1

```cisco
ip route 0.0.0.0 0.0.0.0 203.0.113.2
```

This sends unknown traffic from R1 toward the ISP router.

<img src="images/default-route-r1.jpg" alt="R1 default route" />

### Return Route on ISP

```cisco
ip route 192.168.1.0 255.255.255.0 203.0.113.1
```

This tells the ISP router how to return traffic to the inside LAN.

<img src="images/return-route-isp.jpg" alt="ISP return route" />

---

## Verification After NAT

### Successful PC0 Ping to ISP

```text
ping 203.0.113.2
```

Result:

```text
Packets: Sent = 4, Received = 4, Lost = 0
```

<img src="images/successful-ping-after-nat.jpg" alt="Successful ping after NAT" />

---

## Verify NAT Translations

On R1:

```cisco
show ip nat translation
```

Observed result:

```text
Pro   Inside global     Inside local      Outside local     Outside global
icmp  203.0.113.1:13    192.168.1.10:13   203.0.113.2:13    203.0.113.2:13
icmp  203.0.113.1:14    192.168.1.10:14   203.0.113.2:14    203.0.113.2:14
icmp  203.0.113.1:15    192.168.1.10:15   203.0.113.2:15    203.0.113.2:15
icmp  203.0.113.1:16    192.168.1.10:16   203.0.113.2:16    203.0.113.2:16
```

<img src="images/nat-translations.jpg" alt="NAT translation table" />

---

## NAT Table Explanation

| Column | Meaning |
|--------|---------|
| Inside Local | The real private IP address of the inside device |
| Inside Global | The translated public-facing IP address |
| Outside Local | How the outside destination appears to the inside network |
| Outside Global | The real outside destination IP address |

In this lab:

- Inside Local: `192.168.1.10`
- Inside Global: `203.0.113.1`
- Outside Global: `203.0.113.2`

This proves R1 translated PC0's private IP address into R1's outside interface address.

---

## Troubleshooting Notes

If NAT does not work, verify:

| Issue | Command |
|------|---------|
| Interfaces are up | `show ip interface brief` |
| NAT translations exist | `show ip nat translation` |
| NAT statistics | `show ip nat statistics` |
| ACL matches inside network | `show access-lists` |
| Default route exists | `show ip route` |
| Inside/outside interfaces are correct | `show running-config` |

---

## Key Commands

| Command | Purpose |
|---------|---------|
| `access-list 1 permit 192.168.1.0 0.0.0.255` | Identifies inside addresses for NAT |
| `ip nat inside` | Marks the LAN-facing interface as inside |
| `ip nat outside` | Marks the ISP-facing interface as outside |
| `ip nat inside source list 1 interface g0/1 overload` | Enables PAT |
| `show ip nat translation` | Displays active NAT translations |
| `show ip nat statistics` | Displays NAT statistics |
| `show ip route` | Verifies routing |

---

## Lessons Learned

- NAT translates private IP addresses into public-facing addresses.
- PAT allows multiple inside devices to share one public IP address.
- The `overload` keyword enables PAT.
- NAT requires inside and outside interfaces.
- ACLs can be used to identify which inside addresses should be translated.
- Routing must still be correct for NAT to work.
- `show ip nat translation` confirms that traffic is being translated.

---

## Interview Takeaways

This lab demonstrates the ability to:

- Configure PAT on a Cisco router.
- Explain inside local and inside global addresses.
- Use ACLs to identify NAT traffic.
- Verify NAT translations.
- Troubleshoot routing and NAT issues.
- Explain how private LAN devices access outside networks.

---

## Recruiter Notes

- Configured Cisco NAT/PAT using IOS CLI.
- Built a simulated WAN edge topology.
- Verified private-to-public IP translation.
- Used Cisco verification commands to validate NAT operation.
- Documented routing, NAT configuration, troubleshooting, and verification steps.
