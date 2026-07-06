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

<img width="821" height="617" alt="topology" src="https://github.com/user-attachments/assets/31f423d2-b29d-497b-9184-6988e062f217" />


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

<img width="865" height="325" alt="configure pc0" src="https://github.com/user-attachments/assets/f0181130-c4fc-4a3a-9788-24fe0fdc15bd" />

### PC1

| Setting | Value |
|---------|-------|
| IP Address | 192.168.1.20 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |

<img width="862" height="327" alt="configure pc1" src="https://github.com/user-attachments/assets/7d6086b6-13de-4563-a7ae-19ab57aa1ca5" />

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

<img width="780" height="347" alt="configure R1" src="https://github.com/user-attachments/assets/ea9b0757-0655-4e80-a278-bc394c82f059" />


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

<img width="837" height="262" alt="configure ISP" src="https://github.com/user-attachments/assets/2bc7c8bb-4ee3-4f2d-a36d-e660ae080c3d" />


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

<img width="521" height="437" alt="Test basic connectivity R0" src="https://github.com/user-attachments/assets/3f59be47-7642-416f-96fc-e340f52ed256" />

### R1 to ISP

```cisco
ping 203.0.113.2
```

Result: Successful

<img width="617" height="147" alt="Test basic connectivity R1" src="https://github.com/user-attachments/assets/8ce00f57-12b8-4599-a54a-7b23de5dff2a" />


---

## Configure the NAT ACL

The ACL identifies which inside addresses are allowed to be translated.

```cisco
access-list 1 permit 192.168.1.0 0.0.0.255
```

<img width="616" height="127" alt="ACL" src="https://github.com/user-attachments/assets/f99cb55b-41ea-446a-b0ad-2696b7ab03ef" />


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

<img width="706" height="292" alt="configure nat" src="https://github.com/user-attachments/assets/8f9e0a75-f448-4597-8814-f0668ccd27d1" />


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

<img width="420" height="37" alt="default route" src="https://github.com/user-attachments/assets/b1c5bc5b-1c94-4f99-a433-323e89d7d637" />


### Return Route on ISP

```cisco
ip route 192.168.1.0 255.255.255.0 203.0.113.1
```

This tells the ISP router how to return traffic to the inside LAN.

<img width="517" height="42" alt="return route" src="https://github.com/user-attachments/assets/f6c51968-aa9a-4491-8c4c-59068635bd8c" />


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

<img width="592" height="275" alt="verification 1" src="https://github.com/user-attachments/assets/f14fa8bc-1587-4029-b9ab-c639d94f57a9" />

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

<img width="687" height="167" alt="verification 2" src="https://github.com/user-attachments/assets/6ed9189c-6d49-42e4-b981-eceb1d1c7066" />


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
