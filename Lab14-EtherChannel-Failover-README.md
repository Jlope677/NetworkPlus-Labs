# Lab 13 -- EtherChannel Failover & Troubleshooting

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

> EtherChannel configuration was completed in Lab 12.

## Verification Before Failure

``` cisco
show etherchannel summary
show interface port-channel 1
show interfaces trunk
show spanning-tree
```

Verified: - Po1(SU) - Fa0/1(P) - Fa0/2(P) - Port-channel Up/Up - Trunk
operational - STP recognizes Port-channel1

## Connectivity Test

PC0:

``` text
ping 192.168.1.20
```

Successful.

## Simulate Failure

``` cisco
conf t
interface fa0/1
shutdown
```

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
