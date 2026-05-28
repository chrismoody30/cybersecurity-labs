Lab 01 -- Nmap Network Scanning & Port Discovery
Date Completed:
Difficulty: Beginner
Time to Complete:
---
Objective
Use Nmap to perform network reconnaissance against Metasploitable 2, discovering live hosts, open ports, running services, and OS information. This lab establishes the foundational scanning workflow used at the start of any penetration test or vulnerability assessment.
---
Environment
Component	Details
Attacker	Kali Linux 2025.1 (192.168.56.xxx)
Target	Metasploitable 2 (192.168.56.xxx)
Network	Host-Only Isolated (192.168.56.0/24)
---
Tools Used
Tool	Version	Purpose
Nmap	7.99	Network scanning, port discovery, service detection
---
Security+ Connections
Domain 4.3 -- Given a scenario, use the appropriate tool to assess organizational security (network scanners, port scanners)
Domain 2.1 -- Compare and contrast common threat actors and motivations (reconnaissance as the first phase of an attack)
Domain 4.4 -- Explain the importance of using appropriate network monitoring tools (understanding what services are exposed)
---
Background
Network scanning is the first step in nearly every security engagement. Before an attacker or penetration tester can exploit vulnerabilities, they need to understand what is running on a target system -- what ports are open, what services are listening, and what operating system is in use.
Nmap (Network Mapper) is the industry-standard open-source tool for this purpose. It works by sending specially crafted packets to target hosts and analyzing the responses to determine port states, service versions, and OS details. Understanding how Nmap works from the attacker side also directly informs the defensive side -- knowing what information is visible on your network is the first step toward reducing your attack surface.
For Security+, this lab covers vulnerability scanning concepts, reconnaissance techniques, and the use of appropriate security assessment tools.
---
Methodology
Step 1 -- Identify Your IP Address on the Host-Only Network
Before scanning, confirm Kali's IP address on the lab network:
```bash
ip a
```
Look for the Host-Only adapter (typically eth1 or similar) with an IP in the 192.168.56.x range. Note this address -- it confirms which interface is connected to the lab network.
What this does: Verifies your network configuration before starting any scans. Good practice to always confirm your own IP before running tests.
Step 2 -- Host Discovery Scan
Discover what hosts are alive on the lab network:
```bash
nmap -sn 192.168.56.0/24
```
What this does: Sends ping probes to every address in the 192.168.56.0/24 subnet. The `-sn` flag means ping scan only -- no port scanning. Returns a list of live hosts with their IP addresses and MAC addresses.
Step 3 -- Basic Port Scan
Run a basic TCP port scan against Metasploitable:
```bash
nmap 192.168.56.xxx
```
What this does: Scans the 1,000 most common TCP ports on the target. Returns a list of open ports and the service typically associated with each port number.
Step 4 -- Full Port Scan
Scan all 65,535 TCP ports:
```bash
nmap -p- 192.168.56.xxx
```
What this does: A thorough scan of every possible TCP port. Takes longer than the default scan but ensures no open ports are missed. Real attackers and pentesters always run full port scans to find services running on non-standard ports.
Step 5 -- Service and Version Detection
Identify exact service names and version numbers running on open ports:
```bash
nmap -sV 192.168.56.xxx
```
What this does: Sends additional probes to each open port to determine the exact service and version running. Version information is critical for identifying specific vulnerabilities -- a generic "FTP" finding is less useful than knowing it is "vsftpd 2.3.4" which has a known backdoor.
Step 6 -- OS Detection
Attempt to identify the target operating system:
```bash
sudo nmap -O 192.168.56.xxx
```
What this does: Analyzes TCP/IP stack responses to fingerprint the operating system. Requires root/sudo privileges. OS information helps an attacker or pentester narrow down applicable exploits and understand the target environment.
Step 7 -- Comprehensive Scan
Combine service detection, OS detection, default scripts, and verbose output:
```bash
sudo nmap -A -v 192.168.56.xxx
```
What this does: The `-A` flag enables OS detection, version detection, script scanning, and traceroute simultaneously. The `-v` flag increases verbosity so you see results as they come in rather than waiting for the full scan to complete. This is the most informative single Nmap command for initial reconnaissance.
Step 8 -- Save Scan Output
Save results to a file for reference and documentation:
```bash
sudo nmap -A -v -oN metasploitable-scan.txt 192.168.56.xxx
```
What this does: The `-oN` flag saves results in normal human-readable format. Always save scan output -- in real engagements this becomes part of your report, and in a lab environment it lets you reference findings without re-running scans.
---
Results
Open Ports Discovered
(Fill in after running the lab)
```
[Paste nmap output here]
```
Analysis: Document what ports were found open and what they indicate about the attack surface.
Service Versions Identified
(Fill in after running the lab)
Port	Service	Version	Notes
21	FTP	vsftpd 2.3.4	Known backdoor vulnerability
22	SSH	OpenSSH x.x	
23	Telnet		Cleartext protocol
80	HTTP	Apache x.x.x	
OS Detection Result
(Fill in after running the lab)
---
Screenshots
Screenshot	Description
![Scan 1](./screenshots/01-host-discovery.png)	Host discovery results showing Metasploitable IP
![Scan 2](./screenshots/02-port-scan.png)	Full port scan results
![Scan 3](./screenshots/03-service-detection.png)	Service and version detection output
---
Key Findings
(Fill in after running the lab)
Number of open ports discovered
Most interesting services and versions identified
Any services running on non-standard ports
OS identified
---
What I Learned
(Fill in after running the lab)
---
Real-World Applications
Offensive perspective: Nmap is typically the first tool run against a target during a penetration test. The output directly informs which exploits to attempt -- knowing vsftpd 2.3.4 is running immediately points a tester toward a known backdoor exploit in Metasploit.
Defensive perspective: Running Nmap against your own infrastructure is a legitimate and important security practice. IT administrators and security teams use it to audit what services are exposed on their networks, identify unauthorized or forgotten open ports, and verify that firewall rules are working as expected. If you can see it with Nmap, an attacker can too.
Security+ context: Network scanning and port discovery fall under vulnerability scanning concepts in the Security+ exam. Understanding what information these scans reveal -- and how attackers use that information -- is core knowledge for the threats, vulnerabilities, and mitigations domain.
---
References
Nmap Official Documentation
Nmap Cheat Sheet
CompTIA Security+ SY0-701 Exam Objectives -- Domain 4.3, 2.1
