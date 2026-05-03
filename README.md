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

Linux Server Setup — Jumpbox & Docker Installation
Overview
In this section we set up our Linux Server (corp-svr), which acts as a Jumpbox (also called a Bastion Host) in our SOCLab network. A jumpbox is a dedicated server that acts as a secure gateway — instead of connecting directly to other machines in the network, all access goes through this Linux server first. This is a common security practice in enterprise environments to reduce the attack surface.
We also joined this server to our Active Directory domain and installed Docker to prepare it for future containerized services.
Hostname: corp-svr
DNS Name: corp-svr.corp.soc-lab-dc.com
IP Address: 10.0.2.106
OS: Ubuntu 24.04.3 LTS
Role: Jumpbox / Linux Server

🖥️ Step 1: Clone the Linux VM (Full Clone)
Instead of building a new VM from scratch, we cloned our existing Linux snapshot to save time. We used a Full Clone so the new VM is completely independent.
Steps:
<img width="1273" height="890" alt="Screenshot 2026-04-21 134923" src="https://github.com/user-attachments/assets/8c676bce-cb77-4960-baca-cfa0f94ab8d4" />

In VirtualBox, right-click the existing Linux VM → Clone
Set the new VM name: project-homelab-svr-lin
Select Full Clone as the clone type
Click Next → Finish
<img width="1273" height="890" alt="Screenshot 2026-04-21 134923" src="https://github.com/user-attachments/assets/7c3b68e8-a46f-424a-b593-034c165d5b6f" />

✏️ Step 2: Change the Hostname
After cloning, the machine still had the old hostname. We updated it to reflect its new role.
sudo hostnamectl set-hostname corp-svr
Then we rebooted for the change to take effect. The terminal prompt now shows:
project-homelab-admin@corp-svr:~$

👤 Step 3: Create Admin User
We created a dedicated admin user for managing this server instead of using the default account.
bashsudo adduser project-homelab-admin
Then granted this user sudo (admin) privileges:
sudo usermod -aG sudo project-homelab-admin
<img width="974" height="569" alt="Screenshot 2026-04-21 171224" src="https://github.com/user-attachments/assets/bbcb7f02-add1-421c-bb33-70c858d25085" />

🌐 Step 4: Verify Network Configuration
We checked the network settings to confirm the server received an IP from our DHCP server.
SettingValueIP Address10.0.2.106 Default Gateway 10.0.2.1 DNS Server 10.0.2.5 (our Dc)
<img width="745" height="600" alt="Screenshot 2026-04-21 172608" src="https://github.com/user-attachments/assets/f835df0f-f533-4d73-b36d-762a23f33140" />

🔗 Step 5: Join the Active Directory Domain
We joined the Linux server to our Active Directory domain using Winbind (the same method used for the Linux client).
First we verified connectivity to the domain controller:
bashping corp.soc-lab-dc.com
Then joined the domain:
sudo net ads join -U administrator
Result: Joined 'CORP-SVR' to dns domain 'corp.soc-lab-dc.com' ✅
<img width="1260" height="853" alt="Screenshot 2026-04-21 173255" src="https://github.com/user-attachments/assets/a47c4368-3e17-4e4b-ab2a-4fa260189eb5" />

Verification — AD Users and Computers:
We switched to the Windows Server and opened Active Directory Users and Computers to confirm the machine appeared under the Computers container.
Result: CORP-SVR appeared in the domain with DNS name corp-svr.corp.soc-lab-dc.com ✅

Domain Login Test:
We logged into the Linux server using the domain administrator account to confirm authentication was working:
sudo login
# Login: CORP+Administrator
Result: Successfully logged in as domain admin ✅
<img width="955" height="589" alt="Screenshot 2026-04-21 174717" src="https://github.com/user-attachments/assets/373b976c-f503-4ffa-a3ef-0a162cb71e96" />

🐳 Step 6: Install Docker
We installed Docker on the Linux server following the official documentation at https://docs.docker.com/engine/install/ubuntu.
Step 1 — Add Docker's official GPG key and repository:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the Docker repository
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
<img width="885" height="811" alt="Screenshot 2026-04-21 175511" src="https://github.com/user-attachments/assets/f507f944-dc5a-4451-8792-5a4f5bad4925" />

Step 2 — Install Docker packages:
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
<img width="542" height="292" alt="Screenshot 2026-04-21 175541" src="https://github.com/user-attachments/assets/52bad272-49a9-4aeb-a01e-0d305a38758d" />

Step 3 — Verify Docker installation:
sudo docker run hello-world
<img width="1280" height="1027" alt="Screenshot 2026-04-21 181323" src="https://github.com/user-attachments/assets/ef184b1e-6d4e-47b4-9723-f12eb0b32bcc" />

📌 About This Project
This is Day 4 of the SOC Home Lab series. After setting up our AD domain (Day 1), joining a Linux client (Day 2-3), today we build the most important component of the lab — the dedicated security server that will run Wazuh SIEM/XDR.
Wazuh is an open-source unified XDR and SIEM platform used by thousands of organizations worldwide — including NASA, Cisco, and Intuit. It collects, analyzes, and correlates security events from all endpoints in our lab.
What we accomplished today:

Cloned the Ubuntu Linux VM to create a dedicated security server
Renamed the host to sec-box and configured a new static IP
Created a local service account (sec-user)
Joined sec-box to the Active Directory domain (corp.soc-lab-dc.com)
Installed Wazuh 4.14 all-in-one (Manager + Indexer + Dashboard)
Enrolled 3 agents: Windows 11 client, Ubuntu Linux client, Windows Server DC
Configured agent.conf for Linux log collection (auth, secure, audit logs)
 Step-by-Step Configuration

Step 1: Clone the VM from Day 2/3 Snapshot
Instead of building a fresh Ubuntu VM from scratch, we cloned the existing Linux client VM using VirtualBox:
<img width="1273" height="890" alt="Screenshot 2026-04-21 134923" src="https://github.com/user-attachments/assets/6c69f81a-6992-4b7a-a446-d8c6fc4ee462" />


Source VM: project-homelab-linux-c (Snap linux dc 2)
Clone name: project-homelab-svr-lin → later renamed project-homelab-linux-secbox
Clone type: Full Clone
Snapshot: Current Machine State
MAC Address Policy: Include only NAT network adapter MAC addresses

Step 2: Rename the Hostname to sec-box
After booting the cloned VM, rename it to reflect its new role as the security server
sudo nano /etc/hostname
Reboot to apply the hostname change
sudo reboot
After reboot, verify
hostname
# Output: sec-box
Step 3: Create a Local Service Account
Add a dedicated local user for managing the security server:
sudo adduser sec-user
<img width="808" height="231" alt="Screenshot 2026-04-26 171407" src="https://github.com/user-attachments/assets/e5254183-9947-4ad6-9a0c-010205d6958f" />

Step 4: Configure Static IP Address
Set a static IP for the security server via Network Manager → IPv4 → Manual:
IP Address:   10.0.2.10
Netmask:      255.255.255.0
Gateway:      10.0.2.1
DNS:          10.0.2.5  (Domain Controller)
⚠️ Always point DNS to the Domain Controller. This ensures the server can resolve the AD domain name for the domain join to succeed.
<img width="750" height="596" alt="Screenshot 2026-04-26 173809" src="https://github.com/user-attachments/assets/8c05e07c-d057-4191-9e8c-8ebaadf7afdb" />

Step 5: Join sec-box to Active Directory
Since Samba Winbind is already installed from the cloned VM, we just restart the service and join:
# Restart Winbind service
sudo systemctl restart winbind

# Join the domain
sudo net ads join -U Administrator
<img width="829" height="584" alt="Screenshot 2026-04-26 193602" src="https://github.com/user-attachments/assets/366e697e-87f3-433b-a94d-b891d8359bc1" />

Step 6: Create Domain User — sec user
On the Windows Domain Controller, open Active Directory Users and Computers and create a new domain user:

First name: sec
Last name: user
Display name: sec user
Username: secuser
<img width="425" height="535" alt="Screenshot 2026-04-26 194140" src="https://github.com/user-attachments/assets/49396380-924b-4d50-a85e-e63aab0cb704" />

Step 7: Allocate Resources for the Security Server
Before installing Wazuh, increase the VM resources to handle the SIEM workload:
In VirtualBox → project-homelab-linux-secbox → Settings → System:
Base Memory: 8192 MB (8 GB RAM)
Storage: 80 GB virtual disk
<img width="782" height="516" alt="Screenshot 2026-04-27 174037" src="https://github.com/user-attachments/assets/2431e8ec-a796-457c-8f0d-91a57c5a4244" />

Wazuh with its OpenSearch indexer backend requires significant RAM. 8 GB is the recommended minimum for a lab environment with 3 agents.
 <img width="2552" height="1071" alt="Screenshot 2026-04-27 173639" src="https://github.com/user-attachments/assets/72bd99c2-7dcc-4343-a210-dc450600e900" />

Step 8: Install curl
Wazuh installation requires curl to download the installer script:
sudo apt install curl

Step 9: Install Wazuh 4.14 (All-in-One)
Download and run the official Wazuh installation assistant:
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
<img width="816" height="577" alt="Screenshot 2026-04-27 175915" src="https://github.com/user-attachments/assets/6b4c5435-5ad7-40b8-8c7b-71d9f61b167e" />

The -a flag installs all components:
Wazuh Manager — core SIEM engine
Wazuh Indexer — OpenSearch backend for event storage
Wazuh Dashboard — web UI at https://10.0.2.10


Step 10: Access the Wazuh Dashboard
Open a browser and navigate to:
https://10.0.2.10

Username: admin
Password: [auto-generated — shown at end of install script]
<img width="1211" height="914" alt="Screenshot 2026-04-27 181429" src="https://github.com/user-attachments/assets/81a4852c-1be8-48eb-9f64-d5c9e5e1998c" />

Step 11: Enroll Agent 1 — Ubuntu Linux Client (proj-lab-lin-c)
From the Wazuh Dashboard → Endpoints → Deploy new agent:
Agent deployment command (run on proj-lab-lin-c):

wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.14.5-1_amd64.deb \
&& sudo WAZUH_MANAGER='10.0.2.10' \
WAZUH_AGENT_NAME='project-homelab-linux-c' \
dpkg -i ./wazuh-agent_4.14.5-1_amd64.deb
<img width="1197" height="888" alt="Screenshot 2026-04-27 191030" src="https://github.com/user-attachments/assets/b8a4a27f-70b1-4592-92f0-cd1e559efdcb" />

Start the agent:
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent

Step 12: Enroll Agent 2 — Windows 11 Client
doing the samerhing from linux but here we will the the powershell don't forget to run it as administrator 
From PowerShell (Administrator) on the Windows 11 client:
<img width="1114" height="336" alt="Screenshot 2026-04-27 191609" src="https://github.com/user-attachments/assets/00377600-9076-45b1-945f-591398e69c9e" />

Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.5-1.msi -OutFile $env:tmp\wazuh-agent.msi
msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='10.0.2.10' WAZUH_AGENT_NAME='project-homelab-winclient'

 Step 13: Enroll Agent 3 — Windows Server (Active Directory DC)
From PowerShell (Administrator) on the Domain Controller  
<img width="1216" height="977" alt="Screenshot 2026-04-27 191721" src="https://github.com/user-attachments/assets/52e6b206-067a-44ff-a5a9-9cc3447ce42c" />

Step 15: Configure Linux Agent Log Collection
In Wazuh Dashboard → Endpoints → Groups → linux group → agent.conf:
<img width="1212" height="531" alt="Screenshot 2026-04-28 234108" src="https://github.com/user-attachments/assets/055e3231-d7c9-4a00-8d8c-c08ba0d1789f" />

<agent_config>
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/auth.log</location>
  </localfile>
  <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/secure</location>
  </localfile>
  <localfile>
    <log_format>audit</log_format>
    <location>/var/log/audit/audit.log</location>
  </localfile>
</agent_config>

Log files being monitored:
/var/log/auth.log — SSH logins, sudo usage, authentication events
/var/log/secure — PAM authentication, security events
/var/log/audit/audit.log — Linux audit framework events
<img width="1203" height="349" alt="Screenshot 2026-04-28 234026" src="https://github.com/user-attachments/assets/b473830b-2cc3-477c-8c76-f19de329a8e7" />

also don't forget to config your windows agent.conf of windows group 
