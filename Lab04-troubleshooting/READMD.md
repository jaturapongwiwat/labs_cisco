# Lab 04 — Network Troubleshooting

## Overview

This lab is a continuation of **Lab 03 — DHCP, Routing and NAT/PAT**.

The same network topology and configuration from Lab 03 were used as the starting point. Several network problems were intentionally introduced into the configuration to simulate real-world network incidents.

---

## Objectives

* Troubleshoot VLAN configuration issues
* Troubleshoot trunk configuration issues
* Check default routing
* Troubleshoot NAT/PAT
* Verify connectivity from end devices to the ISP
* Practice a structured troubleshooting process
* Identify root causes using Cisco IOS commands

---

## Network Topology

```text
                    Internet
                       |
                    ISP (R2)
                  G0/0
               203.0.113.1/30
                       |
               203.0.113.2/30
                  G0/1 - R1
                       |
                    G0/0
                       |
                      SW1
                 /           \
            VLAN 10         VLAN 20
             OFFICE             IT
            PC1  PC2          PC3  PC4
```

---

## Base Configuration

The network was originally configured based on **Lab 03**.

### VLAN 10 — OFFICE

```text
Network: 192.168.1.0/24
Gateway: 192.168.1.254
```

### VLAN 20 — IT

```text
Network: 192.168.2.0/24
Gateway: 192.168.2.254
```

### WAN

```text
R1: 203.0.113.2/30
ISP: 203.0.113.1/30
```

### Simulated Internet

```text
ISP Loopback0: 8.8.8.8/32
```

---

# Problems Introduced

Four problems were intentionally created before starting the troubleshooting process.

| Issue | Problem                                   |
| ----- | ----------------------------------------- |
| 1     | PC2 was assigned to the wrong VLAN        |
| 2     | SW1 trunk port was changed to access mode |
| 3     | Default route was removed from R1         |
| 4     | NAT/PAT configuration was removed from R1 |

The problems were not fixed immediately. They were left in the network so that troubleshooting could be performed from the beginning.

---

# Troubleshooting Process

The troubleshooting process followed this general path:

```text
PC
 ↓
IP Address
 ↓
VLAN
 ↓
Default Gateway
 ↓
Trunk
 ↓
Router
 ↓
Routing
 ↓
NAT/PAT
 ↓
ISP
```

---

## Issue 1 — PC2 Assigned to the Wrong VLAN

### Symptom

PC2 could not communicate correctly with devices in VLAN 10.

PC2 was supposed to be connected to:

```text
VLAN 10 — OFFICE
```

However, the switch port was configured as VLAN 20.

### Check

On SW1:

```text
SW1# show vlan brief
```

The output showed that the port connected to PC2 was assigned to VLAN 20 instead of VLAN 10.

### Root Cause

The access port for PC2 was assigned to the wrong VLAN.

### Solution

The port was changed back to VLAN 10.

```text
SW1# configure terminal
SW1(config)# interface fa0/2
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# end
```

### Verification

```text
SW1# show vlan brief
```

PC2 was then shown under VLAN 10.

---

# Issue 2 — Trunk Port Configuration

### Symptom

Devices could not communicate through the Router-on-a-Stick connection.

PC1 could not reach its gateway:

```text
192.168.1.254
```

### Check

On SW1:

```text
SW1# show interfaces trunk
```

The trunk interface did not appear because Fa0/24 had been changed to an access port.

### Root Cause

The link between SW1 and R1 requires a trunk to carry multiple VLANs.

Fa0/24 was configured as an access port instead of a trunk port.

### Solution

Configure Fa0/24 as a trunk.

```text
SW1# configure terminal
SW1(config)# interface fa0/24
SW1(config-if)# switchport mode trunk
SW1(config-if)# end
```

### Verification

```text
SW1# show interfaces trunk
```

Fa0/24 was then shown as a trunk interface.

PC1 was also able to reach its default gateway again.

---

# Issue 3 — Missing Default Route

### Symptom

The LAN could communicate with R1, but traffic could not be routed toward the ISP.

### Check

On R1:

```text
R1# show ip route
```

The default route was checked for:

```text
S* 0.0.0.0/0 via 203.0.113.1
```

If the default route is missing, R1 does not know where to send traffic destined for networks outside the local routing table.

### Root Cause

The default route to the ISP had been removed.

### Solution

Add the default route back to R1.

```text
R1# configure terminal
R1(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
R1(config)# end
```

### Verification

```text
R1# show ip route
```

Expected:

```text
S* 0.0.0.0/0 [1/0] via 203.0.113.1
```

The connection between R1 and the ISP was also tested:

```text
R1# ping 203.0.113.1
```

---

# Issue 4 — Missing NAT/PAT Configuration

### Symptom

The LAN and WAN interfaces were working, but PCs still could not reach the simulated Internet:

```text
8.8.8.8
```

R1 could reach the ISP, but traffic from the private LAN networks was not being translated.

### Check

The NAT configuration was checked with:

```text
R1# show running-config | include ip nat
```

The output showed the inside/outside configuration, but the NAT overload rule was missing.

### Root Cause

The NAT/PAT rule had been removed.

Without NAT/PAT, private IP addresses such as:

```text
192.168.1.x
192.168.2.x
```

were not translated to the R1 WAN address.

### Solution

Configure PAT using the existing ACL:

```text
R1# configure terminal
R1(config)# ip nat inside source list 1 interface gigabitEthernet 0/1 overload
R1(config)# end
```

### Verification

Generate traffic from a PC by pinging:

```text
8.8.8.8
```

Then check the NAT table:

```text
R1# show ip nat translations
```

NAT translations should appear after traffic is generated.

Additional verification:

```text
R1# show ip nat statistics
```

The PC was then able to reach the simulated Internet.

---

# Final Verification

After fixing all four problems, the following checks were performed.

### Switch

```text
SW1# show vlan brief
SW1# show interfaces trunk
SW1# show mac address-table
```

### Router

```text
R1# show ip interface brief
R1# show ip route
R1# show ip nat translations
R1# show ip nat statistics
```

### Connectivity

```text
R1# ping 203.0.113.1
R1# ping 8.8.8.8
```

From the PCs:

```text
ping 192.168.1.254
ping 192.168.2.254
ping 8.8.8.8
```

---

# Troubleshooting Summary

| Issue                                   | Symptom                                | Root Cause                      | Solution                 |
| --------------------------------------- | -------------------------------------- | ------------------------------- | ------------------------ |
| PC2 cannot communicate correctly        | PC2 was in the wrong network           | Wrong VLAN assignment           | Change Fa0/2 to VLAN 10  |
| PC cannot reach gateway                 | Router-on-a-Stick communication failed | Trunk port configured as access | Change Fa0/24 to trunk   |
| Internet connectivity failed            | R1 had no route to external networks   | Missing default route           | Add default route to ISP |
| LAN could not access simulated Internet | No NAT translation                     | Missing NAT/PAT rule            | Configure NAT overload   |

---

# What I Learned

Through this lab, I practiced troubleshooting a network by checking each layer and configuration step instead of immediately changing multiple settings.

Key topics practiced:

* VLAN troubleshooting
* Access port configuration
* Trunk configuration
* Router-on-a-Stick
* Default routing
* NAT/PAT
* Cisco IOS verification commands
* Connectivity testing using `ping`
* Identifying root causes from configuration and command output

This lab also helped me practice a troubleshooting workflow similar to handling network incidents in a NOC environment.

---

# Tools

* Cisco Packet Tracer
* Cisco IOS CLI

---

