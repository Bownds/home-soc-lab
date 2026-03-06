# Home SOC Lab Network Architecture

This lab simulates a small SOC environment where attacks can be generated, detected, and investigated.

## Network

Home LAN
192.168.86.0/24

## Infrastructure

Router  
192.168.86.1

Proxmox Hypervisor  
192.168.86.50

Ubuntu Server VM  
192.168.86.247

SSH Access  
ssh steve@192.168.86.247

Proxmox Web Interface  
https://192.168.86.50:8006

---

## Logical Architecture

MacBook (Admin)
│
SSH
│
Ubuntu Server VM
│
Proxmox Hypervisor
│
Wireless Bridge
│
Home Router
