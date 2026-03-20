# Home SOC Lab

![Proxmox](https://img.shields.io/badge/Virtualization-Proxmox-blue)
![Wazuh](https://img.shields.io/badge/SIEM-Wazuh-green)
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
* Monitor endpoint activity using Wazuh SIEM
* Simulate attacks using Kali Linux
* Investigate alerts using SIEM dashboards
* Document findings through incident reports

---

## Current Lab Status

✔ Proxmox VE hypervisor deployed  
✔ Ubuntu Server (target) deployed and configured  
✔ Kali Linux (attacker) deployed  
✔ Wazuh SIEM deployed (all-in-one)  
✔ Wazuh agent installed and connected to Ubuntu  
✔ SSH service exposed for testing  
✔ Brute force attack simulated using Hydra  
✔ Detection validated in Wazuh (1,100+ events)  
✔ Incident report created and documented  

---

## Skills Demonstrated

* SIEM deployment and configuration (Wazuh)
* Endpoint monitoring and log analysis
* Brute force attack simulation (Hydra)
* Network reconnaissance (Nmap)
* SSH authentication log analysis
* MITRE ATT&CK mapping (T1110 – Brute Force)
* Threat detection and investigation workflows
* Proxmox virtualization
* Linux system administration
* Network troubleshooting and configuration
* Incident documentation and reporting

---

## Lab Environment

### Systems

* Kali Linux (Attacker) – 192.168.86.39  
* Ubuntu Server (Target) – 192.168.86.35  
* Wazuh SIEM – 192.168.86.36  

---

## Attack Simulation: SSH Brute Force

A brute force attack was simulated from the Kali attacker machine targeting the Ubuntu server's SSH service.

### Command Used


hydra -l fakeuser -P /usr/share/wordlists/rockyou.txt ssh://192.168.86.35


### Outcome

* Over 1,100 failed login attempts generated  
* Wazuh successfully detected repeated authentication failures  
* Alerts mapped to MITRE ATT&CK technique T1110  

---

## Detection and Analysis

Wazuh detected:

* Repeated SSH authentication failures  
* Invalid user login attempts  
* High-frequency attack behavior  
* Source IP correlation to attacker system  

This demonstrates successful detection of brute force activity in a monitored environment.

---

## SOC Investigation Reports

Incident reports are located in the **incident-reports** directory.

### Completed Reports

* SSH Brute Force Attack Detection  
  → `incident-reports/ssh-brute-force.md`

---

## Repository Structure


architecture
Lab diagrams and network design
incident-reports
SOC investigation reports
screenshots
Evidence collected during investigations


---

## Lab Hardware

### Primary Server

* Dell Optiplex 990  
* Intel i5-2400  
* 16GB RAM  
* 2TB HDD  
* Proxmox VE  

---

## Lab Roadmap

Planned future expansions:

* Windows endpoint monitoring
* Centralized log enrichment
* Custom detection rules
* Alert tuning and thresholds
* Web attack simulation
* Lateral movement scenarios
* Threat intelligence integration

---

## Summary

This lab demonstrates a complete SOC workflow:

* Attack simulation (Kali Linux)
* Detection (Wazuh SIEM)
* Investigation (log analysis)
* Documentation (incident reporting)

It is designed to replicate real-world security monitoring and analysis practices in a controlled environment.
