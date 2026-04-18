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

🖥️ Step 1: Create the Virtual Machine in VirtualBox
Before installing Windows Server, we set up the NAT Network in VirtualBox so all our VMs can communicate with each other.
Network Created:

Name: project-SOC-lab
IPv4 Prefix: 10.0.2.0/24
DHCP: Enabled 

Then i create a vm with 2cpu and 4gigs of ram and 50GB of storage 

🔧 Step 2: Install Active Directory Domain Services (AD DS)
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


🌐 Step 3: Promote Server to Domain Controller
After installing the AD DS role, we promoted the server to a Domain Controller by creating a new forest.
Configuration:

Root Domain Name: corp.soc-lab-dc.com
Forest Functional Level: Windows Server 2025
DNS Server: ✅ Enabled
Global Catalog: ✅ Enabled

Once promotion completed, the server restarted and Server Manager confirmed all four roles were active:
RoleStatusAD DS✅ OnlineDHCP✅ OnlineDNS✅ OnlineFile and Storage Services✅ Online


🌍 Step 4: Configure DNS Forwarder
To allow the DC to resolve external internet addresses (like google.com) while also handling internal domain resolution, we configured a DNS Forwarder.

Open DNS Manager from Server Manager
Right-click the server → Properties → Forwarders tab
Add Google's public DNS: 8.8.8.8

Verification — DNS and Connectivity Test:
We verified the setup using PowerShell:

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
