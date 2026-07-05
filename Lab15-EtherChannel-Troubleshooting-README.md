
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
<img width="637" height="277" alt="etherchannel fail" src="https://github.com/user-attachments/assets/f4c00b24-38b8-4223-bd98-528f03b37809" />
<img width="797" height="545" alt="Configure Switch 2" src="https://github.com/user-attachments/assets/e2cb8340-64e6-4c7b-ac34-10aa9525268a" />


## Verification

### Commands Used

```cisco
show etherchannel summary
show interfaces trunk
show running-config
```
<img width="607" height="282" alt="verify fail1" src="https://github.com/user-attachments/assets/66240d3c-fef2-4f57-85fb-3d4b7bfb09ab" />
<img width="480" height="311" alt="verify fail2" src="https://github.com/user-attachments/assets/ec68e36f-4d2b-4e73-942e-59cdb3d6f9b1" />

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
<img width="636" height="242" alt="fix etherchannel" src="https://github.com/user-attachments/assets/0e2fb684-865b-475a-a645-ff84a9016555" />

Verification After Fix:

```text
Po1(SU)

Fa0/1(P)
Fa0/2(P)
```
<img width="532" height="282" alt="verify fix" src="https://github.com/user-attachments/assets/987cbc04-44f1-4af3-9207-828c8cc85322" />

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
<img width="590" height="122" alt="create issue #2" src="https://github.com/user-attachments/assets/95ea9a5c-f9ff-478c-82c5-50c8a370f222" />

### SW2

```cisco
interface port-channel1
 switchport mode trunk
```
<img width="797" height="545" alt="Configure Switch 2" src="https://github.com/user-attachments/assets/218e4cd6-f79a-4e74-8568-0c2262d082a1" />

## Verification

### Commands Used

```cisco
show etherchannel summary
show interfaces trunk
show running-config
```
<img width="515" height="292" alt="verify issue #2" src="https://github.com/user-attachments/assets/aa21d4c7-73b4-4f1e-801b-7a6f68c2e07b" />
<img width="422" height="72" alt="verify issue #2(trunk)" src="https://github.com/user-attachments/assets/aa80e940-b214-47ca-86f4-39571d46a4c5" />
<img width="445" height="467" alt="verify issue #2(running-config)" src="https://github.com/user-attachments/assets/0e7430a1-344d-48e1-991d-67b037e7f595" />
<img width="521" height="277" alt="verify issue #2(etherchannel)" src="https://github.com/user-attachments/assets/1ce00c38-2a48-4467-939f-fd795eeb0591" />
<img width="542" height="207" alt="verify issue #2(trunk sw2)" src="https://github.com/user-attachments/assets/f5392010-80c3-43b3-ba34-37346b735875" />

<img width="452" height="455" alt="verify issue #2(running-config2)" src="https://github.com/user-attachments/assets/c8e906a8-d0f9-4731-9b49-8fb7db538908" />




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
<img width="492" height="87" alt="issue #2 fix" src="https://github.com/user-attachments/assets/a90a7d72-7b70-4683-bcab-dafce8caba8b" />

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
