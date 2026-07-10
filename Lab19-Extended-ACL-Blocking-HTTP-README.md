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

------------------------------------------------------------------------

# Configuration

Configure: - R1 interfaces - Sales-PC - Finance-PC - Payroll Server -
Enable HTTP on the Payroll Server

> Insert **configure R1(5).jpg**

> Insert **configure Sales-PC.jpg**

> Insert **configure Finance-PC.jpg**

> Insert **configure PayRoll-Server.jpg**

> Insert **turn HTTP on.jpg**

------------------------------------------------------------------------

# Verify Baseline Connectivity

From Sales-PC:

``` text
ping 192.168.2.100
http://192.168.2.100
```

Both should work before the ACL.

> Insert **ping from sales-pc.jpg**

> Insert **browser on sales.jpg**

Verify Finance-PC can browse to the Payroll Server.

> Insert **browser on finance.jpg**

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

> Insert **apply the access list.jpg**

------------------------------------------------------------------------

# Verify Configuration

``` cisco
show access-lists
show ip interface g0/0/0
show running-config
```

> Insert **verify the access list.jpg**

> Insert **verify interface.jpg**

> Insert **running-config.jpg**

------------------------------------------------------------------------

# Test Results

From Sales-PC:

-   `ping 192.168.2.100` ✅ succeeds
-   `http://192.168.2.100` ❌ Request Timeout

Finance-PC:

-   `http://192.168.2.100` ✅ succeeds

> Insert **test acl(1).jpg**

> Insert **test browser.jpg**

> Insert **test browser on finance pc.jpg**

------------------------------------------------------------------------

# Verify Hit Counters

``` cisco
show access-lists
```

The deny rule should show increasing matches.

> Insert **hit counters.jpg**

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
