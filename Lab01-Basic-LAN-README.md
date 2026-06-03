# Lab 01 - Basic LAN Connectivity

## Objective

Configure two PCs on the same Local Area Network (LAN) using a Layer 2 switch and verify connectivity through ICMP testing (ping).

---

## Topology

```text
PC0 ---- Switch0 ---- PC1
```

### Devices Used

| Device | Model |
|----------|----------|
| PC0 | Generic PC |
| PC1 | Generic PC |
| Switch0 | Cisco 2960 |


<img width="398" height="312" alt="image" src="https://github.com/user-attachments/assets/ac84a24a-b590-48c3-a2ce-e54993907f07" />


---

## Network Configuration

### PC0

| Setting | Value |
|----------|----------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |


<img width="695" height="712" alt="image" src="https://github.com/user-attachments/assets/92803d8a-39fd-4075-be8c-88dd9a274ceb" />


### PC1

| Setting | Value |
|----------|----------|
| IP Address | 192.168.1.20 |
| Subnet Mask | 255.255.255.0 |


<img width="690" height="651" alt="image" src="https://github.com/user-attachments/assets/da3c1374-961f-4ef3-8bd0-8370707e8db8" />

---

## Verification

### Connectivity Test

From PC0:

```cmd
ping 192.168.1.20
```

Expected Result:

```text
Reply from 192.168.1.20
Packets: Sent = 4, Received = 4, Lost = 0
```

Result:

✅ Successful communication between PC0 and PC1.


<img width="688" height="687" alt="image" src="https://github.com/user-attachments/assets/6f36c968-c2b0-432e-846c-f0192b4e282b" />
<img width="707" height="688" alt="image" src="https://github.com/user-attachments/assets/d12b2009-7cdb-412b-a891-9e97a0a76ad4" />


---

## Troubleshooting Exercise

### Issue Introduced

PC1 IP address was changed to:

```text
192.168.2.20
255.255.255.0
```
<img width="696" height="700" alt="image" src="https://github.com/user-attachments/assets/0b768d92-9a61-4b7d-9637-2f64a2d054ac" />

### Test Performed

From PC0:

```cmd
ping 192.168.2.20
```

### Result

❌ Ping failed.

<img width="708" height="715" alt="image" src="https://github.com/user-attachments/assets/90f472d5-8203-4ace-b1bb-623a0366ff6f" />

### Root Cause Analysis

PC0 and PC1 were configured on different networks:

| Device | Network |
|----------|----------|
| PC0 | 192.168.1.0/24 |
| PC1 | 192.168.2.0/24 |

A Layer 2 switch can only forward traffic within the same subnet. Communication between different networks requires a router.

### Resolution

Changed PC1 back to:

```text
192.168.1.20
255.255.255.0
```

Connectivity was restored successfully.

---

## Key Networking Concepts

- IPv4 Addressing
- Subnet Masks
- Layer 2 Switching
- ICMP (Ping)
- Basic Network Troubleshooting

---

## Lessons Learned

- Devices connected to the same switch must be in the same subnet to communicate directly.
- IP addressing mistakes are a common cause of connectivity issues.
- The `ping` command is an effective first step when troubleshooting network connectivity.
- Layer 2 switches forward frames based on MAC addresses and do not perform routing functions.

---

## Skills Demonstrated

- IP Configuration
- Connectivity Testing
- Basic Troubleshooting
- Network Documentation
- Packet Tracer Topology Creation
