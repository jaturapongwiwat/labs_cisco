# Lab 3 — DHCP, Default Route and NAT/PAT

## Overview

This lab demonstrates a small office network using Cisco Packet Tracer.

The network is designed with two VLANs and a router connected to a simulated ISP. The main purpose is to practice DHCP, Inter-VLAN Routing, Default Routing, and NAT/PAT.

---

## Objectives

* Configure VLANs on a Layer 2 switch
* Configure Router-on-a-Stick
* Configure DHCP Server on the router
* Configure a Default Route
* Configure NAT/PAT
* Simulate an ISP connection
* Verify network connectivity
* Perform basic network troubleshooting

---

## Network Topology

```text
                         Simulated Internet
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
                         Cisco 2960 Switch
                         /               \
                    VLAN 10             VLAN 20
                    OFFICE                 IT
                   /      \             /      \
                 PC1      PC2          PC3      PC4
```

---

## IP Addressing

### VLAN 10 — OFFICE

| Device     | IP Address       | Gateway       |
| ---------- | ---------------- | ------------- |
| PC1        | DHCP             | 192.168.1.254 |
| PC2        | DHCP             | 192.168.1.254 |
| R1 G0/0.10 | 192.168.1.254/24 | -             |

Network: `192.168.1.0/24`

### VLAN 20 — IT

| Device     | IP Address       | Gateway       |
| ---------- | ---------------- | ------------- |
| PC3        | DHCP             | 192.168.2.254 |
| PC4        | DHCP             | 192.168.2.254 |
| R1 G0/0.20 | 192.168.2.254/24 | -             |

Network: `192.168.2.0/24`

### WAN

| Device | Interface | IP Address     |
| ------ | --------- | -------------- |
| R1     | G0/1      | 203.0.113.2/30 |
| ISP    | G0/0      | 203.0.113.1/30 |

### Simulated Internet

The ISP router uses a loopback interface to simulate an Internet destination.

```text
8.8.8.8/32
```

---

## VLAN and Port Assignment

| Switch Port | Device | VLAN    | Network        |
| ----------- | ------ | ------- | -------------- |
| Fa0/1       | PC1    | VLAN 10 | 192.168.1.0/24 |
| Fa0/2       | PC2    | VLAN 10 | 192.168.1.0/24 |
| Fa0/3       | PC3    | VLAN 20 | 192.168.2.0/24 |
| Fa0/4       | PC4    | VLAN 20 | 192.168.2.0/24 |
| Fa0/24      | R1     | Trunk   | VLAN 10, 20    |

---

## DHCP

R1 is configured as the DHCP server for both VLANs.

### VLAN 10

```text
Network:       192.168.1.0/24
Default GW:    192.168.1.254
DNS Server:    8.8.8.8
Excluded IPs:  192.168.1.1 - 192.168.1.20
```

### VLAN 20

```text
Network:       192.168.2.0/24
Default GW:    192.168.2.254
DNS Server:    8.8.8.8
Excluded IPs:  192.168.2.1 - 192.168.2.20
```

PC1–PC4 are configured to obtain their IP addresses automatically using DHCP.

---

## Inter-VLAN Routing

R1 uses Router-on-a-Stick to route traffic between VLAN 10 and VLAN 20.

```text
R1 G0/0.10
192.168.1.254/24
        |
        | VLAN 10
        |
       SW1
        |
        | VLAN 20
        |
R1 G0/0.20
192.168.2.254/24
```

The physical interface `G0/0` is connected to SW1 using a trunk link.

---

## Default Route

R1 uses a default route to forward unknown destinations to the ISP.

```text
0.0.0.0/0 → 203.0.113.1
```

Configuration:

```text
ip route 0.0.0.0 0.0.0.0 203.0.113.1
```

---

## NAT/PAT

Private IP addresses from the internal networks are translated to the WAN IP of R1 before traffic is sent to the ISP.

```text
192.168.1.0/24
        \
         \
          R1
         /
192.168.2.0/24
        |
        v
203.0.113.2
```

R1 uses PAT with the WAN interface IP:

```text
203.0.113.2
```

This allows multiple internal devices to share the same translated IP address.

---

## Configuration Files

The project includes configuration files for each network device.

```text
lab03-dhcp-nat-routing/
│
├── Lab03-DHCP-NAT.pkt
├── README.md
├── R1-config.txt
├── R2-config.txt
└── SW1-config.txt
```

---

## Verification

### Check R1 Interfaces

```text
R1# show ip interface brief
```

The main interfaces should be `up/up`.

---

### Check Routing Table

```text
R1# show ip route
```

The routing table should contain:

```text
S* 0.0.0.0/0 [1/0] via 203.0.113.1
```

---

### Test R1 to ISP

```text
R1# ping 203.0.113.1
```

---

### Test Simulated Internet

```text
R1# ping 8.8.8.8
```

---

### Check DHCP Bindings

```text
R1# show ip dhcp binding
```

This shows the IP addresses assigned to the client devices.

---

### Check DHCP Pool

```text
R1# show ip dhcp pool
```

---

### Check NAT Translations

```text
R1# show ip nat translations
```

After a client generates traffic, NAT translations should appear in the table.

---

### Check NAT Statistics

```text
R1# show ip nat statistics
```

---

### Check VLANs

```text
SW1# show vlan brief
```

Expected:

```text
VLAN 10    OFFICE
VLAN 20    IT
```

---

### Check Trunk

```text
SW1# show interfaces trunk
```

Fa0/24 should operate as a trunk.

---

## Connectivity Test

The following tests are performed from the client devices.

```text
PC1 → 192.168.1.254
PC1 → 192.168.2.254
PC1 → 203.0.113.1
PC1 → 8.8.8.8
```

Expected traffic flow:

```text
PC
 |
 v
SW1
 |
 v
R1
 |
 | NAT/PAT
 v
ISP
 |
 v
8.8.8.8
```

---

## Troubleshooting

If the default route does not appear in the routing table, check the following:

```text
R1# show ip interface brief
R1# show ip route
R1# show running-config | include ip route
R1# ping 203.0.113.1
```

Also verify that the WAN interfaces are operational.

R1:

```text
G0/1    203.0.113.2    up    up
```

ISP:

```text
G0/0    203.0.113.1    up    up
```

For VLAN or trunk problems:

```text
SW1# show vlan brief
SW1# show interfaces trunk
SW1# show mac address-table
```

---

## What I Learned

Through this lab, I practiced:

* VLAN configuration
* Access port configuration
* Trunk configuration
* Router-on-a-Stick
* Inter-VLAN Routing
* DHCP configuration
* Default Route configuration
* NAT/PAT configuration
* Private and public IP addressing
* Basic network verification
* Basic network troubleshooting

---

## Tools

* Cisco Packet Tracer

---


