# Lab 19 -- Extended ACL (Blocking HTTP to a Payroll Server)

## Objective

Configure an **Extended ACL** to block HTTP (TCP/80) from the Sales
network to the Payroll Server while allowing all other traffic.

------------------------------------------------------------------------

# Real-World Scenario

Sales employees should be able to verify connectivity with ping but must
not access the internal Payroll website. Finance users should retain
full access.

------------------------------------------------------------------------

# Skills Demonstrated

-   Extended ACL configuration
-   Protocol and port filtering
-   ACL placement
-   HTTP services
-   Verification and troubleshooting
-   Hit counter analysis

------------------------------------------------------------------------

# Topology

``` text
Sales-PC (192.168.1.10)
 Fa0
  |
Switch0 Fa0/2
Switch0 Fa0/1
  |
R1 G0/0/0 (192.168.1.1)
R1 G0/0/1 (192.168.2.1)
  |
Switch1 Fa0/1
├── Fa0/2 → Finance-PC (192.168.2.10)
└── Fa0/3 → Payroll-Server (192.168.2.100)
```
<img width="846" height="370" alt="topology" src="https://github.com/user-attachments/assets/8ba33458-e06f-40a0-89b6-fc85c6a03fca" />

------------------------------------------------------------------------

# Configuration

Configure: - R1 interfaces - Sales-PC - Finance-PC - Payroll Server -
Enable HTTP on the Payroll Server

> <img width="1132" height="510" alt="configure R1" src="https://github.com/user-attachments/assets/9529f995-c32f-4a7d-9125-e55c34846fa5" />


> <img width="857" height="322" alt="configure Sales-PC" src="https://github.com/user-attachments/assets/fc8b650a-3d44-4fb8-baaa-fd07ee37165d" />


> <img width="866" height="327" alt="configure Finance-PC" src="https://github.com/user-attachments/assets/c4799ffa-7041-4617-b201-c45ea0bb3006" />


> <img width="887" height="307" alt="configure PayRoll-Server" src="https://github.com/user-attachments/assets/5ad58bc1-d115-48c1-b323-cb2ec9c5526e" />


> <img width="871" height="477" alt="turn HTTP on" src="https://github.com/user-attachments/assets/de5402d1-52eb-430b-a59a-8aa2fc691603" />


------------------------------------------------------------------------

# Verify Baseline Connectivity

From Sales-PC:

``` text
ping 192.168.2.100
http://192.168.2.100
```

Both should work before the ACL.

> <img width="871" height="482" alt="ping from sales-pc" src="https://github.com/user-attachments/assets/2b0ec4b2-aa04-42ef-9a36-165334390e6f" />


> <img width="877" height="402" alt="browser on sales" src="https://github.com/user-attachments/assets/c7ab84e5-ae9d-48a1-b58e-3e7f51672dab" />


Verify Finance-PC can browse to the Payroll Server.

> <img width="880" height="427" alt="browser on finance" src="https://github.com/user-attachments/assets/e143b4e7-7d4f-4daa-8a50-7364773dfbcf" />


------------------------------------------------------------------------

# Create the Extended ACL

``` cisco
access-list 100 deny tcp host 192.168.1.10 host 192.168.2.100 eq www
access-list 100 permit ip any any
```

------------------------------------------------------------------------

# Apply the ACL

``` cisco
interface g0/0/0
 ip access-group 100 in
```

Extended ACLs are placed **close to the source**, so the ACL is applied
inbound on the Sales interface.

> <img width="610" height="197" alt="apply the access list" src="https://github.com/user-attachments/assets/b367247a-8a25-493c-ae93-4dc00f79732c" />


------------------------------------------------------------------------

# Verify Configuration

``` cisco
show access-lists
show ip interface g0/0/0
show running-config
```

> <img width="561" height="97" alt="verify the access list" src="https://github.com/user-attachments/assets/f929382d-1d96-4d12-a624-69dd6e024b66" />

> <img width="570" height="407" alt="verify interface" src="https://github.com/user-attachments/assets/a4a95998-f770-4913-8dc6-9a63b26a7ff0" />


> <img width="607" height="482" alt="running-config" src="https://github.com/user-attachments/assets/c3635103-96c4-48df-8869-41d802357ccd" />


------------------------------------------------------------------------

# Test Results

From Sales-PC:

-   `ping 192.168.2.100` ✅ succeeds
-   `http://192.168.2.100` ❌ Request Timeout

Finance-PC:

-   `http://192.168.2.100` ✅ succeeds

> <img width="627" height="262" alt="test acl" src="https://github.com/user-attachments/assets/0d49ef92-5eac-4195-a0a9-cacdd386b218" />


> <img width="867" height="691" alt="test browser" src="https://github.com/user-attachments/assets/89a3010a-ac6b-4653-81da-f22c92642f44" />


> <img width="881" height="502" alt="test browser on finance pc" src="https://github.com/user-attachments/assets/887c7096-8c75-4c3f-a9f7-a9b7ed7d78c5" />


------------------------------------------------------------------------

# Verify Hit Counters

``` cisco
show access-lists
```

The deny rule should show increasing matches.

> <img width="657" height="107" alt="hit counters" src="https://github.com/user-attachments/assets/e3dd965a-fc65-4d5d-ba48-b2c608c26e72" />

------------------------------------------------------------------------

# Troubleshooting

Commands used:

``` cisco
show access-lists
show ip interface g0/0/0
show running-config
```

Common issues: - Wrong interface - Wrong direction (out instead of in) -
Wrong protocol or port - Missing `permit ip any any` - Incorrect
source/destination IP

------------------------------------------------------------------------

# Lessons Learned

-   Extended ACLs filter by source, destination, protocol, and port.
-   Place Extended ACLs close to the traffic source.
-   `access-list` creates ACL entries; `ip access-group` applies them.
-   Hit counters confirm that ACL entries are matching traffic.

------------------------------------------------------------------------

# Recruiter Notes

-   Configured an Extended ACL to restrict application-specific traffic.
-   Applied Cisco ACL placement best practices.
-   Verified functionality with browser testing, ICMP testing, interface
    verification, running configuration review, and ACL hit counters.
