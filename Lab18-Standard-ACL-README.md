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

<img src="images/topology.jpg" alt="Lab 18 Standard ACL topology" />

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

<img src="images/r1-configuration.jpg" alt="R1 interface configuration" />

---

## Step 2 – Configure the PCs

### Sales-PC

| Setting | Value |
|---------|-------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |

<img src="images/sales-pc-configuration.jpg" alt="Sales PC configuration" />

### Finance-PC

| Setting | Value |
|---------|-------|
| IP Address | 192.168.2.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.2.1 |

<img src="images/finance-pc-configuration.jpg" alt="Finance PC configuration" />

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

<img src="images/verify-connectivity-before-acl.jpg" alt="Successful ping before ACL" />

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

<img src="images/create-acl.jpg" alt="Create standard ACL" />

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

<img src="images/apply-acl.jpg" alt="Apply ACL to interface" />

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

<img src="images/verify-acl-exists.jpg" alt="Verify ACL exists" />

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

<img src="images/verify-acl-interface.jpg" alt="Verify ACL applied to interface" />

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

<img src="images/show-running-config.jpg" alt="Show running configuration with ACL" />

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

<img src="images/test-acl-blocked.jpg" alt="ACL blocking ping from Sales to Finance" />

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

<img src="images/acl-hit-count.jpg" alt="ACL hit counter verification" />

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
