# Lab 18 – Standard ACL Traffic Filtering

## Objective

Configure a **Standard Access Control List (ACL)** to block traffic from the Sales workstation to the Finance network while verifying that the ACL was created, applied to the correct interface, and actively filtering traffic.

---

## Real-World Scenario

The Finance department contains sensitive systems. Company policy states that the Sales workstation should not be able to access the Finance network.

As the network administrator, the task is to create a Standard ACL, apply it close to the destination, verify the configuration, and confirm that the ACL blocks the intended traffic.

---

## Skills Demonstrated

- Standard ACL configuration
- ACL placement
- Interface direction selection
- Cisco IOS verification commands
- ACL hit counter verification
- Basic Layer 3 troubleshooting
- Enterprise-style documentation

---

## Technologies Used

- Cisco Packet Tracer
- Cisco ISR4331 Router
- Cisco Catalyst 2960 Switches
- IPv4
- Standard ACLs
- ICMP

---

## Topology

```text
Sales Network                                         Finance Network
192.168.1.0/24                                       192.168.2.0/24

Sales-PC Fa0 -- Fa0/2 Sales-SW Fa0/1 -- G0/0/0 R1 G0/0/1 -- Fa0/1 Finance-SW Fa0/2 -- Fa0 Finance-PC
```

<img width="870" height="237" alt="topology" src="https://github.com/user-attachments/assets/357e18de-a487-43f4-8223-74b5c1bdbd81" />


---

## IP Addressing

| Device | Interface | IP Address | Default Gateway |
|--------|-----------|------------|-----------------|
| Sales-PC | Fa0 | 192.168.1.10/24 | 192.168.1.1 |
| R1 | G0/0/0 | 192.168.1.1/24 | N/A |
| R1 | G0/0/1 | 192.168.2.1/24 | N/A |
| Finance-PC | Fa0 | 192.168.2.10/24 | 192.168.2.1 |

---

## Step 1 – Configure R1

```cisco
enable
configure terminal
hostname R1

interface g0/0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown

interface g0/0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown

end
write
```

<img width="842" height="522" alt="r1 configuration" src="https://github.com/user-attachments/assets/ba8a3045-1715-45a5-a4c6-b9aa414189d2" />


---

## Step 2 – Configure the PCs

### Sales-PC

| Setting | Value |
|---------|-------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |

<img width="862" height="337" alt="sales configuration" src="https://github.com/user-attachments/assets/3f538674-f39c-464d-b1ce-2cb160b168b0" />


### Finance-PC

| Setting | Value |
|---------|-------|
| IP Address | 192.168.2.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.2.1 |

<img width="880" height="332" alt="Finance configuration" src="https://github.com/user-attachments/assets/11a34983-6ada-4d34-a263-f17407201e33" />


---

## Step 3 – Verify Connectivity Before the ACL

Before applying the ACL, verify that Sales-PC can reach Finance-PC.

From Sales-PC:

```text
ping 192.168.2.10
```

Expected result:

```text
Reply from 192.168.2.10
```

<img width="852" height="446" alt="verify connectivity" src="https://github.com/user-attachments/assets/110795ee-de63-4d53-ada6-39d626f8cf2f" />


This confirms routing and addressing are working before traffic filtering is introduced.

---

## Step 4 – Create the Standard ACL

Business requirement:

> Block Sales-PC from reaching the Finance network.

Create ACL 10:

```cisco
access-list 10 deny host 192.168.1.10
access-list 10 permit any
```

<img width="562" height="107" alt="create access-list" src="https://github.com/user-attachments/assets/7a517989-6855-4919-81d0-74219a121818" />


### Why `permit any` Is Required

Every ACL ends with an invisible **implicit deny**.

Without this line:

```cisco
access-list 10 permit any
```

all other traffic would also be denied.

---

## Step 5 – Apply the ACL Close to the Destination

Standard ACLs filter only by **source IP address**, so they are placed as close to the destination as possible.

In this lab, the protected destination is the Finance network.

The R1 interface facing the Finance network is:

```text
G0/0/1
```

Apply ACL 10 outbound:

```cisco
interface g0/0/1
 ip access-group 10 out
```
<img width="337" height="71" alt="apply access list" src="https://github.com/user-attachments/assets/b6da969b-9a28-4c9a-bb60-7874f8b35191" />


### Why This Interface and Direction Were Used

Packet flow:

```text
Sales-PC
   |
   v
R1 G0/0/0
   |
   v
R1 G0/0/1  <- ACL checks traffic outbound here
   |
   v
Finance-PC
```

The ACL is applied **outbound** because traffic leaves R1 through G0/0/1 on the way to the Finance network.

---

## Step 6 – Verify the ACL Exists

```cisco
show access-lists
```

Expected result:

```text
Standard IP access list 10
    10 deny host 192.168.1.10
    20 permit any
```
<img width="332" height="87" alt="verify access list exist" src="https://github.com/user-attachments/assets/9ec49c36-029c-4181-ae14-7cbec9fbcb9f" />


This confirms the ACL was created, but it does not prove that the ACL is applied to an interface.

---

## Step 7 – Verify Where the ACL Is Applied

```cisco
show ip interface g0/0/1
```

Look for:

```text
Outgoing access list is 10
```

<img width="530" height="582" alt="verify where access list is applied" src="https://github.com/user-attachments/assets/52329914-d6cb-4668-87bd-eeaeae48ae6b" />


This confirms ACL 10 is applied outbound on the Finance-facing interface.

---

## Step 8 – Verify the Running Configuration

```cisco
show running-config
```

Confirm:

```cisco
interface GigabitEthernet0/0/1
 ip access-group 10 out

access-list 10 deny host 192.168.1.10
access-list 10 permit any
```

<img width="637" height="495" alt="show running-config" src="https://github.com/user-attachments/assets/404c78dc-6b8c-420b-9a79-6d3972b91757" />


---

## Step 9 – Test the ACL

From Sales-PC:

```text
ping 192.168.2.10
```

Expected result:

```text
Destination host unreachable
```
<img width="542" height="260" alt="test acl" src="https://github.com/user-attachments/assets/ec799eb6-3e66-4dfb-be2c-1728a13e5008" />


The ping fails because the ACL denies traffic from Sales-PC before it reaches the Finance network.

---

## Step 10 – Verify ACL Hit Counters

Run:

```cisco
show access-lists
```

Observed result:

```text
Standard IP access list 10
    10 deny host 192.168.1.10 (4 match(es))
    20 permit any
```

<img width="432" height="122" alt="hit count" src="https://github.com/user-attachments/assets/6f7f4b01-1ceb-4f1d-af27-175f2583f1b2" />


### What the Hit Count Means

The match counter proves that packets from Sales-PC reached the ACL and matched the deny statement.

This confirms:

- The ACL exists.
- The ACL is applied to the correct interface.
- The ACL is applied in the correct direction.
- The ACL is actively blocking the intended traffic.

---

## Verification Workflow

| Step | Command | Purpose |
|------|---------|---------|
| 1 | `show access-lists` | Verify ACL rules exist |
| 2 | `show ip interface g0/0/1` | Verify ACL interface and direction |
| 3 | `show running-config` | Validate full configuration |
| 4 | `ping 192.168.2.10` | Test blocked traffic |
| 5 | `show access-lists` | Verify hit counters increased |

---

## Troubleshooting Notes

If the ACL does not work as expected, check:

| Issue | What to Verify |
|------|----------------|
| ACL not blocking traffic | Wrong interface or direction |
| Everyone is blocked | Missing `permit any` |
| ACL exists but does nothing | ACL was created but not applied |
| Hit count stays at 0 | Traffic is not reaching the ACL |
| Wrong host blocked | Incorrect source IP in ACL |

---

## Key Commands

| Command | Purpose |
|---------|---------|
| `access-list 10 deny host 192.168.1.10` | Blocks Sales-PC |
| `access-list 10 permit any` | Allows all other traffic |
| `ip access-group 10 out` | Applies ACL to an interface |
| `show access-lists` | Verifies ACL rules and hit counters |
| `show ip interface g0/0/1` | Shows ACL direction on interface |
| `show running-config` | Displays full configuration |

---

## Lessons Learned

- Standard ACLs filter based on source IP address only.
- Standard ACLs should be placed close to the destination.
- Creating an ACL does not affect traffic until it is applied to an interface.
- The `ip access-group` command applies the ACL to an interface.
- The implicit deny blocks traffic that does not match a permit statement.
- ACL hit counters confirm whether traffic is matching the ACL.

---

## Interview Takeaways

Be prepared to explain:

- Difference between `access-list` and `ip access-group`.
- Why Standard ACLs are placed close to the destination.
- What the implicit deny does.
- Why `permit any` is needed after a deny statement.
- How to verify whether an ACL is applied and working.
- How ACL hit counters help troubleshoot traffic filtering.

---

## Recruiter Notes

- Configured a Standard ACL to enforce a department-based access restriction.
- Applied the ACL close to the protected Finance network.
- Verified ACL configuration, interface placement, direction, and hit counters.
- Demonstrated structured troubleshooting using Cisco IOS verification commands.
- Documented the lab using an enterprise-style workflow.
