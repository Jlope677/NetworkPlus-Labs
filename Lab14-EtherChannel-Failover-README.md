# Lab 14 -- EtherChannel Failover & Troubleshooting

## Objective

Demonstrate EtherChannel redundancy by shutting down one physical member
link while maintaining connectivity over the remaining bundled link.

## Topology

``` text
                  Port-Channel 1

        Fa0/1 ================= Fa0/1
        Fa0/2 ================= Fa0/2

     +---------+              +---------+
     |  SW1    |              |  SW2    |
     +---------+              +---------+
        | Fa0/3                 | Fa0/3
       PC0                     PC1
```

> EtherChannel configuration was completed in Lab 13.


## Configure the PC Access Ports

Configure the ports connected to the PCs as access ports and place them in VLAN 1.

### SW1

```cisco
conf t
interface fa0/3
 switchport mode access
 switchport access vlan 1
exit
```
<img width="556" height="132" alt="Configure the PC Ports (sw1)" src="https://github.com/user-attachments/assets/55a7d200-5245-4797-aba0-04e36fa31038" />

### SW2

```cisco
conf t
interface fa0/3
 switchport mode access
 switchport access vlan 1
exit
```

<img width="781" height="136" alt="Configure the PC Ports (sw2)" src="https://github.com/user-attachments/assets/4d1985f5-0d39-426a-b84b-499fdd5e2474" />

**Verification**

```cisco
show vlan brief
```

**Expected Result**

- Fa0/3 appears in VLAN 1 on both switches.
- Both PCs are connected to access ports in the same VLAN.

---

## Configure the PC IP Addresses

Assign both PCs IP addresses in the same subnet.

### PC0

| Setting | Value |
|---------|-------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | Not Required |

<img width="881" height="322" alt="pc0" src="https://github.com/user-attachments/assets/2e5a4eb2-f23a-43dd-a8ba-fa02eaaf6929" />

### PC1

| Setting | Value |
|---------|-------|
| IP Address | 192.168.1.20 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | Not Required |

<img width="862" height="322" alt="pc1" src="https://github.com/user-attachments/assets/b46244ff-8736-4171-a49b-13fba059c59e" />

**Why no default gateway?**

This is a Layer 2 switching lab. Both PCs are in VLAN 1 and communicate through the switches using the EtherChannel link. Because traffic never leaves the local subnet, a default gateway is not required.


## Verification Before Failure

Before simulating a link failure, verify that the EtherChannel is operating correctly.

### Commands Used

```cisco
show etherchannel summary
show interface port-channel 1
show interfaces trunk
show spanning-tree
```

### Verification Results

- ✅ Port-Channel 1 (Po1) is operational.
- ✅ EtherChannel is configured as a Layer 2 bundle.
- ✅ Both Fa0/1 and Fa0/2 are active members of the EtherChannel.
- ✅ Port-channel interface is **Up/Up**.
- ✅ Port-Channel1 is operating as an 802.1Q trunk.
- ✅ Spanning Tree recognizes the Port-Channel as a single logical interface.

---

### Switch 1 Verification

#### show etherchannel summary

<img width="655" height="311" alt="etherchannel summary" src="https://github.com/user-attachments/assets/6fd94f1a-10bc-4f59-8f5e-20c9d6f0d12f" />

#### show interface port-channel 1

<img width="645" height="455" alt="show interface port-channel 1" src="https://github.com/user-attachments/assets/66a0c356-700a-4bd8-9d4b-cc4f66dd7e38" />

#### show spanning-tree

<img width="760" height="477" alt="spanning tree" src="https://github.com/user-attachments/assets/8f0629fc-4fe0-481d-b2b0-055372cb8c5c" />

---

### Switch 2 Verification

#### show etherchannel summary

<img width="671" height="309" alt="etherchannel summary(2)" src="https://github.com/user-attachments/assets/f88a70c3-6003-4c90-b378-c34bb3ec1be6" />

#### show interface port-channel 1

<img width="680" height="467" alt="show interface port-channel (2)" src="https://github.com/user-attachments/assets/67c3d523-a5e0-4e71-a50e-b1121ff2a4c8" />

#### show spanning-tree

<img width="735" height="491" alt="verify(2)" src="https://github.com/user-attachments/assets/bc4cb61e-e74c-4477-85f6-f40697f60044" />






## Connectivity Test

PC0:

``` text
ping 192.168.1.20
```

Successful.

<img width="906" height="480" alt="ping before the fail test" src="https://github.com/user-attachments/assets/cdb0332b-a4b2-4242-b3c9-fa26eddedd0d" />

## Simulate Failure

``` cisco
conf t
interface fa0/1
shutdown
```
<img width="916" height="226" alt="simulate a cable failure" src="https://github.com/user-attachments/assets/0eea529e-c236-4139-b83e-18e21af8e218" />

## Verification After Failure

``` cisco
show etherchannel summary
```

Expected:

``` text
Po1(SU)
Fa0/1(D)
Fa0/2(P)
```

Port-channel remains Up/Up and connectivity continues.

<img width="621" height="387" alt="verify port channel (1)" src="https://github.com/user-attachments/assets/78cf1e86-b625-43de-96ab-bc8515ba162f" />
<img width="672" height="397" alt="verify port channel (2)" src="https://github.com/user-attachments/assets/8eb39368-499c-4e6d-b6d1-39352481d91c" />


## What I Learned

-   EtherChannel provides redundancy.
-   LACP removes failed links from the bundle automatically.
-   The logical Port-channel remains operational while one member link
    is still active.
-   STP treats the Port-channel as one logical interface.

## Recruiter Notes

-   Demonstrated LACP failover.
-   Verified uninterrupted user connectivity after a simulated link
    failure.
-   Used Cisco verification commands to validate redundancy.
