# Lab 05 - Spanning Tree Protocol (STP)

## Objective

Demonstrate how Spanning Tree Protocol (STP) prevents Layer 2 loops by blocking redundant paths while maintaining fault tolerance.

## Skills Demonstrated

- Layer 2 loop prevention
- Broadcast storm prevention
- Root Bridge election
- Root ports
- Designated ports
- Alternate (blocked) ports
- STP convergence
- Fault tolerance

## Topology

  <img width="695" height="510" alt="tapology" src="https://github.com/user-attachments/assets/4b7835b6-c065-48b6-bd20-63d6b04ebe25" />


## Devices Used

- Switch0 - Cisco 2960
- Switch1 - Cisco 2960
- Switch2 - Cisco 2960

## Root Bridge

Switch0 became the Root Bridge.

Root Bridge MAC:
000A.F3E3.9B34

## STP Port Roles

Switch0
- Fa0/1 Designated Forwarding
- Fa0/2 Designated Forwarding

Switch1
- Fa0/1 Root Port
- Fa0/2 Alternate Port (Blocking)

Switch2
- Fa0/1 Root Port
- Fa0/2 Designated Port

## Verification Command

show spanning-tree

<img width="756" height="340" alt="Verification Command switch0" src="https://github.com/user-attachments/assets/e358eac4-c135-4887-8a5d-01c6177048d4" />

<img width="712" height="362" alt="Verification Command switch1" src="https://github.com/user-attachments/assets/be0bdcc0-7d74-4f0d-b381-4a59e67a64f7" />

<img width="682" height="352" alt="Verification Command switch2" src="https://github.com/user-attachments/assets/298e48e3-44a1-43a6-921a-133264169737" />



## Failure Test

Disconnected:
Switch0 ↔ Switch1

<img width="661" height="440" alt="disconnected switch 0 and 1" src="https://github.com/user-attachments/assets/2c5d4252-b82a-4445-b155-5a16ed114072" />

Before:
Fa0/2 Alternate Blocking

After:
Fa0/2 Root Forwarding

This demonstrated STP convergence and fault tolerance.

<img width="1032" height="840" alt="failure test" src="https://github.com/user-attachments/assets/3becd621-2466-4028-837d-2e43f0585b4a" />


## Lessons Learned

- Redundant links improve availability.
- Redundant links can create Layer 2 loops.
- STP prevents loops by blocking one path.
- STP automatically activates backup paths when failures occur.
- A blocked port can become a forwarding port after convergence.

## Skills Demonstrated

- Cisco IOS CLI
- STP Verification
- Root Bridge Identification
- Layer 2 Troubleshooting
- Network Redundancy Design
- Fault Tolerance Concepts
- Convergence Analysis
- Packet Tracer Design
