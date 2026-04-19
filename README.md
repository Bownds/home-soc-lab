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

✔ Proxmox VE 9.1.1 hypervisor deployed  
✔ Ubuntu Server (target) deployed and configured  
✔ Kali Linux (attacker) deployed  
✔ Wazuh SIEM deployed (all-in-one)  
✔ Wazuh agent installed and connected to Ubuntu  
✔ SSH service exposed for testing  
✔ Brute force attack simulated using Hydra  
✔ Detection validated in Wazuh (1,100+ events)  
✔ Incident report created and documented  
✔ Network reconnaissance completed (Nmap -sV, -A)  
⬜ Web enumeration (Gobuster, Nikto) — in progress  
⬜ Web attack simulation  
⬜ Wazuh agent re-connected to updated environment  

---

## Skills Demonstrated

* SIEM deployment and configuration (Wazuh)
* Endpoint monitoring and log analysis
* Brute force attack simulation (Hydra)
* Network reconnaissance (Nmap)
* Web enumeration (Gobuster, Nikto) — in progress
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

> Note: IPs are DHCP-assigned and may change between sessions. Values below reflect current confirmed addresses.

| VM | OS | IP (current) | Role |
|---|---|---|---|
| VM 101 | Kali Linux | 192.168.86.38 | Attacker |
| VM 100 | Ubuntu Server | 192.168.86.43 | Target |
| VM 102 | Wazuh | TBD | SIEM / Defender |

**Network:** 192.168.86.0/24  
**Hypervisor:** Proxmox VE 9.1.1 on Dell Optiplex 990

### Target Services Confirmed

| Port | Service | Version |
|---|---|---|
| 22 | SSH | OpenSSH 9.6p1 |
| 80 | HTTP | Apache httpd 2.4.58 |

---

## Attack Simulations

### 1. SSH Brute Force (Hydra)

A brute force attack was simulated from the Kali attacker machine targeting the Ubuntu server's SSH service.

**Command used:**
```bash
hydra -l fakeuser -P /usr/share/wordlists/rockyou.txt ssh://192.168.86.35
```

**Outcome:**
* Over 1,100 failed login attempts generated
* Wazuh successfully detected repeated authentication failures
* Alerts mapped to MITRE ATT&CK technique **T1110 – Brute Force**

---

### 2. Network Reconnaissance (Nmap)

Passive and active reconnaissance performed against the Ubuntu target to enumerate open ports, service versions, and OS information.

**Commands used:**
```bash
ping -c 4 192.168.86.43
nmap -sV 192.168.86.43
nmap -A 192.168.86.43
```

**Findings:**

| Port | State | Service | Version |
|---|---|---|---|
| 22/tcp | open | SSH | OpenSSH 9.6p1 Ubuntu |
| 80/tcp | open | HTTP | Apache httpd 2.4.58 |

**Notable observations:**
* Default Apache "It works" page still active — indicates unconfigured/unhardened web server
* SSH host keys (ECDSA + ED25519) enumerated and recorded
* OS fingerprinting returned ambiguous result (Linux + MikroTik false positive) — demonstrates limitation of Nmap OS detection vs. service banner reliability
* Network distance: 1 hop (same subnet, direct connection)

**MITRE ATT&CK:** T1046 – Network Service Discovery

---

## Detection and Analysis

Wazuh detected during SSH brute force exercise:
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

```
architecture/         Lab diagrams and network design
incident-reports/     SOC investigation reports
screenshots/          Evidence collected during investigations
```

---

## Lab Hardware

### Primary Server

* Dell Optiplex 990
* Intel i5-2400
* 16GB RAM
* 2TB HDD
* Proxmox VE 9.1.1

---

## Lab Roadmap

### Completed
- [x] Proxmox hypervisor setup
- [x] VM deployment (Kali, Ubuntu, Wazuh)
- [x] Wazuh agent installation and configuration
- [x] SSH brute force simulation and detection (Hydra)
- [x] Network reconnaissance (Nmap)

### In Progress
- [ ] Web enumeration (Gobuster, Nikto)
- [ ] Web attack simulation

### Planned
- [ ] Wazuh detection rules for web attacks
- [ ] Windows endpoint monitoring
- [ ] Custom Wazuh detection rules
- [ ] Alert tuning and thresholds
- [ ] Lateral movement scenarios
- [ ] Centralized log enrichment
- [ ] Threat intelligence integration

---

## Summary

This lab demonstrates a complete SOC workflow:

* **Attack simulation** — Kali Linux (Hydra, Nmap, Gobuster, Nikto)
* **Detection** — Wazuh SIEM
* **Investigation** — log analysis and alert correlation
* **Documentation** — incident reporting and lab journaling

It is designed to replicate real-world security monitoring and analysis practices in a controlled environment.
