
# Lab 15 – EtherChannel Troubleshooting

## Objective

Troubleshoot common Layer 2 EtherChannel issues by intentionally introducing configuration errors, identifying the root cause, correcting the configuration, and verifying that connectivity is restored.

---

# Real-World Scenario

After a scheduled maintenance window, users report intermittent connectivity between two access switches. The EtherChannel appears to be configured, but traffic is not flowing correctly. As the network administrator, your task is to investigate the issue, determine the cause, restore service, and document your findings.

---

# Skills Demonstrated

- EtherChannel Troubleshooting
- LACP Negotiation
- Layer 2 Switching
- Port-Channel Configuration
- Trunk Troubleshooting
- Cisco IOS Verification Commands
- Root Cause Analysis
- Enterprise Troubleshooting Methodology

---

# Prerequisites

Complete these labs first:

## Lab 13 – EtherChannel (LACP Configuration)

- Configure an LACP EtherChannel
- Configure Port-channel1
- Configure trunking
- Verify the EtherChannel forms successfully

## Lab 14 – EtherChannel Failover

- Configure PC access ports
- Assign IP addresses
- Verify connectivity
- Simulate a member link failure
- Verify failover

This lab reuses the completed topology from Lab 14.

---

# Topology

```text
             Fa0/1================Fa0/1
           +-----------------------------+
           |                             |
PC0 Fa0---Fa0/3                     Fa0/3---Fa0 PC1
          +--------+===========+--------+
          |  SW1   |           |  SW2   |
          +--------+===========+--------+
             Fa0/2================Fa0/2

             EtherChannel (Po1)
```

| Device | Interface | Address |
|---------|-----------|---------|
| PC0 | Fa0 | 192.168.1.10/24 |
| SW1 | Fa0/3 | Access VLAN 1 |
| SW2 | Fa0/3 | Access VLAN 1 |
| PC1 | Fa0 | 192.168.1.20/24 |

---

# Scenario 1 – Passive/Passive LACP Failure

## Objective

Observe how LACP behaves when neither switch initiates negotiation.

## Configuration

On **both** switches:

```cisco
interface range fa0/1-2
 channel-group 1 mode passive
```

## Verification

### Commands Used

```cisco
show etherchannel summary
show interfaces trunk
show running-config
```

### Expected Results

```text
Po1(SD)

Fa0/1(I)
Fa0/2(I)
```

Meaning:

- S = Layer 2 EtherChannel
- D = Down
- I = Individual Interface

## Troubleshooting

Observation:
- EtherChannel is down.
- Interfaces are operating individually.

Root Cause:
- Both switches are waiting for LACP negotiation.
- Neither side transmits LACP packets.

Resolution:

```cisco
interface range fa0/1-2
 channel-group 1 mode active
```

Verification After Fix:

```text
Po1(SU)

Fa0/1(P)
Fa0/2(P)
```

---

# Scenario 2 – Port-Channel Configuration Mismatch

## Objective

Demonstrate that LACP can succeed even when the logical Port-channel configuration is incorrect.

## Configuration

### SW1

```cisco
interface port-channel1
 switchport mode access
 switchport access vlan 1
```

### SW2

```cisco
interface port-channel1
 switchport mode trunk
```

## Verification

### Commands Used

```cisco
show etherchannel summary
show interfaces trunk
show running-config
```

### Verification Results

SW1:
- Po1(SU)
- No trunk interfaces
- Port-channel configured as an access port

SW2:
- Po1(SU)
- Po1 trunking
- Port-channel configured as a trunk

## Troubleshooting

Observation:
- EtherChannel formed successfully.
- Trunk configuration differs between switches.

Root Cause:
- LACP verifies only physical link compatibility.
- It does not verify the Layer 2 switching configuration.

### Traffic Impact

**Scenario A – VLAN 1**

- Untagged traffic is treated as the native VLAN.
- ICMP ping may still succeed.

**Scenario B – VLAN 10 or Higher**

- SW1 sends untagged frames.
- SW2 expects tagged VLAN traffic.
- Frames are placed into the native VLAN.

Result:

- ICMP fails.
- VLAN communication fails.

Resolution:

Configure both Port-channel interfaces identically.

```cisco
interface port-channel1
 switchport mode trunk
```

Verify:

- Matching Port-channel configuration
- Matching trunk mode
- Successful connectivity

---

# Lessons Learned

- Active mode initiates LACP negotiation.
- Passive mode waits for LACP packets.
- Two Passive interfaces never form an EtherChannel.
- LACP validates bundled links, not Layer 2 switching configuration.
- EtherChannel can be operational while user traffic still fails.
- Multiple verification commands are required to isolate the root cause.

---

# Recruiter Notes

- Configured and troubleshot Cisco Layer 2 EtherChannels.
- Diagnosed LACP negotiation failures.
- Identified Port-channel configuration mismatches.
- Used Cisco IOS verification commands to isolate and resolve issues.
- Documented troubleshooting methodology similar to enterprise change documentation.
