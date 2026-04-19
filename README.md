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
