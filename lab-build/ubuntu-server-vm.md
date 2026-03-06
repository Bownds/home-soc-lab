# Ubuntu Server VM Deployment

## Objective

Deploy a Linux server inside Proxmox to act as the first system in the SOC lab environment.

---

## Environment

Hypervisor  
Proxmox VE

Host Machine  
Dell Optiplex 990

CPU  
Intel i5-2400

RAM  
12GB

Storage  
2TB HDD

---

## Virtual Machine Configuration

Operating System  
Ubuntu Server 24.04.4 LTS

CPU  
2 cores

Memory  
4GB

Disk  
20GB

Network  
Proxmox bridge vmbr0

---

## Network Verification

Command used


ip a


Result


192.168.86.247/24


---

## SSH Access

SSH connection verified from MacBook.


ssh steve@192.168.86.247


---

## System Status

Kernel  
6.8.0-101-generic

System Load  
0.07

Disk Usage  
34%

Memory Usage  
6%
