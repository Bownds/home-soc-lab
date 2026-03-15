# Home SOC Lab
![Proxmox](https://img.shields.io/badge/Virtualization-Proxmox-blue)
![Security Onion](https://img.shields.io/badge/SIEM-Security%20Onion-green)
![Kali Linux](https://img.shields.io/badge/Attacker-Kali%20Linux-red)
![Ubuntu](https://img.shields.io/badge/Target-Ubuntu-orange)
---
## Lab Architecture
<p align="center">
  <img src="architecture/lab-network-diagram.png" width="400">
</p>
This repository documents the design, build, and investigation exercises from my personal cybersecurity home lab.
The goal of this lab is to simulate a small **Security Operations Center (SOC)** environment where attacks can be generated, detected, investigated, and documented using real security tools.
---
## Lab Objectives
* Build a virtual SOC environment using Proxmox
* Monitor network activity using Security Onion
* Simulate attacks using Kali Linux
* Investigate alerts using SIEM dashboards
* Write incident investigation reports
---
## Current Lab Status
✔ Proxmox VE hypervisor deployed  
✔ Static network configuration completed  
✔ Ubuntu Server VM deployed  
✔ SSH remote administration configured  
✔ Apache web server deployed on target server  
✔ Weak credential user created for brute-force testing  
✔ Kali Linux attacker VM deployed  
✔ Initial reconnaissance scan performed using Nmap
### Next Systems to Deploy
* Security Onion SIEM platform
* Windows workstation target
* Centralized log collection
* Attack simulation scenarios
---
## Skills Demonstrated
* Proxmox virtualization
* Linux server administration
* Bridge networking configuration
* Static IP configuration
* Home network architecture
* DHCP troubleshooting
* Remote server administration (SSH)
* Apache web server deployment
* Attack surface preparation
* Infrastructure documentation
* Network device identification via MAC address
* Network reconnaissance using Nmap
---
## Lab Hardware
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
## Planned Lab Architecture
* Kali Linux (Attacker VM)
* Ubuntu Server (Target VM)
* Windows Workstation (Target)
* Security Onion (SIEM / IDS)
* Firewall (planned)
---
## Infrastructure
Router  
192.168.86.1
Proxmox Hypervisor  
192.168.86.248
Web Interface  
https://192.168.86.248:8006
Ubuntu Server VM  
192.168.86.38
SSH Access  
```
ssh steve@192.168.86.38
```
---

## Initial Reconnaissance Testing

After deploying the attacker and target machines, a reconnaissance scan was performed from the Kali Linux attacker VM against the Ubuntu target server.

### Target

Ubuntu Server  
192.168.86.38

### Tool Used

Nmap (Network Mapper)

### Command

nmap -sV 192.168.86.38

### Result

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu
80/tcp open  http    Apache httpd 2.4.58 (Ubuntu)

### Findings

The scan confirmed the Ubuntu target server exposes two services:

• SSH (Port 22) – remote administration access  
• HTTP (Port 80) – Apache web server  

This represents the reconnaissance phase of an attack, where an adversary identifies open ports and running services before attempting exploitation.

Future phases of the lab will use Security Onion to detect and investigate this type of network scanning activity.

---
## Lab Exercises
Planned security investigation exercises include:
* Network reconnaissance detection
* SSH brute force investigation
* Web attack simulation
* Malware alert investigation
* Lateral movement simulation
---
## Repository Structure
```
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
```
---
## Lab Roadmap
The following systems will be added to expand the SOC environment:
* Kali Linux attacker machine
* Security Onion SIEM platform
* Windows Active Directory domain
* Sysmon log collection
* Attack simulations
* Incident response exercises
---
## SOC Investigation Reports
Incident investigation reports generated during attack simulations will be documented in the **incident-reports** directory.
Reports will include:
* Attack description
* Detection method
* Log analysis
* Indicators of compromise (IOCs)
* Response actions
