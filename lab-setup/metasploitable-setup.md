Metasploitable 2 Setup Guide
This document covers the setup and configuration of Metasploitable 2 as the intentionally vulnerable target machine in this home lab environment.
---
What is Metasploitable 2
Metasploitable 2 is a deliberately vulnerable Linux virtual machine created by Rapid7, the company behind the Metasploit Framework. It is designed specifically as a safe and legal target for practicing penetration testing techniques, vulnerability scanning, and exploitation. It runs Ubuntu 8.04 and contains a wide range of intentionally misconfigured and vulnerable services across multiple attack vectors.
> Metasploitable should NEVER be exposed to the internet or any untrusted network. It is intentionally insecure by design and would be trivially compromised if reachable from outside an isolated lab environment.
---
Vulnerable Services Included
Metasploitable 2 runs numerous intentionally vulnerable services, including:
Service	Port	Vulnerability
FTP (vsftpd 2.3.4)	21	Backdoor command execution
SSH	22	Weak credentials
Telnet	23	Cleartext protocol, weak credentials
SMTP	25	Open relay
HTTP (Apache)	80	Multiple web app vulnerabilities
SMB (Samba)	139/445	Username map script vulnerability
MySQL	3306	No root password
PostgreSQL	5432	Weak credentials
VNC	5900	Weak credentials
IRC (UnrealIRCd)	6667	Backdoor command execution
DVWA	80/dvwa	Damn Vulnerable Web Application
Mutillidae	80/mutillidae	Vulnerable web application
These services provide a broad range of attack surfaces covering network exploitation, web application testing, password attacks, and privilege escalation.
---
Installation
Step 1 -- Download Metasploitable 2
Go to sourceforge.net/projects/metasploitable
Download the zip file
Extract the zip -- you will find a .vmdk file inside (this is the pre-built virtual hard disk)
Step 2 -- Create a New VM in VirtualBox
Open VirtualBox and click New
Configure as follows:
Name: Metasploitable
Type: Linux
Version: Ubuntu (32-bit)
Set RAM to 1024 MB
On the hard disk screen, select Use an existing virtual hard disk file
Click the folder icon and browse to the extracted .vmdk file
Click Finish
> No ISO or installation is required. The .vmdk file is a complete pre-installed operating system that boots directly.
---
Network Configuration
Metasploitable should only have a Host-Only adapter -- no internet access whatsoever.
Select the Metasploitable VM and click Settings
Go to Network > Adapter 1
Set Attached to: Host-Only Adapter
Select VirtualBox Host-Only Ethernet Adapter
Leave Adapter 2 disabled
Click OK
This ensures Metasploitable can only communicate with other VMs on the Host-Only network and has no path to the internet or your real home network.
---
Default Credentials
Username: `msfadmin`
Password: `msfadmin`
These credentials work on multiple services including SSH, FTP, and the console login. Many other services have their own weak or default credentials discoverable through scanning and enumeration.
---
Verifying the Setup
After booting Metasploitable, log in and check its IP address on the Host-Only network:
```bash
ifconfig
```
Look for an IP in the 192.168.56.x range on the eth0 or eth1 interface. This is the address you will target from Kali.
From Kali, verify connectivity:
```bash
ping 192.168.56.xxx
```
If the ping succeeds, both machines are communicating correctly on the Host-Only network and the lab is ready for scanning and exploitation exercises.
---
Lab Network Diagram
```
┌─────────────────────────────────────────────────┐
│              Host Machine (Windows 11)           │
│                   32GB RAM                       │
│                                                  │
│  ┌──────────────────┐    ┌────────────────────┐  │
│  │   Kali Linux     │    │  Metasploitable 2  │  │
│  │   (Attacker)     │    │    (Target)        │  │
│  │                  │    │                    │  │
│  │  Adapter 1: NAT  │    │  Adapter 1:        │  │
│  │  (Internet)      │    │  Host-Only Only    │  │
│  │                  │    │  (No Internet)     │  │
│  │  Adapter 2:      │◄──►│                    │  │
│  │  Host-Only       │    │  192.168.56.xxx    │  │
│  │  192.168.56.xxx  │    │                    │  │
│  └──────────────────┘    └────────────────────┘  │
│                                                  │
│         Host-Only Network: 192.168.56.0/24       │
└─────────────────────────────────────────────────┘
```
---
Key Lessons Learned
Metasploitable uses a .vmdk file rather than an ISO -- no installation process is needed, just point VirtualBox to the existing disk file when creating the VM
Network isolation is critical -- always verify Metasploitable only has a Host-Only adapter before booting it
The machine intentionally runs outdated and vulnerable software -- do not attempt to update or patch it as that defeats the purpose of the lab target
