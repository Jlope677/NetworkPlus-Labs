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

       Switch0
       /     \
      /       \
     /         \
Switch1-------Switch2

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

## Failure Test

Disconnected:
Switch0 ↔ Switch1

Before:
Fa0/2 Alternate Blocking

After:
Fa0/2 Root Forwarding

This demonstrated STP convergence and fault tolerance.

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
