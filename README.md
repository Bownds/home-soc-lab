# Home SOC Lab

This repository documents the design, build, and investigation exercises from my cybersecurity home lab.

The goal of this lab is to simulate a small Security Operations Center (SOC) environment where attacks can be generated, detected, investigated, and documented.

---

# Lab Objectives

* Build a virtual SOC environment using Proxmox
* Monitor network activity using Security Onion
* Simulate attacks using Kali Linux
* Investigate alerts using SIEM dashboards
* Write incident investigation reports

---

# Current Lab Status

✔ Proxmox VE hypervisor deployed  
✔ Static network configuration completed  
✔ Ubuntu Server VM deployed  
✔ SSH remote administration configured  

Next systems to deploy:

* Kali Linux attacker machine
* Security Onion SIEM platform
* Windows workstation target
* Centralized log collection

---

# Skills Demonstrated

* Proxmox virtualization
* Linux server administration
* Bridge networking configuration
* Static IP configuration
* Home network architecture
* DHCP troubleshooting
* Remote server administration (SSH)
* Infrastructure documentation
* Network device identification via MAC address

---

# Lab Hardware

### Primary Server

* Dell Optiplex 990
* Intel i5-2400
* 12GB RAM
* 2TB HDD
* Proxmox VE hypervisor

### Additional Systems

* Smilodin desktop (future SOC dashboard display)
* Dell Optiplex 790 (future firewall)
* MacBook Pro (admin workstation)

---

# Planned Lab Architecture

* Kali Linux (Attacker)
* Ubuntu Server (Target)
* Windows Workstation (Target)
* Security Onion (SIEM / IDS)
* Firewall (planned)

---

# Lab Exercises

* Network reconnaissance detection
* SSH brute force investigation
* Web attack simulation
* Malware alert investigation
* Lateral movement simulation

---

# Repository Structure

architecture  
Lab diagrams and network design  

lab-build  
Documentation for building the environment  

detection-labs  
Attack simulations and detection exercises  

incident-reports  
SOC investigation reports  

screenshots  
Evidence collected during investigations  

---

# Lab Roadmap

The following systems will be added to expand the SOC environment:

* Kali Linux attacker machine
* Security Onion SIEM platform
* Windows Active Directory domain
* Sysmon log collection
* Attack simulations
* Incident response exercises

---

# Network Architecture


Internet
│
Optimum Router (192.168.1.1)
│
Google Mesh Router (192.168.86.1)
│
Ethernet
│
Gigabit Switch
│
Proxmox Server (Optiplex 990)
192.168.86.248
│
vmbr0 Bridge
│
Ubuntu Server VM
192.168.86.247
│
SSH
│
MacBook Admin Machine


---

# Infrastructure

Router  
192.168.86.1

Proxmox Hypervisor  
192.168.86.248

Web UI  
https://192.168.86.248:8006

Ubuntu Server VM  
192.168.86.247

SSH Access  
ssh steve@192.168.86.247

---

# Current Environment

### Deployed Systems

* Proxmox VE hypervisor
* Ubuntu Server 24.04 VM
* SSH remote administration

### Next Deployments

* Kali Linux attacker machine
* Security Onion SIEM
* Windows workstation
* Centralized log collection

---

# SOC Investigation Reports

Incident investigation reports generated during attack simulations will be documented in the **incident-reports** directory. These reports will include:

* Attack description
* Detection method
* Log analysis
* Indicators of compromise
* Response actions
