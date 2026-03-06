# Home SOC Lab

This repository documents the design, build, and investigation exercises from my cybersecurity home lab.

The goal of this lab is to simulate a small Security Operations Center (SOC) environment where attacks can be generated, detected, investigated, and documented.

## Lab Objectives

• Build a virtual SOC environment using Proxmox  
• Monitor network activity using Security Onion  
• Simulate attacks using Kali Linux  
• Investigate alerts using SIEM dashboards  
• Write incident investigation reports  

## Lab Hardware

Primary Server  
Dell Optiplex 990  
i5-2400 CPU  
12GB RAM  
2TB storage  
Proxmox VE hypervisor  

Additional Systems  
Smilodin desktop (SOC dashboard display)  
Dell Optiplex 790 (future firewall)  
MacBook Pro (admin workstation)

## Planned Lab Architecture

Kali Linux (Attacker)

Ubuntu Server (Target)

Windows Workstation (Target)

Security Onion (SIEM / IDS)

Firewall (planned)

## Lab Exercises

Network reconnaissance detection  
SSH brute force investigation  
Web attack simulation  
Malware alert investigation  
Lateral movement simulation

## Repository Structure

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

## Lab Roadmap

The following systems will be added to expand the SOC environment:

- Kali Linux attacker machine
- Security Onion SIEM platform
- Windows Active Directory domain
- Sysmon log collection
- attack simulations
- incident response exercises
