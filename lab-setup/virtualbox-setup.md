VirtualBox Setup Guide
This document covers the installation and configuration of Oracle VirtualBox 7.2 as the hypervisor for this cybersecurity home lab environment.
---
System Requirements
Component	Minimum	My Setup
RAM	8GB	32GB
CPU	Dual-core with VT-x/AMD-V	Intel Core i9-12900K
Storage	50GB free	SSD
OS	Windows 10/11	Windows 11
VirtualBox Version	7.2+	7.2
---
Installation
Step 1 -- Download VirtualBox and Extension Pack
Go to virtualbox.org/wiki/Downloads
Download VirtualBox 7.2 for Windows
Download the VirtualBox 7.2 Extension Pack (same version number is critical)
Step 2 -- Install VirtualBox
Right-click the installer and select Run as Administrator
Follow the installation wizard
When prompted about network interfaces and a warning that your internet will briefly disconnect -- click Yes. This step installs the critical VirtualBox network drivers (vboxnetadp and vboxnetflt) and must not be skipped
Complete the installation and restart your PC
Step 3 -- Install the Extension Pack
Open VirtualBox as Administrator
Go to File > Tools > Extension Pack Manager
Click Install and select the Extension Pack file you downloaded
Accept the license agreement and let it install
> The Extension Pack adds USB 2.0/3.0 support, improved network drivers, and disk encryption support. It must match your VirtualBox version exactly.
---
Resolving Common Issues
VirtualBox Won't Open
If VirtualBox silently fails to open with no error message, Hyper-V is likely conflicting with it.
Fix -- Disable Hyper-V via Command Prompt (Administrator):
```
bcdedit /set hypervisorlaunchtype off
dism /online /disable-feature /featurename:Microsoft-Hyper-V-All
dism /online /disable-feature /featurename:VirtualMachinePlatform
dism /online /disable-feature /featurename:HypervisorPlatform
```
Restart after running all four commands.
Also check Windows Security > Device Security > Core Isolation > Memory Integrity and disable it if enabled. This can also block VirtualBox from launching.
Host-Only Network Adapter Fails (VERR_INTNET_FLT_IF_NOT_FOUND)
This error means the vboxnetflt network filter driver is missing or not registered.
Verify the services are running:
```
sc query vboxnetadp
sc query vboxnetflt
```
If vboxnetflt shows as missing, the network drivers were not installed correctly. The fix is a clean reinstall of VirtualBox with the installer run as Administrator, making sure to accept the network interface installation prompt during setup.
---
Network Configuration
This lab uses two network adapter types:
NAT (Network Address Translation)
Gives the VM internet access through the host machine
Used on Kali Linux Adapter 1
VMs on NAT cannot communicate with each other directly
Host-Only Adapter
Creates a private isolated network between VMs
No internet access, no connection to your real network
Used for lab traffic between Kali and Metasploitable
Configured under File > Tools > Network Manager
Host-Only Adapter Settings:
Setting	Value
IPv4 Address	192.168.56.1
IPv4 Network Mask	255.255.255.0
DHCP Server	Enabled
Server Address	192.168.56.100
Lower Address Bound	192.168.56.101
Upper Address Bound	192.168.56.254
---
VM Resource Allocation
With 32GB of host RAM, resources are allocated as follows:
VM	RAM	CPU Cores	Purpose
Kali Linux	8192 MB	4	Attacker machine
Metasploitable 2	1024 MB	1	Target machine
Host OS Reserve	~6GB	--	Windows 11
---
Key Lessons Learned
Always run the VirtualBox installer as Administrator -- without it the network drivers (vboxnetflt) will not install properly and Host-Only networking will fail
The Extension Pack must match the exact VirtualBox version or it will throw a version mismatch error
Hyper-V and VirtualBox cannot run simultaneously on Windows -- Hyper-V must be fully disabled including Virtual Machine Platform and Windows Hypervisor Platform features
Memory Integrity under Windows Security Core Isolation can also silently block VirtualBox even after Hyper-V is disabled
