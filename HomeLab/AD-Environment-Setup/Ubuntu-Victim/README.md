# Ubuntu Victim — Build & Onboarding

Part of the `AD-Environment-Setup` series for the `aizen.lab` home SOC lab (SOCNET, `192.168.100.0/24`).

This write-up covers standing up a second victim host — an Ubuntu 24.04 LTS Desktop VM (`ubuntu01` / `Ubuntu_victim0`) — configuring it for the lab's SOCNET topology, deploying a deliberately vulnerable web application (DVWA) for initial-access practice, and enrolling it as a Wazuh agent so the blue-team side of future attacks has full log visibility.

## Goal

Add a second, independent attack target to the lab that behaves like a real HTB-style box: a Kali attacker pings it, runs an nmap scan, enumerates a web service on port 80, finds a vulnerability, exploits it for initial access, and escalates privileges from there — with **no dependency on the Windows domain controller (`WIN-DC01`) already being compromised**.

## Design Decisions

Before building anything, two design questions were worked through:

**1. Should this box be domain-joined (lateral movement) or standalone (initial access)?**

Two options were considered:
- Domain-join the box via `realmd`/`sssd`, extending the AD forest and creating a lateral-movement target reachable only with an existing domain credential.
- Keep it fully standalone with a public-facing vulnerable service, so a Kali attacker can compromise it from zero credentials — same as a typical HTB Linux box.

The second was chosen. The domain-join path was appealing for tying into the broader `aizen.lab` narrative, but it assumes prior compromise of `WIN-DC01`, which conflicts with the goal of an independent, fully self-contained target. The domain-joined / lateral-movement angle is deferred for a future build.

**2. What should the vulnerability be?**

Two options were considered for the port 80 service:
- **DVWA** (Damn Vulnerable Web App) — fast to stand up, vulnerabilities are labeled and toggled via a settings page.
- **Outdated real-world software with a known CVE** (e.g. an old WordPress + vulnerable plugin) — slower to prep, but produces a genuinely blind discovery experience: real version fingerprinting, real CVE lookup, real public PoC exploitation.

**DVWA was chosen for this build.** It's faster to get practicing with common vuln classes (SQLi, command injection, file upload, etc.). The trade-off is knowingly, since DVWA's vulnerabilities are pre-labeled — this isn't a "blind" HTB-style exercise the way a real CVE box would be.

> **Note for the next VM:** once DVWA has been used to practice and learn the core vuln classes, the next victim VM should use **option 2 — outdated real-world software with a known CVE** — to get the full blind discovery experience (fingerprint → CVE lookup → adapt public exploit) that mirrors real HTB methodology.

## Build Steps

### 1. Create the VM

New VM in VirtualBox, Ubuntu 24.04 LTS Desktop, unattended installation.

![New VM — name and OS selection](screenshots/01-new-vm-name-os-selection.png)

Adjusted defaults for a Desktop install (needs more headroom than a minimal server build):

![Hardware — 2 CPUs](screenshots/02-vm-hardware-2cpu.png)
![Hard disk — 45 GB](screenshots/03-vm-disk-45gb.png)
![Unattended install — user, hostname, Guest Additions enabled](screenshots/04-unattended-install-guest-additions.png)

- **User:** `nurgle`
- **Hostname:** `ubuntu01`
- **Guest Additions:** enabled (saves a manual install/mount step later)

Unattended install running:

![Ubuntu installer progress](screenshots/05-ubuntu-installer-progress.png)

First boot, into the standard Ubuntu setup wizard:

![Ubuntu first-boot welcome screen](screenshots/06-ubuntu-first-boot-welcome.png)

### 2. Install DVWA (LAMP stack)

Ran the following, in order, on the fresh desktop:

```bash
# Update the system
sudo apt update && sudo apt upgrade -y

# Install the LAMP stack + required PHP extensions
sudo apt install -y apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php git

# Start and enable Apache + MariaDB
sudo systemctl enable --now apache2
sudo systemctl enable --now mariadb
```

![apt update/upgrade](screenshots/07-apt-update-upgrade.png)
![LAMP stack install](screenshots/08-lamp-stack-install.png)
![Apache + MariaDB enabled](screenshots/09-apache-mariadb-enabled.png)

Created the DVWA database and a dedicated MariaDB user:

```bash
sudo mysql -u root
```
```sql
CREATE DATABASE dvwa;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

![MariaDB — create DVWA database and user](screenshots/10-mariadb-create-dvwa-database.png)

Cloned DVWA into the Apache web root and configured it:

```bash
sudo git clone https://github.com/digininja/DVWA.git /var/www/html/DVWA
sudo cp /var/www/html/DVWA/config/config.inc.php.dist /var/www/html/DVWA/config/config.inc.php
# edited config.inc.php: db_user = dvwa, db_password = p@ssw0rd, db_database = dvwa

sudo chown -R www-data:www-data /var/www/html/DVWA
sudo chmod -R 755 /var/www/html/DVWA

sudo phpenmod mysqli gd
sudo systemctl restart apache2
ip a
```

![Copying config.inc.php](screenshots/11-dvwa-config-copy.png)
![chown www-data](screenshots/12-dvwa-chown-www-data.png)
![chmod 755](screenshots/13-dvwa-chmod-755.png)
![phpenmod + restart + ip a](screenshots/14-dvwa-phpenmod-and-ip.png)

Browsing to the raw NAT IP (`10.0.2.15`) from the VM's own browser resolved as a search query rather than a page load — using `http://localhost/DVWA/setup.php` worked instead. From there, **"Create / Reset Database"** initialized the schema, and logging in with the default credentials (`admin` / `password`) confirmed the install:

![DVWA setup.php checklist](screenshots/15-dvwa-setup-php-page.png)
![DVWA welcome page, logged in](screenshots/16-dvwa-welcome-dashboard.png)

DVWA is intentionally left on default credentials — leaving default creds in place is itself a realistic finding for the lab.

### 3. Networking — SOCNET static IP

Following the lab's established two-adapter pattern (matching `WIN-DC01` and Kali):

- **Adapter 1:** NAT — internet access for package installs
- **Adapter 2:** Internal Network (`SOCNET`) — isolated lab traffic on `192.168.100.0/24`

![VM Network — Adapter 1, NAT](screenshots/17-vm-network-adapter1-nat.png)
![VM Network — Adapter 2, SOCNET](screenshots/18-vm-network-adapter2-socnet.png)

`enp0s8` (the SOCNET interface) had already picked up `192.168.100.19` via DHCP. To make it static, since this VM runs NetworkManager (not raw netplan) as the renderer, the static IP was configured directly through `nmcli` rather than editing netplan YAML:

```bash
nmcli con show
```

![nmcli con show output](screenshots/19-nmcli-con-show-output.png)

```bash
sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.100.19/24
sudo nmcli con mod "Wired connection 1" ipv4.method manual
sudo nmcli con down "Wired connection 1" && sudo nmcli con up "Wired connection 1"
```

Confirmed static with `ip a` (note `valid_lft forever`, no longer DHCP-leased):

![Static IP confirmed](screenshots/20-static-ip-confirmed.png)

Verified reachability to the Wazuh manager:

```bash
ping 192.168.100.10
```

![Ping to Wazuh manager — success, 0% loss](screenshots/21-ping-wazuh-manager-success.png)

### 4. Wazuh Agent Enrollment

```bash
# curl wasn't installed by default on this build — installed first
sudo apt install -y curl
```

![Installing curl](screenshots/22-apt-install-curl.png)

```bash
# Import the Wazuh GPG key and add the repo
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && sudo chmod 644 /usr/share/keyrings/wazuh.gpg
```

![Wazuh GPG key import success](screenshots/23-wazuh-gpg-key-import-success.png)

```bash
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt update

# Install the agent, pointing it at the manager
sudo WAZUH_MANAGER='192.168.100.10' apt install -y wazuh-agent
```

![wazuh-agent install output — v4.14.7-1](screenshots/24-wazuh-agent-apt-install-output.png)

Verified the manager address landed correctly in the config (since `sudo VAR=value command` doesn't always pass environment variables the same way `VAR=value sudo command` does):

```bash
sudo cat /var/ossec/etc/ossec.conf | grep -A 2 "<server>"
```

Confirmed `192.168.100.10:1514`. Enabled and started the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
sudo systemctl status wazuh-agent
```

![Wazuh agent active/running, all sub-processes started cleanly](screenshots/25-wazuh-agent-service-active-status.png)

Unlike the port 1515 registration issue hit during the `WIN-DC01` deployment, this agent registered with the manager automatically — no manual `manage_agents` step was needed.

### 5. Dashboard Cleanup

Before this VM was added, the dashboard was carrying two stale agents left over from earlier lab iterations:
- `win_victim` (ID 001) — superseded once `WIN-DC01` was consolidated to also serve as the sole Windows victim
- `win10-01` (ID 003) — the abandoned Windows 10 Home VM that could not join the domain

![Dashboard before cleanup — 4 agents, two stale](screenshots/26-wazuh-dashboard-agents-before-cleanup.png)

Both were removed via `manage_agents` on the Wazuh manager, leaving a dashboard that accurately reflects the current lab topology:

![Dashboard after cleanup — WIN-DC01 and ubuntu01 only](screenshots/27-wazuh-dashboard-final-clean-agents.png)

## Final State

| Item | Value |
|---|---|
| Hostname | `ubuntu01` |
| VM name | `Ubuntu_victim0` |
| OS | Ubuntu 24.04.4 LTS |
| SOCNET IP (static) | `192.168.100.19/24` |
| Web service | DVWA on Apache/MariaDB, default creds (`admin`/`password`) |
| Wazuh agent | Enrolled, active, ID 004 |
| Domain-joined | No — standalone, self-contained target |

The VM can be pinged and port-scanned from Kali independently of any other host in the lab, and Wazuh has full log visibility into it for the eventual dual (red team / blue team) write-up.

## Next Steps

- Begin the attack phase from Kali: `nmap` scan → enumerate DVWA → exploit → shell → local privilege escalation, with corresponding Wazuh detection documented alongside.
- Once DVWA has served its purpose for practicing core vuln classes, build a second, independent victim VM using **outdated real-world software with a known CVE** for a true blind-discovery exercise.
