# Lab 01 — Basic Switching and VLAN

## Overview

This lab demonstrates basic Cisco switch configuration and VLAN segmentation using Cisco Packet Tracer.

## Objectives

* Configure a Cisco switch
* Create VLANs
* Assign switch ports to VLANs
* Understand access ports
* Test connectivity within the same VLAN
* Understand why devices in different VLANs cannot communicate without Layer 3 routing

## Topology


PC1 ── Fa0/1 ──┐
PC2 ── Fa0/2 ──┤
               SW1
PC3 ── Fa0/3 ──┤
PC4 ── Fa0/4 ──┘


## IP Addressing

| Device | VLAN | IP Address    | Subnet Mask   |
| PC1    |   10 | 192.168.1.1   | 255.255.255.0 |
| PC2    |   10 | 192.168.1.2   | 255.255.255.0 |
| PC3    |   20 | 192.168.1.3   | 255.255.255.0 |
| PC4    |   20 | 192.168.1.4   | 255.255.255.0 |

## VLAN Configuration

| VLAN | Name   | Ports       |
| ---: | ------ | ----------- |
|   10 | OFFICE | Fa0/1–Fa0/2 |
|   20 | IT     | Fa0/3–Fa0/4 |

## Configuration

### Create VLANs


vlan 10
name OFFICE

vlan 20
name IT


### Configure VLAN 10 Ports


interface range fa0/1-2
switchport mode access
switchport access vlan 10


### Configure VLAN 20 Ports


interface range fa0/3-4
switchport mode access
switchport access vlan 20


## Verification


show vlan brief


The switch should show:


VLAN 10 → Fa0/1, Fa0/2
VLAN 20 → Fa0/3, Fa0/4


## Connectivity Test

| Test      | Result    |
| --------- | --------- |
| PC1 → PC2 |  Success  |
| PC3 → PC4 |  Success  |
| PC1 → PC3 |  Failed   |
| PC2 → PC4 |  Failed   |

## What I Learned

* A VLAN separates devices into different Layer 2 broadcast domains.
* Access ports can be assigned to a specific VLAN.
* Devices in different IP subnets require Layer 3 routing to communicate.
* `show vlan brief` can be used to verify VLAN and port assignments.

## Tools

* Cisco Packet Tracer

