# Home SOC Lab
![Proxmox](https://img.shields.io/badge/Virtualization-Proxmox-blue)
![Wazuh](https://img.shields.io/badge/SIEM-Wazuh-green)
![Kali Linux](https://img.shields.io/badge/Attacker-Kali%20Linux-red)
![Ubuntu](https://img.shields.io/badge/Target-Ubuntu-orange)
![IoT](https://img.shields.io/badge/IoT-Twinkly-yellow)
![DVWA](https://img.shields.io/badge/WebApp-DVWA-purple)

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
* Practice web application attacks using DVWA

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
✔ DVWA installed and configured on Ubuntu  
✔ XSS (Reflected) — cookie theft demonstrated  
✔ XSS (Stored) — persistent payload demonstrated  
⬜ XSS (DOM)  
⬜ SQL Injection  
⬜ Apache log monitoring in Wazuh  
⬜ Network IDS (Suricata)  
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
* Web application attacks (XSS Reflected, XSS Stored)
* Session cookie theft via XSS
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
| 80/dvwa | DVWA | PHP 8.3 / MariaDB |

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
* ETag inode leakage — CVE-2003-1418
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

#### Steps performed
1. Device discovered via nmap ping sweep — identified by Espressif MAC prefix
2. Port scan revealed only port 80 open with custom HTTP firmware
3. Device info retrieved with zero authentication (name, MAC, UUID, firmware, uptime)
4. Auth token obtained using a fake base64 challenge — no real verification performed
5. Full device control achieved — light color changed remotely via curl

**Security findings:**

| Finding | Severity |
|---|---|
| Device info exposed without authentication | Medium |
| Fake challenge accepted — weak mutual auth | High |
| IoT device on flat network with VMs and admin machines | High |

**MITRE ATT&CK:** T1046 – Network Service Discovery, T1078 – Valid Accounts  
**Wazuh detection:** ❌ Not monitored

---

### 5. Web Application Attacks — XSS (DVWA)

Cross-site scripting attacks against DVWA installed on Ubuntu Apache.  
**Date:** 2026-05-04  
**Tool:** Browser (Mac) attacking DVWA at `http://192.168.86.40/dvwa`  
**Security level:** Low

#### DVWA Installation
```bash
sudo apt install -y php php-mysqli php-gd libapache2-mod-php mariadb-server
sudo git clone https://github.com/digininja/DVWA.git /var/www/html/dvwa
sudo cp /var/www/html/dvwa/config/config.inc.php.dist /var/www/html/dvwa/config/config.inc.php
sudo chown -R www-data:www-data /var/www/html/dvwa
sudo chmod -R 755 /var/www/html/dvwa
sudo mysql -u root -e "CREATE DATABASE dvwa; CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd'; GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost'; FLUSH PRIVILEGES;"
sudo systemctl restart apache2
```

#### Attack 5a — XSS Reflected

**What it is:** User input is reflected back into the page without sanitization. The browser executes whatever is injected.

**Proof of concept payload:**
```javascript
<script>alert('XSS')</script>
```
Result: Alert popup confirmed — page is vulnerable.

**Cookie theft payload:**
```javascript
<script>alert(document.cookie)</script>
```
Result: Retrieved live session cookies:
- `PHPSESSID=h0ps1aqovu5qp5nhken63vbskg` — session ID, can be used to hijack the account without a password
- `security=low` — DVWA security level

**Real world impact:** An attacker could replace `alert()` with a script that silently sends the cookie to an attacker-controlled server, instantly hijacking the victim's session.

---

#### Attack 5b — XSS Stored

**What it is:** Malicious script is saved to the database and executes automatically for every user who visits the page — no link clicking required.

**Payload injected into guestbook message field:**
```javascript
<script>alert('Stored XSS!')</script>
```
Result: Alert fired on submission AND on every subsequent page load — confirmed persistent storage in database.

**Real world impact:** One payload compromises every visitor automatically. This is the mechanism behind the 2005 Samy MySpace worm which spread to 1 million profiles in 20 hours.

**Comparison:**

| | Reflected XSS | Stored XSS |
|---|---|---|
| Script location | URL parameter | Database |
| Who gets hit | Only users who click the link | Every visitor automatically |
| Persistence | One time | Until removed from database |
| Danger level | Medium | High |

**MITRE ATT&CK:** T1059.007 – JavaScript, T1185 – Browser Session Hijacking  
**Wazuh detection:** ❌ Not monitored (Wazuh not active this session)

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
| XSS attacks | browser | ❌ | Apache logs not configured |

---

## SOC Investigation Reports

| Report | Description |
|---|---|
| `incident-reports/ssh-brute-force.md` | SSH brute force attack detection via Wazuh |

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
- [x] DVWA installation and configuration
- [x] XSS Reflected — proof of concept and cookie theft
- [x] XSS Stored — persistent payload and database injection

### Planned
- [ ] XSS DOM — third XSS variant
- [ ] XSS Medium difficulty — filter bypass
- [ ] SQL Injection
- [ ] Fail2Ban on Ubuntu
- [ ] Wazuh active response
- [ ] Apache log monitoring in Wazuh
- [ ] Network IDS with Suricata
- [ ] Metasploit — structured exploitation framework
- [ ] Custom Wazuh detection rules
- [ ] Windows endpoint monitoring
- [ ] Network segmentation / VLANs (IoT isolation)
- [ ] OPNsense firewall on Optiplex 790
- [ ] OT/ICS security exploration (OpenPLC, Modbus)

---

## Practice Platforms & Resources

| Platform | Focus | Cost |
|---|---|---|
| PortSwigger Web Security Academy | Web attacks, XSS, SQLi | Free |
| HackTheBox | CTF style, all categories | Free tier |
| TryHackMe | Beginner friendly, guided paths | Free tier |
| PentesterLab | Web focused, XSS/SQLi | Free tier |
| WebGoat | Web attacks, runs locally | Free |
| picoCTF | Beginner CTF, Carnegie Mellon | Free |
| CTFtime.org | CTF competition calendar | Free |
| DEFCON CTF | Advanced CTF competition | Free |

---

## Commands Reference

| Command | Purpose |
|---|---|
| `nmap -sn 192.168.86.0/24` | Ping sweep — find live hosts |
| `nmap -sV <ip>` | Service version detection |
| `nmap -A <ip>` | Aggressive scan (OS + scripts) |
| `gobuster dir -u <url> -w <wordlist>` | Web directory brute force |
| `nikto -h <url>` | Web vulnerability scan |
| `hydra -l <user> -P <wordlist> ssh://<ip>` | SSH brute force |
| `curl http://<ip>/xled/v1/gestalt` | Twinkly device info (no auth) |
| `curl -X POST http://<ip>/xled/v1/login` | Twinkly auth token request |

---

## Troubleshooting

**Wazuh agent disconnected after reboot** — Check the manager IP in `/var/ossec/etc/ossec.conf` on the Ubuntu agent. DHCP can reassign the Wazuh VM's IP between sessions.

**SSH connection refused to Kali** — Check that sshd is running: `sudo systemctl start ssh`. Confirm current IP with `ip a` from the Proxmox console.

**DVWA login not working** — Default credentials are `admin / password`. Reset via Setup page if needed.

---

*Lab maintained and documented with AI assistance via Claude (claude.ai)*
