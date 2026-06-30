# Lab 12 -- EtherChannel (LACP)

## Objective

Configure an EtherChannel using **LACP** between two Cisco Catalyst 2960
switches. Combine two physical links into a single logical Port-Channel
to increase bandwidth and provide redundancy.

## Topology

``` text
                  Port-Channel 1

        Fa0/1 ================= Fa0/1
        Fa0/2 ================= Fa0/2

+--------------------+      +--------------------+
|     Switch1        |      |     Switch2        |
| Fa0/1              |------| Fa0/1             |
| Fa0/2              |------| Fa0/2             |
+--------------------+      +--------------------+
```

## Switch 1

``` cisco
interface range fa0/1-2
 channel-group 1 mode active

interface port-channel1
 switchport mode trunk
```

**Reasoning** - Active mode starts LACP negotiation. - Creates
Port-Channel1. - Configures the logical interface as a trunk.

## Switch 2

``` cisco
interface range fa0/1-2
 channel-group 1 mode passive

interface port-channel1
 switchport mode trunk
```

**Reasoning** - Passive mode waits for LACP packets. - Joins the
EtherChannel after negotiation.

## Verification

### show etherchannel summary

Verify: - Po1(SU) - Fa0/1(P) - Fa0/2(P)

### show interface port-channel 1

Verify Port-channel is Up/Up with both interfaces as members.

### show spanning-tree

Verify STP references Port-channel1 instead of individual interfaces.

### show interfaces trunk

Verify Po1 is trunking.

## What I Learned

-   EtherChannel combines multiple links into one logical interface.
-   LACP is the IEEE standard.
-   Active initiates negotiation; Passive responds.
-   STP treats the bundle as a single link.
-   EtherChannel improves bandwidth and redundancy.

## Recruiter Notes

-   Configured an LACP EtherChannel.
-   Verified with:
    -   show etherchannel summary
    -   show interface port-channel 1
    -   show spanning-tree
    -   show interfaces trunk
