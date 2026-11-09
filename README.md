# Cybersecurity Portfolio — 0x5t3v3n

Hands-on cybersecurity portfolio focused on **penetration testing, offensive security, defensive security, and detection engineering**.

I build practical labs and document the process from **initial reconnaissance through exploitation, post-exploitation, detection, and analysis**. My goal is to develop into a cybersecurity professional who understands both how attacks work and how defenders can detect and respond to them.

## Current Focus

* Penetration testing and vulnerability assessment
* Active Directory security
* Web application security
* Network reconnaissance and enumeration
* Privilege escalation
* Security monitoring and detection engineering
* Windows event analysis
* Incident response fundamentals
* Red-team and blue-team lab exercises

---

# Technical Skills

## Offensive Security

| Area               | Tools / Techniques                                  |
| ------------------ | --------------------------------------------------- |
| Reconnaissance     | Nmap, NetExec, Gobuster, FFUF                       |
| Web Security       | Burp Suite, SQL Injection, LFI, XSS                 |
| Exploitation       | Metasploit, manual exploitation                     |
| Active Directory   | BloodHound, Impacket, Kerberos                      |
| Credential Attacks | Kerberoasting, password cracking                    |
| Post-Exploitation  | Enumeration, privilege escalation, lateral movement |

## Defensive Security

| Area                  | Tools / Techniques                        |
| --------------------- | ----------------------------------------- |
| SIEM                  | Wazuh                                     |
| Windows Monitoring    | Windows Event Logs, Sysmon                |
| Detection Engineering | Wazuh rules, Sigma concepts, MITRE ATT&CK |
| Threat Hunting        | Event analysis, IOC identification        |
| Incident Response     | Alert triage and investigation            |
| Malware Analysis      | Static and dynamic analysis fundamentals  |

## Programming & Scripting

* Python
* Bash
* PowerShell

---

# Home Lab

My home lab is a virtualized environment designed to simulate both **attacker and defender workflows**.

### Environment

* Kali Linux — attacker
* Windows Server — Active Directory domain controller / victim
* Ubuntu — victim system
* Ubuntu — Wazuh server
* VirtualBox
* Wazuh
* Sysmon
* Active Directory

The lab allows me to generate attacks against controlled systems and then investigate the resulting telemetry from the defensive side.

## Home Lab Projects

| Project                                                                                   | Focus                                           |
| ----------------------------------------------------------------------------------------- | ----------------------------------------------- |
| [AD Environment Setup](./HomeLab/AD-Environment-Setup/)                                   | Active Directory domain and lab environment     |
| [Wazuh Server](./HomeLab/Creating-Wazuh-Server/)                                          | Wazuh SIEM deployment                           |
| [Network Topology](./HomeLab/Network-Topology/)                                           | Virtualized security lab architecture           |
| [Kerberoasting](./HomeLab/Kerberoasting/)                                                 | Active Directory credential attack and analysis |
| [Privilege Escalation](./HomeLab/Privilege-Escalation/)                                   | Simulated privilege escalation activity         |
| [Wazuh Agent Deployment](./HomeLab/AD-Environment-Setup/Wazuh-Agent-Deployment-WIN-DC01/) | Windows endpoint monitoring                     |

## Featured Home Lab Exercise

### Kerberoasting

A controlled Active Directory attack beginning with low-privileged domain credentials, followed by:

1. Network and domain reconnaissance
2. Active Directory enumeration
3. SPN discovery
4. Kerberos service-ticket requests
5. TGS hash extraction
6. Offline password cracking
7. Credential recovery
8. Documentation of the attack chain

[Read the Red Team Write-Up](./HomeLab/Kerberoasting/Kerberoasting-RedTeam-Writeup.md)

---

# Detection Engineering

My defensive work focuses on understanding what an attack looks like from the perspective of the systems being monitored.

## RDP Brute-Force Detection

Simulated repeated failed RDP authentication attempts and investigated Windows Event ID 4625 through Wazuh.

[View Project](./Bruteforce_detection_sim/)

## Sysmon & Wazuh Detection Engineering

Configured Sysmon telemetry and Wazuh rules to improve endpoint visibility and detection capabilities.

[View Project](./Detection_Engineering/)

---

# Hack The Box

Hands-on penetration testing practice using retired machines and challenge environments.

## Machines

### Orion

Web application and Linux penetration testing exercise involving reconnaissance, vulnerability identification, exploitation, and privilege escalation.

[View Write-Up](./HTB/Machines/Orion/)

## Season 10

| Machine     | Skills Practiced                  | Write-Up                                               |
| ----------- | --------------------------------- | ------------------------------------------------------ |
| WingData    | Enumeration, exploitation         | [Write-Up](./HTB/Season_10/WingData/WingData.md)       |
| Eighteen    | Web enumeration, exploitation     | [Write-Up](./HTB/Season_10/Eighteen/Eighteen.md)       |
| Interpreter | Enumeration, privilege escalation | [Write-Up](./HTB/Season_10/Interpreter/Interpreter.md) |
| Pterodactyl | Web security, exploitation        | [Write-Up](./HTB/Season_10/Pterodactyl/Pterodactyl.md) |
| Facts       | Web enumeration and exploitation  | [Write-Up](./HTB/Season_10/facts/facts.md)             |

## Starting Point

| Machine | Skills Practiced                       | Write-Up                                        |
| ------- | -------------------------------------- | ----------------------------------------------- |
| Fawn    | FTP enumeration, service discovery     | [Write-Up](./HTB/Starting%20Point/Fawn/fawn.md) |
| Meow    | Telnet enumeration, basic exploitation | [Write-Up](./HTB/Starting%20Point/Meow/meow.md) |

---

# TryHackMe

Hands-on security labs covering web security, enumeration, exploitation, and security fundamentals.

| Room                  | Skills Practiced                 | Write-Up                                       |
| --------------------- | -------------------------------- | ---------------------------------------------- |
| TakeOver              | Subdomain enumeration            | [Write-Up](./THM/TakeOver/TakeOver.md)         |
| Lo-Fi                 | Local File Inclusion             | [Write-Up](./THM/Lo-Fi/Lo-Fi.md)               |
| Lookup                | Web enumeration and exploitation | [Write-Up](./THM/Lookup/Lookup.md)             |
| web-app               | Web application security         | [Write-Up](./THM/web-app/web-app.md)           |
| Advent of Cyber — XSS | Cross-Site Scripting             | [Write-Up](./THM/AoC-2025-XSS/AoC-2025-XSS.md) |
| PHP2Shell             | PHP engine internals, memory corruption, CVE analysis | [Write-Up](./THM/PHP2Shell/PHP2Shell.md) |

---

# Other Projects

## Hacker AI Bot

Python-based security tooling experimentation involving reconnaissance and penetration-testing workflows.

[View Project](./hacker_AI_Bot/)

---

# Certifications & Training

## Completed

* TCM Security — Practical Help Desk Fundamentals
* Cisco Networking Academy — Ethical Hacker
* Cisco Networking Academy — Introduction to Cybersecurity
* Cisco Networking Academy — Packet Tracer
* EC-Council — Pentesting Fundamentals
* EC-Council — Bash Scripting
* EC-Council — Ethical Hacking Essentials

## Currently Studying

* CompTIA Security+
* Hack The Box Academy
* TryHackMe
* Python

---

# Career Direction

My current career path is focused on progressing into professional cybersecurity roles, beginning with practical IT and security experience and progressing toward **penetration testing and red-team operations**.

Long term, I want to develop strong capability across both offensive and defensive security so that I can:

* Understand how attack chains work
* Identify vulnerabilities
* Exploit systems in controlled environments
* Analyze the telemetry generated by attacks
* Develop and improve detections
* Investigate security events
* Help organizations strengthen their security posture

---

# Portfolio

* GitHub: [0x5t3v3n](https://github.com/0x5t3v3n)

---

*Last updated: September 2026*
