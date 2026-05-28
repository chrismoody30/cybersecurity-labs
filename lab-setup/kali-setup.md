Kali Linux Setup Guide
This document covers the installation and initial configuration of Kali Linux 2025.1 as the attacker machine in this home lab environment.
---
Why Kali Linux
Kali Linux is a Debian-based distribution maintained by Offensive Security, purpose-built for penetration testing and security research. It comes preloaded with hundreds of security tools covering reconnaissance, exploitation, password attacks, wireless testing, and forensics. It is the industry standard for hands-on security practice and is used by penetration testers, red teamers, and security researchers worldwide.
---
Installation Method
Rather than installing from an ISO, this lab uses the pre-built VirtualBox image provided by Kali, which imports directly as a ready-to-use VM with all tools already installed.
Step 1 -- Download the Pre-Built Image
Go to kali.org/get-kali
Select Virtual Machines
Download the VirtualBox image (.ova file)
Step 2 -- Import into VirtualBox
Open VirtualBox
Go to File > Import Appliance
Select the downloaded .ova file
Review the settings and click Import
Wait for the import to complete
Step 3 -- Configure VM Resources
Before booting, adjust the VM settings for better performance:
Select the Kali VM and click Settings
System > Motherboard -- set RAM to 8192 MB
System > Processor -- set CPU cores to 4
Display > Screen -- set Video Memory to 128 MB
Click OK
---
Network Configuration
Kali uses two network adapters in this lab:
Adapter 1 -- NAT
Provides internet access for downloading tools and updates
Configure under Settings > Network > Adapter 1
Attached to: NAT
Adapter 2 -- Host-Only
Connects Kali to Metasploitable on the isolated lab network
Configure under Settings > Network > Adapter 2
Enable the adapter and attach to: VirtualBox Host-Only Ethernet Adapter
---
Initial Configuration
Default Credentials
Username: `kali`
Password: `kali`
> Change these credentials after first login in any environment beyond a local isolated lab.
Step 1 -- Fix Package Repository
On a fresh Kali image the GPG signing key may be outdated. Fix it before updating:
```bash
sudo wget https://archive.kali.org/archive-key.asc -O /etc/apt/trusted.gpg.d/kali-archive-keyring.asc
```
Verify the sources list points to the correct repository:
```bash
sudo nano /etc/apt/sources.list
```
The file should contain:
```
deb http://http.kali.org/kali kali-rolling main non-free contrib
```
Step 2 -- Update the System
```bash
sudo apt update && sudo apt upgrade -y
```
This will take 10-30 minutes on a fresh image depending on how many packages need updating. If the update gets interrupted or stalls, run:
```bash
sudo apt --fix-missing upgrade
```
If specific packages fail with dependency conflicts, use:
```bash
sudo dpkg --configure -a
sudo apt --fix-broken install
sudo apt upgrade --fix-missing -y
```
Step 3 -- Verify Core Tools
After updating, confirm the primary security tools are installed and functional:
```bash
nmap --version
wireshark --version
msfconsole --version
```
Expected versions (as of this lab setup):
Nmap 7.99
Wireshark 4.4.4
Metasploit Framework 6.4.133
---
Disable Screen Lock
To prevent interruptions during long scans or downloads:
Click the top right menu in Kali
Go to Settings > Power Manager
Set display sleep to Never
---
Tools Overview
The following tools are available in Kali and will be used throughout this lab series:
Tool	Category	Purpose
Nmap	Reconnaissance	Network and port scanning
Wireshark	Traffic Analysis	Packet capture and inspection
Metasploit	Exploitation	Vulnerability exploitation framework
Hydra	Password Attacks	Network service brute forcing
John the Ripper	Password Attacks	Hash cracking
Nikto	Web Scanning	Web server vulnerability scanning
tcpdump	Traffic Analysis	Command-line packet capture
Netcat	Networking	Connection testing and data transfer
Aircrack-ng	Wireless	Wireless network security testing
---
Key Lessons Learned
Always run updates immediately after importing a fresh Kali image -- the pre-built images can be months behind and tools may not function correctly until updated
Use `sudo` for all system-level commands -- running without it will produce permission denied errors
The `--fix-missing` flag on apt upgrade is useful when mirrors are out of sync and returning 404 errors on specific packages
Non-essential package conflicts like pyinstaller can be marked as held with `sudo apt-mark hold` to prevent them from blocking the rest of the upgrade
