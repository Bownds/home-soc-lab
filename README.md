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
✔ Network reconnaissance completed (Nmap)  
✔ Web enumeration completed (Gobuster, Nikto)  
✔ Brute force attack simulated using Hydra  
✔ Detection validated in Wazuh (3,000+ events)  
✔ MITRE ATT&CK mapping reviewed in dashboard  
✔ Incident report created and documented  
⬜ Apache log monitoring in Wazuh  
⬜ Network IDS (Suricata)  
⬜ Web attack simulation (DVWA)  
⬜ Windows endpoint monitoring  

---

## Skills Demonstrated

* SIEM deployment and configuration (Wazuh)
* Endpoint monitoring and log analysis
* Network reconnaissance (Nmap)
* Web enumeration (Gobuster, Nikto)
* Brute force attack simulation (Hydra)
* SSH authentication log analysis
* MITRE ATT&CK mapping (T1046, T1110.001)
* Alert correlation and severity triage
* Detection gap analysis
* Threat detection and investigation workflows
* Proxmox virtualization
* Linux system administration
* Incident documentation and reporting

---

## Lab Environment

### Systems

> Note: IPs are DHCP-assigned and may change between sessions.

| VM | OS | IP (current) | Role |
|---|---|---|---|
| VM 101 | Kali Linux | 192.168.86.38 | Attacker |
| VM 100 | Ubuntu Server | 192.168.86.43 | Target |
| VM 102 | Wazuh | 192.168.86.44 | SIEM / Defender |

**Network:** 192.168.86.0/24  
**Hypervisor:** Proxmox VE 9.1.1 on Dell Optiplex 990

### Target Services Confirmed

| Port | Service | Version |
|---|---|---|
| 22 | SSH | OpenSSH 9.6p1 |
| 80 | HTTP | Apache httpd 2.4.58 |

---

## Attack Simulations

### 1. Network Reconnaissance (Nmap)

Passive and active reconnaissance against the Ubuntu target to enumerate open ports, service versions, and OS information.

**Commands used:**
```bash
ping -c 4 192.168.86.43
nmap -sV 192.168.86.43
nmap -A 192.168.86.43
```

**Findings:**

| Port | State | Service | Version |
|---|---|---|---|
| 22/tcp | open | SSH | OpenSSH 9.6p1 |
| 80/tcp | open | HTTP | Apache httpd 2.4.58 |

**Notable observations:**
* Default Apache page still active — server not hardened
* SSH host keys (ECDSA + ED25519) enumerated
* OS fingerprinting returned false positive (MikroTik) — service banners are more reliable
* Network distance: 1 hop (same subnet)

**MITRE ATT&CK:** T1046 – Network Service Discovery  
**Wazuh detection:** ❌ Not detected (network layer not monitored by default)

---

### 2. Web Enumeration (Gobuster + Nikto)

Directory brute force and vulnerability scanning against the Apache web server.

**Commands used:**
```bash
gobuster dir -u http://192.168.86.43 -w /usr/share/wordlists/dirb/common.txt
nikto -h http://192.168.86.43
```

**Gobuster findings:**

| Path | Status | Notes |
|---|---|---|
| `/index.html` | 200 | Default Apache page |
| `/server-status` | 403 | mod_status enabled — info leakage risk |
| `/.htaccess`, `/.htpasswd`, `/.hta` | 403 | Exist but blocked |

**Nikto findings:**
* Version disclosure — Apache version in every response header
* Missing X-Frame-Options — clickjacking protection absent
* Missing X-Content-Type-Options — MIME sniffing protection absent
* ETag inode leakage — CVE-2003-1418, 20+ year old unpatched misconfiguration
* OPTIONS method allowed — server reveals accepted HTTP verbs

**Wazuh detection:** ❌ Not detected (Apache logs not configured in agent)

---

### 3. SSH Brute Force (Hydra)

Credential attack against the Ubuntu SSH service using the rockyou.txt wordlist.

**Command used:**
```bash
hydra -l steve -P /usr/share/wordlists/rockyou.txt ssh://192.168.86.43
```

**Attack stats:**
* 248 tries/minute, 16 parallel tasks
* 14,344,399 passwords in wordlist

**Wazuh detection:** ✅ Detected immediately

| Metric | Value |
|---|---|
| Events generated | 3,000+ in ~2 minutes |
| Rule fired | 5760 – sshd authentication failed (level 5) |
| Correlation alert | Level 12 (High) – failures followed by success |
| MITRE tactic | Credential Access |
| MITRE technique | T1110.001 – Password Guessing |

---

## Detection Analysis

### What Wazuh Caught vs. Missed

| Attack | Tool | Detected | Reason |
|---|---|---|---|
| Port scan | nmap | ❌ | Network layer — not in OS logs |
| Web enumeration | gobuster | ❌ | Apache logs not configured |
| Web vuln scan | nikto | ❌ | Apache logs not configured |
| SSH brute force | hydra | ✅ | auth.log monitored by default |
| Auth failures + success | manual | ✅ | Correlation rule (level 12) |

**Key insight:** Wazuh out of the box monitors OS authentication well but is blind to network and web layer attacks. Closing these gaps requires Apache log monitoring and a network IDS like Suricata.

### MITRE ATT&CK Coverage

Tactics observed in the Wazuh dashboard after today's exercises:
* Credential Access (dominant — Hydra brute force)
* Lateral Movement (SSH usage)
* Reconnaissance (partial detection)
* Defense Evasion (sudo activity)
* Privilege Escalation (sudo commands)

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
- [x] Network reconnaissance (Nmap)
- [x] Web enumeration (Gobuster, Nikto)
- [x] SSH brute force simulation and detection (Hydra)
- [x] MITRE ATT&CK dashboard analysis
- [x] Detection gap analysis

### Planned
- [ ] Apache log monitoring in Wazuh
- [ ] Network IDS with Suricata
- [ ] Web attack simulation (DVWA)
- [ ] Custom Wazuh detection rules
- [ ] Windows endpoint monitoring
- [ ] Alert tuning and thresholds
- [ ] Lateral movement scenarios
- [ ] Threat intelligence integration

---

## Summary

This lab demonstrates a complete SOC workflow:

* **Reconnaissance** — Nmap network and service scanning
* **Enumeration** — Gobuster directory brute force, Nikto vulnerability scanning
* **Attack simulation** — Hydra SSH brute force
* **Detection** — Wazuh SIEM alert correlation
* **Analysis** — MITRE ATT&CK mapping, detection gap identification
* **Documentation** — Incident reporting and lab journaling

Designed to replicate real-world security monitoring and analysis in a controlled environment.
