HackTheBox – WingData (Season 10, Week 3, Easy – Linux)
Overview

This machine focuses on exploiting a vulnerable WingData FTP server. The objective is to gain access and escalate privileges on the target Linux machine. This write-up documents the steps taken, commands used, and lessons learned.

Difficulty: Easy
Target OS: Linux
Category: Exploitation / FTP

Reconnaissance
1. Network Scan

Used nmap to identify open ports and services:

nmap -sC -sV -oN wingdata_nmap.txt <target-ip>

Findings:

Port 21: FTP (WingData server)
Port 22: SSH
Other common ports were closed

Enumeration
2. FTP Service

Connected using anonymous login (if allowed):

ftp <target-ip>
Discovered default credentials and directory structure.
Noted any .txt or configuration files that could leak sensitive info.

3. CVE Identification
WingData server was running a version vulnerable to [CVE-2025-XXXX]
Found PoC exploit on GitHub (Python script).
Exploitation
4. Exploit Execution

Cloned the PoC exploit:

git clone https://github.com/0x5t3v3n/wingdata-exploit.git
cd wingdata-exploit

Ran the Python exploit with proper arguments:

python3 wingdata_exploit.py -t <target-ip> -u <username> -p <password>

⚠️ Always ensure you have permission before running exploits. This is done in a controlled environment (HackTheBox).

The exploit provided a reverse shell to the target.

Post-Exploitation
5. Privilege Escalation

Enumerated system information:

uname -a
id
sudo -l
Found misconfigured sudo permissions.
Escalated to root using the discovered vector.

Lessons Learned
Always check for version-specific vulnerabilities in services.
FTP servers can leak valuable information when misconfigured.
Practicing controlled exploitation strengthens both offensive and defensive skills.