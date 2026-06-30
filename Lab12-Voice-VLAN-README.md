# Lab 11 -- Voice VLAN Configuration

## Objective

Configure a Cisco switch port to support both a Data VLAN and a Voice
VLAN using a Cisco IP Phone. Verify that PC traffic remains in the Data
VLAN while voice traffic uses a separate Voice VLAN.

## Skills Demonstrated

-   Creating VLANs
-   Naming VLANs
-   Configuring an access port
-   Configuring a Voice VLAN
-   Verifying VLAN configuration
-   Using Packet Tracer Simulation Mode

## Topology

```text

                    VLAN 10 (DATA)

 PC1
Fa0
 |
 |
Fa0/2
+-------------+
|   Switch    |
+-------------+
      |
    Fa0/1
      |
  Switch Port
+----------------------+
| Cisco 7960 IP Phone  |
+----------------------+
      |
    PC Port
      |
     Fa0
      |
     PC0

PC1 (192.168.10.20) → VLAN 10
PC0 (192.168.10.10) → VLAN 10
Cisco 7960 IP Phone → Voice VLAN 20
```

## IP Addressing

  Device   Address            VLAN
  -------- ------------------ ------
  PC0      192.168.10.10/24   10
  PC1      192.168.10.20/24   10

## Configuration

``` cisco
vlan 10
 name DATA

vlan 20
 name VOICE

interface fa0/1
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 20

interface fa0/2
 switchport mode access
 switchport access vlan 10
```

## Verification

``` cisco
show vlan brief
show running-config
```

Expected: - VLAN 10 = DATA - VLAN 20 = VOICE - Fa0/1 carries Data VLAN
10 and Voice VLAN 20.

## Packet Simulation

An ICMP Echo Request was sent from PC0 to PC1.

Observed sequence: 1. PC0 created the ICMP Echo Request. 2. The IP Phone
received the Ethernet frame from the PC. 3. The phone forwarded the
frame to the switch while keeping data and voice logically separated. 4.
The switch learned the source MAC address. 5. The switch forwarded the
frame only within VLAN 10. 6. PC1 replied with an ICMP Echo Reply. 7.
The switch forwarded the reply back to PC0.

## What I Learned

-   Voice VLANs allow one switch port to carry both voice and data
    traffic.
-   Cisco IP Phones behave like small Layer 2 switches.
-   VLANs logically separate traffic while sharing the same physical
    cable.
-   Switches forward frames only inside the appropriate VLAN.
-   Packet Tracer Simulation Mode helps visualize encapsulation, MAC
    learning, and switching behavior.
