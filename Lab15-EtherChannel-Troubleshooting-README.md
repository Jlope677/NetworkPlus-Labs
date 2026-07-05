# Lab 15 – EtherChannel Troubleshooting

## Objective

Troubleshoot common Layer 2 EtherChannel issues by intentionally introducing configuration errors, identifying the root cause, correcting the configuration, and verifying that connectivity is restored.

---

## Real-World Scenario

After a scheduled maintenance window, users report intermittent connectivity between two access switches. Although the EtherChannel appears to be configured, network traffic is not flowing correctly.

As the network administrator, your task is to investigate the issue, determine the cause, restore service, and document your findings.

---

## Skills Demonstrated

- EtherChannel Troubleshooting
- LACP Negotiation
- Layer 2 Switching
- Port-Channel Configuration
- Trunk Troubleshooting
- Cisco IOS Verification Commands
- Root Cause Analysis
- Enterprise Troubleshooting Methodology

---

## Technologies Used

- Cisco Packet Tracer
- Cisco Catalyst 2960 Switches
- Cisco IOS CLI
- IEEE 802.3ad (LACP)
- IEEE 802.1Q Trunking
- Ethernet

---

## Prerequisites

### Lab 13 – EtherChannel (LACP Configuration)

- Configure an LACP EtherChannel
- Configure Port-channel1
- Configure trunking
- Verify the EtherChannel forms successfully

### Lab 14 – EtherChannel Failover

- Configure PC access ports
- Configure PC IP addresses
- Verify end-to-end connectivity
- Simulate a member link failure
- Verify EtherChannel redundancy

This lab reuses the completed topology from Lab 14.

---

## Lab Workflow

1. Start with the working EtherChannel topology.
2. Create a Passive/Passive LACP negotiation failure.
3. Verify the failure using Cisco IOS commands.
4. Restore the EtherChannel.
5. Create a Port-channel Access/Trunk mismatch.
6. Verify the configuration mismatch.
7. Restore the correct configuration.
8. Verify end-to-end connectivity.

---

## Topology

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

| Device | Interface | Configuration |
|---------|-----------|---------------|
| PC0 | Fa0 | 192.168.1.10/24 |
| SW1 | Fa0/3 | Access VLAN 1 |
| SW2 | Fa0/3 | Access VLAN 1 |
| PC1 | Fa0 | 192.168.1.20/24 |

---

## Scenario 1 – Passive / Passive LACP Failure

### Objective

Observe what happens when neither switch initiates LACP negotiation.

### Configuration

On both switches:

```cisco
interface range fa0/1-2
 channel-group 1 mode passive
```

> <img width="637" height="277" alt="etherchannel fail" src="https://github.com/user-attachments/assets/9121f926-6aff-49a7-acec-6c463944c59f" />
> <img width="797" height="545" alt="Configure Switch 2" src="https://github.com/user-attachments/assets/02cd907f-dc16-41da-b36f-1b2bce9d0c7d" />



### Verification

```cisco
show etherchannel summary
show interfaces trunk
show running-config
```

> <img width="607" height="282" alt="verify fail1" src="https://github.com/user-attachments/assets/0d6a3d36-43c8-4ff3-8586-f02ae3485165" />
> <img width="480" height="311" alt="verify fail2" src="https://github.com/user-attachments/assets/59a4b06e-12a9-4295-a379-97b3a5d9198c" />



### Observed Results

```text
Po1(SD)

Fa0/1(I)
Fa0/2(I)
```

| Symbol | Meaning |
|---------|---------|
| S | Layer 2 EtherChannel |
| D | Down |
| I | Individual Interface |

### Troubleshooting

**Observation**

- EtherChannel failed to form.
- Interfaces operated individually.

> **Root Cause**
>
> Both switches were configured as Passive. Passive mode waits for LACP packets and never initiates negotiation, so the EtherChannel never formed.

### Resolution

Configure one switch as Active.

```cisco
interface range fa0/1-2
 channel-group 1 mode active
```

>  <img width="636" height="242" alt="fix etherchannel" src="https://github.com/user-attachments/assets/f6b6a130-a6ec-44b6-a17f-0bfffd5e9c2e" />



### Verification After Fix

```text
Po1(SU)

Fa0/1(P)
Fa0/2(P)
```

> <img width="532" height="282" alt="verify fix" src="https://github.com/user-attachments/assets/b1782593-0afb-4d9d-ba9e-5f78d8487d08" />


---

## Scenario 2 – Port-channel Configuration Mismatch

### Objective

Demonstrate that LACP can successfully form an EtherChannel while the Port-channel configuration is incorrect.

### Configuration

**SW1**

```cisco
interface port-channel1
 switchport mode access
 switchport access vlan 1
```

**SW2**

```cisco
interface port-channel1
 switchport mode trunk
```

> <img width="590" height="122" alt="create issue #2" src="https://github.com/user-attachments/assets/65a300f0-ed55-4026-92ce-deb1fd0167e7" />
> <img width="797" height="545" alt="Configure Switch 2" src="https://github.com/user-attachments/assets/82e2fc32-7547-47e0-842d-8e4d9f5223c2" />



### SW1 Verification

#### show etherchannel summary

<img width="515" height="292" alt="verify issue #2" src="https://github.com/user-attachments/assets/bff49db3-ff87-44a4-bbb0-0efa43853ab1" />


#### show interfaces trunk

<img width="422" height="72" alt="verify issue #2(trunk)" src="https://github.com/user-attachments/assets/776d80d7-9483-4eec-950b-2c430093823f" />


#### show running-config
<img width="445" height="467" alt="verify issue #2(running-config)" src="https://github.com/user-attachments/assets/8b67ebc9-2781-461b-ba38-699d80e01a4f" />



---

### SW2 Verification

#### show etherchannel summary

<img width="521" height="277" alt="verify issue #2(etherchannel)" src="https://github.com/user-attachments/assets/99935641-211b-400b-95c5-6a577b6e4c9b" />


#### show interfaces trunk
<img width="542" height="207" alt="verify issue #2(trunk sw2)" src="https://github.com/user-attachments/assets/f9c8e194-5c43-44b9-ad27-9be253e38b9d" />


#### show running-config

<img width="452" height="455" alt="verify issue #2(running-config2)" src="https://github.com/user-attachments/assets/820a4cdf-995a-4d42-bcf1-52f11dfc27e1" />






| Verification | SW1 | SW2 |
|--------------|-----|-----|
| EtherChannel | Po1(SU) | Po1(SU) |
| Trunk Status | No trunk | Po1 trunking |
| Port-channel Mode | Access | Trunk |

### Troubleshooting

**Observation**

- EtherChannel formed successfully.
- Trunk configuration differed between switches.

> **Root Cause**
>
> LACP verifies physical compatibility only. It does not verify the Layer 2 configuration of the Port-channel.

### Traffic Impact

#### Scenario A – VLAN 1

- Untagged traffic is treated as Native VLAN 1.
- ICMP may still succeed.

#### Scenario B – VLAN 10+

- SW1 sends untagged frames.
- SW2 expects tagged frames.
- Communication fails.

### Resolution

```cisco
interface port-channel1
 switchport mode trunk
```

> <img width="492" height="87" alt="issue #2 fix" src="https://github.com/user-attachments/assets/bf5c52bd-ba64-410c-a70d-278452d9bec6" />


### Verification After Fix

- Matching Port-channel configuration
- Matching trunk mode
- Successful connectivity
- Successful ICMP communication

---

## Key Commands

| Command | Purpose |
|----------|---------|
| `show etherchannel summary` | Verify EtherChannel status |
| `show interfaces trunk` | Verify trunk operation |
| `show running-config` | Verify interface configuration |
| `show spanning-tree` | Verify STP operation |

---

## Lessons Learned

- Active initiates LACP.
- Passive waits for LACP.
- Two Passive interfaces never form an EtherChannel.
- LACP validates bundled links but not Layer 2 configuration.
- EtherChannel can be operational while user traffic still fails.
- Always verify both the EtherChannel and the Port-channel configuration.

---

## Interview Takeaways

- Troubleshot Layer 2 EtherChannel failures.
- Diagnosed LACP negotiation failures.
- Identified Port-channel configuration mismatches.
- Restored network connectivity using structured troubleshooting.

---

## Recruiter Notes

- Configured and troubleshot Cisco Layer 2 EtherChannels.
- Diagnosed Passive/Passive LACP failures.
- Identified Access/Trunk Port-channel mismatches.
- Used Cisco IOS verification commands to isolate and resolve issues.
- Documented the troubleshooting process using an enterprise-style workflow.
