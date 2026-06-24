# Lab 08 - OSPF Single Area Routing

## Objective
Configure OSPF Area 0 between two routers and verify routes are learned dynamically.

## OSPF Commands

R1
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0

R2
router ospf 1
network 192.168.2.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0

## What I Learned
- OSPF is a Link-State routing protocol.
- OSPF uses Area 0 as the backbone area.
- OSPF routes appear with O in the routing table.
- Routers must become neighbors before exchanging routes.

## Understanding Network Statements
network 192.168.2.0 0.0.0.255 area 0
- Advertises 192.168.2.0/24
- Enables OSPF on matching interfaces
- Places interface into Area 0

network 10.0.0.0 0.0.0.3 area 0
- Advertises 10.0.0.0/30 WAN link
- Enables OSPF on router-to-router connection
- Places interface into Area 0
