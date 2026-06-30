# Lab 13 -- EtherChannel (LACP)

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
<img width="750" height="727" alt="Configure Switch 1" src="https://github.com/user-attachments/assets/8502314d-899e-410d-8290-7f762b2dc933" />


**Reasoning** - Active mode starts LACP negotiation. - Creates
Port-Channel1. - Configures the logical interface as a trunk.

## Switch 2

``` cisco
interface range fa0/1-2
 channel-group 1 mode passive

interface port-channel1
 switchport mode trunk
```
<img width="797" height="545" alt="Configure Switch 2" src="https://github.com/user-attachments/assets/3484b9da-c6d2-44c4-ba7d-d4a73227d48c" />

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
## Switch1
<img width="655" height="311" alt="etherchannel summary" src="https://github.com/user-attachments/assets/9fd767b4-56e4-4413-804e-2862d7f3a3b6" />
<img width="645" height="455" alt="show interface port-channel 1" src="https://github.com/user-attachments/assets/d8088cc9-0897-45a9-9575-bca9b960fe74" />
<img width="760" height="477" alt="spanning tree" src="https://github.com/user-attachments/assets/b5955b31-79a7-4e9d-8d2f-46f527b81ba4" />
## Switch2
<img width="671" height="309" alt="etherchannel summary(2)" src="https://github.com/user-attachments/assets/1edde30f-fe54-4e3a-8235-b5ee919f12de" />
<img width="680" height="467" alt="show interface port-channel (2)" src="https://github.com/user-attachments/assets/24db0b17-412a-437f-8e11-93c8f7965b58" />
<img width="735" height="491" alt="verify(2)" src="https://github.com/user-attachments/assets/7229412f-128e-47eb-b82d-c4a2043b9a4c" />


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
