# Lab 02 - Inter-VLAN Routing

## Overview

In this lab, I continued from Lab 01 and added a Cisco Router to allow communication between different VLANs.

The main goal of this lab is to understand how VLANs communicate with each other using Inter-VLAN Routing.

I used Cisco Packet Tracer to build and test the network.

---

## Topology

                    R1
                 G0/0
                   |
                 Trunk
                   |
                  SW1
        ┌──────────┼──────────┐──────────┐
        │          │          │          │
      VLAN 10    VLAN 10    VLAN 20    VLAN 20
        │          │          │          │
       PC1        PC2        PC3        PC4


---

## Network Design

### VLANs

| VLAN | Name   | Network        |
| ---- | ------ | -------------- |
| 10   | OFFICE | 192.168.1.0/24 |
| 20   | IT     | 192.168.2.0/24 |

### IP Addressing

| Device | VLAN | IP Address  | Default Gateway |
| ------ | ---: | ----------- | --------------- |
| PC1    |   10 | 192.168.1.1 | 192.168.1.254   |
| PC2    |   10 | 192.168.1.2 | 192.168.1.254   |
| PC3    |   20 | 192.168.2.3 | 192.168.2.254   |
| PC4    |   20 | 192.168.2.4 | 192.168.2.254   |

### Router

| Interface | VLAN | IP Address    |
| --------- | ---: | ------------- |
| G0/0.10   |   10 | 192.168.1.254 |
| G0/0.20   |   20 | 192.168.2.254 |

---

## Configuration

### Switch

The switch ports connected to the PCs were configured as access ports.

interface range fa0/1-2
switchport mode access
switchport access vlan 10

interface range fa0/3-4
switchport mode access
switchport access vlan 20

The connection between the switch and router was configured as a trunk.


interface fa0/24
switchport mode trunk


---

## Router Configuration

I used Router-on-a-Stick for Inter-VLAN Routing.

### VLAN 10


interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.1.254 255.255.255.0

### VLAN 20


interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.2.254 255.255.255.0


---

## Testing

First, I tested communication between devices in the same VLAN.

### PC1 → PC2


ping 192.168.1.2


Result: **Success**

### PC3 → PC4


ping 192.168.2.4


Result: **Success**

After that, I tested communication between different VLANs.

### PC1 → PC3


ping 192.168.2.3


Result: **Success**

The communication worked because the router was configured to route traffic between VLAN 10 and VLAN 20.

---

## Verification Commands

I used the following commands to check the configuration.

### Switch


show vlan brief
show interfaces trunk
show mac address-table


### Router


show ip interface brief
show ip route


---

## What I Learned

From this lab, I learned:

* How VLANs separate devices into different Layer 2 networks.
* The difference between an Access Port and a Trunk Port.
* How a trunk carries traffic from multiple VLANs.
* How Router-on-a-Stick works.
* How to configure router sub-interfaces.
* How devices in different VLANs communicate through a router.
* How to verify VLAN and routing configurations using Cisco IOS commands.

---

## Tools

* Cisco Packet Tracer



