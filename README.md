# security-SOC-lab
"A hands-on enterprise SOC home lab featuring Active Directory, Wazuh SIEM, Security Onion, and attack simulations on a virtualized business network."
🏢 Part 1: Active Directory — Domain Controller Setup
Overview
The Active Directory (AD) server is the foundation of our SOCLab network. It acts as the Domain Controller (DC), responsible for centralized authentication, user account management, group policies, DNS, and DHCP across the entire lab environment.
In a real enterprise, the Domain Controller is one of the most critical and targeted systems — making it essential for any SOC analyst to understand how it works, how to configure it, and how to monitor it for threats.
Domain: corp.soc-lab-dc.com
Server IP: 10.0.2.5
OS: Windows Server 2025 Standard Evaluation
Hostname: WIN-1MD9MNNGLGQ
<img width="395" height="452" alt="Screenshot 2026-04-18 150157 - Copy" src="https://github.com/user-attachments/assets/f338b125-a945-46fd-8a3c-654a00aa6556" />


🖥️ Step 1: Create the Virtual Machine in VirtualBox
Before installing Windows Server, we set up the NAT Network in VirtualBox so all our VMs can communicate with each other.
Network Created:
Name: project-SOC-lab
IPv4 Prefix: 10.0.2.0/24
DHCP: Enabled 
<img width="1270" height="998" alt="Screenshot 2026-04-18 130611" src="https://github.com/user-attachments/assets/9eafa7fa-3e6b-4d31-99af-13b8030d2569" />
Then i create a vm with 2cpu and 4gigs of ram and 50GB of storage 

🔧 Step 2: Install Active Directory Domain Services (AD DS)
<img width="1027" height="772" alt="Screenshot 2026-04-18 143606" src="https://github.com/user-attachments/assets/34fa44b4-1eb6-4dbb-97de-e3d0a476ed0a" />

Once Windows Server 2025 was installed and booted, we opened Server Manager and used the Add Roles and Features Wizard to install the required roles.
Roles Installed:

✅ Active Directory Domain Services (AD DS)
✅ DHCP Server
✅ DNS Server

Steps:

Open Server Manager → Click Manage → Add Roles and Features
Click Next on the "Before You Begin" page
Select Role-based or feature-based installation

Select the local server: WIN-1MD9MNNGLGQ at 10.0.2.5
Check Active Directory Domain Services, DHCP Server, and DNS Server
Click Install and wait for completion 
<img width="781" height="558" alt="Screenshot 2026-04-18 151940 - Copy" src="https://github.com/user-attachments/assets/e829eb86-5f4b-44ea-8598-2ab837ca8670" />

🌐 Step 3: Promote Server to Domain Controller
After installing the AD DS role, we promoted the server to a Domain Controller by creating a new forest.
Configuration:

Root Domain Name: corp.soc-lab-dc.com
Forest Functional Level: Windows Server 2025
DNS Server: ✅ Enabled
Global Catalog: ✅ Enabled

Once promotion completed, the server restarted and Server Manager confirmed all four roles were active:
RoleStatusAD DS✅ OnlineDHCP✅ OnlineDNS✅ OnlineFile and Storage Services✅ Online
<img width="2556" height="1076" alt="Screenshot 2026-04-18 153511" src="https://github.com/user-attachments/assets/096054b9-1671-45d4-a276-1411a28d3400" />


🌍 Step 4: Configure DNS Forwarder
To allow the DC to resolve external internet addresses (like google.com) while also handling internal domain resolution, we configured a DNS Forwarder.

Open DNS Manager from Server Manager
Right-click the server → Properties → Forwarders tab
Add Google's public DNS: 8.8.8.8
<img width="754" height="510" alt="Screenshot 2026-04-18 161136" src="https://github.com/user-attachments/assets/19de5006-7ee2-45f3-81fe-339345923de4" />

Verification — DNS and Connectivity Test:
We verified the setup using PowerShell:
<img width="1110" height="626" alt="Screenshot 2026-04-18 161311" src="https://github.com/user-attachments/assets/4169b5b9-94a5-4ddd-a723-c3255da20f4d" />

📡 Step 5: Configure DHCP Scope
We configured a DHCP scope so the DC can automatically assign IP addresses to client machines joining the domain.
DHCP Scope Settings:

Scope Name: project-home-lab-ac
IP Range: 10.0.2.x range
Default Gateway (Router): 10.0.2.1

Steps:

Open DHCP Manager from Server Manager
Expand IPv4 → Right-click → New Scope Wizard
Set scope name and IP range
Set default gateway to 10.0.2.1
Authorize the DHCP server 
<img width="735" height="651" alt="Screenshot 2026-04-18 161942" src="https://github.com/user-attachments/assets/b11cfe05-fe76-45b6-bb87-7d91d55c4462" />

and lastly i have added two user 

📌 About This Project
This project documents the setup of a Windows 11 client machine configured with a static IP address and joined to an Active Directory domain (corp.soc-lab-dc.com). This is the foundation of my SOC home lab — designed to simulate a real enterprise network environment for blue team practice, log analysis, and SIEM monitoring.
What this lab simulates:

A real corporate Active Directory environment
Domain-joined endpoints that generate Windows Event Logs
A foundation for Wazuh/Splunk SIEM integration and alert monitoring


🖥️ Lab Environment
ComponentDetailsClient OSWindows 11Machine Nameproject-homelab-winclientDomaincorp.soc-lab-dc.comClient IP10.0.2.101 Subnet Mask255.255.255.0Default Gateway10.0.2.1DNS Server10.0.2.5 (Domain Controller)Domain User steve@corp.soc-lab-dcNetBIOS NamePROJECT-HOMELAB
🔧 What I Did — Step by Step
Step 1: Windows 11 Installation
Installed a fresh Windows 11 virtual machine to serve as the domain client endpoint. Configured initial privacy settings with minimal data sharing — a security best practice for lab environments.
Step 2: Static IP Configuration
Configured a static IP address on the Windows client to ensure reliable communication with the Domain Controller:

IP Address: 10.0.2.101
Subnet Mask: 255.255.255.0
Default Gateway: 10.0.2.1
Preferred DNS: 10.0.2.5 (pointing to the DC so domain resolution works)
<img width="975" height="730" alt="Screenshot 2026-04-19 165005" src="https://github.com/user-attachments/assets/5045a158-acc2-41cf-be02-7f7060c50a5a" />
Step 3: Domain Join
Joined the Windows 11 client to the Active Directory domain corp.soc-lab-dc.com:

Navigated to System Properties → Computer Name → Change
Set the DNS Suffix to corp.soc-lab-dc.com
Set NetBIOS name to PROJECT-HOMELAB
Selected Domain membership and entered corp.soc-lab-dc.com
Authenticated with domain admin credentials (steve@corp.soc-lab-dc)

Step 4: Verification
Verified the domain join was successful using ipconfig /all in Command Prompt:

Host Name: project-homelab-winclient
Primary DNS Suffix: corp.soc-lab-dc.com
DNS Suffix Search List: corp.soc-lab-dc.com
DNS Server: 10.0.2.5
Confirmed: "Welcome to the corp.soc-lab-dc.com domain" ✅
<img width="972" height="604" alt="Screenshot 2026-04-19 173051" src="https://github.com/user-attachments/assets/1c562c7b-a8a4-40f5-baec-3035b3d33c79" />
🧠 Key Concepts Learned

Active Directory — centralized identity and access management for enterprise networks
Static IP configuration — why endpoints in a lab need fixed addresses
DNS and domain resolution — how the client finds and communicates with the Domain Controller
Domain joining process — authentication flow using domain admin credentials
NetBIOS naming — legacy naming system still used for backward compatibility
ipconfig /all — how to verify network configuration and domain membership from the command line


🔜 What's Next in This Lab
This domain-joined client is the foundation. Here's what I'm building on top of it:

 Install and configure Wazuh Agent on the Windows client
 Forward Windows Event Logs to Wazuh SIEM
 Simulate brute force login attacks and detect them in Wazuh
 Configure Sysmon for enhanced endpoint telemetry
 Map detections to MITRE ATT&CK framework
 Add a Kali Linux attacker machine to simulate real threats

This is Part 2 of my SOC Home Lab series. After joining a Windows 11 client to my Active Directory domain (corp.soc-lab-dc.com) in Part 1, I now join an Ubuntu Linux machine to the same domain using Samba Winbind.
In enterprise environments, Linux servers are commonly domain-joined so that Active Directory users can authenticate across both Windows and Linux systems. This is a critical concept for SOC analysts — because domain-joined Linux machines generate authentication logs and events that flow into your SIEM.
Reference documentation: server-world.info
Two Methods to Join Linux to Active Director
1- sssd + realmd Simpler setup, modern environments
2- Samba Winbind ✅ (used in this project) Full Samba integration, more control, enterprise standard
first thing i did was installing linux using vitrualbox after i did i start with configuring static ip and dns on ubuntu
Set a static IP address and point DNS to the Domain Controller so Ubuntu can resolve the domain name
Via GUI (Network Manager → IPv4 → Manual):
IP Address:  10.0.2.103
Netmask:     255.255.255.0
Gateway:     10.0.2.1
DNS:         10.0.2.5
<img width="754" height="580" alt="Screenshot 2026-04-20 141104" src="https://github.com/user-attachments/assets/7e6942b9-fd05-4eb8-bac9-8bb21161c5af" />

Step 2: Install Samba Winbind Packages 
sudo apt update
sudo apt -y install winbind libpam-winbind libnss-winbind krb5-config samba-dsdb-modules samba-vfs-modules
<img width="1207" height="138" alt="Screenshot 2026-04-20 150123" src="https://github.com/user-attachments/assets/f38d6df8-5bb1-40be-8f12-a97331b91f13" />

Step 3: Backup & Replace smb.conf
Move the default Samba config to preserve it, then create a fresh one:
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.org
<img width="1187" height="88" alt="Screenshot 2026-04-20 150324" src="https://github.com/user-attachments/assets/6f36fda0-3db5-48b7-8eff-7d680860e0f5" />

sudo nano /etc/samba/smb.conf
<img width="1194" height="110" alt="Screenshot 2026-04-20 150439" src="https://github.com/user-attachments/assets/079fe1a5-bcb5-435a-9f1f-03300683b147" />

w<img width="748" height="393" alt="Screenshot 2026-04-20 150748" src="https://github.com/user-attachments/assets/b8b96ad2-3364-41f4-a9a2-600b908845c1" />
rite paste this with realm of you active directory 

[global]
  kerberos method = secrets and keytab
  realm = CORP.SOC-LAB-DC.COM
  workgroup = CORP
  security = ads
  template shell = /bin/bash
  winbind enum groups = Yes
  winbind enum users = Yes
  winbind separator = +
  idmap config * : rangesize = 100000
  idmap config * : range = 1000000-19999999
  idmap config * : backend = autorid

Step 4: Configure nsswitch.conf
Add winbind to passwd and group lookups so Linux resolves AD users:
sudo nano /etc/nsswitch.conf
passwd:   files systemd sss winbind
group:    files systemd sss winbind
<img width="1144" height="558" alt="Screenshot 2026-04-20 151124" src="https://github.com/user-attachments/assets/4bcfac78-41dd-4a79-a001-ecd216c55fe4" />


Step 5: Enable PAM Winbind Authentication
sudo pam-auth-update
<img width="929" height="233" alt="Screenshot 2026-04-20 151431" src="https://github.com/user-attachments/assets/0e424a1a-0d15-44fa-8e9a-3206568e9e72" />

In the PAM configuration menu, ensure these are enabled:

✅ Winbind NT/Active Directory authentication
✅ Create home directory on login
✅ Unix authentication
<img width="1152" height="577" alt="Screenshot 2026-04-20 151547" src="https://github.com/user-attachments/assets/23403076-4184-4b25-9857-e8539daffb03" />

Step 6: Configure DNS in resolv.conf
Point the system DNS resolver to the Domain Controller:
sudo nano /etc/resolv.conf
Add/confirm:
nameserver 10.0.2.5
nameserver 127.0.0.53
options edns0 trust-ad
search .
<img width="887" height="630" alt="Screenshot 2026-04-20 152005" src="https://github.com/user-attachments/assets/d9000c20-1a66-4f64-8f2d-220e9c42004d" />

Step 7: Fix Hostname — NetBIOS 15 Character Limit
The first domain join attempt failed with this error:
Our netbios name can be at most 15 chars long, "PROJECT-HOMELAB-LINUX-C" is 23 chars long
Invalid configuration. Exiting....
Failed to join domain: The format of the specified computer name is invalid.
Fix: Rename the hostname to 15 characters or fewer:
sudo hostnamectl set-hostname proj-lab-lin-c
<img width="886" height="127" alt="Screenshot 2026-04-20 152505" src="https://github.com/user-attachments/assets/966ddedb-f4f6-49ea-a8d4-bac5a664c55f" />

Step 8: Join the Domain
sudo net ads join -U Administrator
<img width="835" height="187" alt="Screenshot 2026-04-20 160125" src="https://github.com/user-attachments/assets/c02b50bf-007c-4769-8f03-d0eed74ecc78" />
  
Enter the Administrator password when prompted. Successful output:
Password for [CORP\Administrator]:
Using short domain name -- CORP
Joined 'PROJ-LAB-LIN-C' to dns domain 'corp.soc-lab-dc.com'


Step 9: Restart Winbind Service
bashsystemctl restart winbind
<img width="837" height="183" alt="Screenshot 2026-04-20 160410" src="https://github.com/user-attachments/assets/dc9cdbb5-f6ae-4710-b3e8-217157e00a9e" />

Step 10: Verify Domain Join
Check AD connection info:
net ads info
Expected output:
LDAP server: 10.0.2.5
LDAP server name: WIN-1MD9MNNGLGQ.corp.soc-lab-dc.com
Realm: CORP.SOC-LAB-DC.COM
Bind Path: dc=CORP,dc=SOC-LAB-DC,dc=COM
LDAP port: 389
KDC server: 10.0.2.5
<img width="813" height="282" alt="Screenshot 2026-04-20 160816" src="https://github.com/user-attachments/assets/da43db3b-7c02-405d-99c6-f133d54aed66" />

Step 11: Login as a Domain User
sudo login
When prompted for username, use the format:
CORP+soso
<img width="575" height="56" alt="Screenshot 2026-04-20 161057" src="https://github.com/user-attachments/assets/17eca221-fff1-47cf-a592-193eb91393df" />

  
Step 12: Verify in Active Directory Users and Computers
On the Windows Domain Controller, open:
Server Manager → Tools → Active Directory Users and Computers → corp.soc-lab-dc.com → Computers
You will see PROJ-LAB-LIN-C listed alongside the Windows client — confirming the Linux machine is fully domain-joined. ✅
PROJ-LAB-LIN-C    Computer    DNS: proj-lab-lin-c.corp.soc-lab-dc.com
<img width="446" height="182" alt="Screenshot 2026-04-20 161340" src="https://github.com/user-attachments/assets/7855f31e-0d54-4db7-ab27-84254028b5b4" />
<img width="1100" height="348" alt="Screenshot 2026-04-20 161419" src="https://github.com/user-attachments/assets/db23b2fd-e141-41ee-a564-74769f322f33" />

