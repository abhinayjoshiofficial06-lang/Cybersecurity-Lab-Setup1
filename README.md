🔐 Cybersecurity Lab Environment Setup
Building an isolated virtual lab for penetration testing and ethical hacking practice

Skill VirtualBox Kali Linux Network NetworkWalks

📌 Project Overview
This project sets up a virtual cybersecurity and penetration-testing laboratory using VirtualBox and Kali Linux.

The lab gives a controlled, isolated environment where security tools, network scanning, reconnaissance and vulnerability assessment can be practised safely and repeatedly. It runs on a private NAT Network so more machines can be added later as targets for authorized testing.

🎯 Objectives
Install and configure VirtualBox.
Import Kali Linux as a virtual machine.
Create a private NAT Network (10.0.0.0/24).
Assign the static IP 10.0.0.2/24 to the Kali VM.
Give Kali full Internet access and verify DNS resolution.
Enable shared clipboard, drag & drop and a shared folder with the host.
Take a clean VM snapshot for recovery.
Document the full process with screenshots.
🛡️ Purpose of the Lab
Typical uses: network reconnaissance, port scanning, vulnerability assessment, packet analysis, web security testing and security-tool experimentation.

⚠️ Important: Use this lab only on systems you own or have explicit permission to test.

🏗️ Lab Architecture
Lab architecture

Windows Host (VirtualBox)
        |
   NatNetwork 10.0.0.0/24  (gateway 10.0.0.1, DHCP disabled)
        |
   Kali Linux  10.0.0.2/24  --->  Internet
⚙️ Lab Configuration
🧩 Component	⚙️ Configuration
🖥️ Host OS	Windows 11
🧰 Hypervisor	VirtualBox (latest version)
🐉 Security OS	Kali Linux (latest VirtualBox image)
🧠 Kali RAM	2048 MB
🌐 Virtual Network	NAT Network (NatNetwork)
📡 Network Address	10.0.0.0/24
🐧 Kali IP Address	10.0.0.2/24
🚪 Default Gateway	10.0.0.1
🌍 DNS Server	8.8.8.8
🔌 Adapter Type	Intel PRO/1000 MT Desktop
📋 Clipboard / Drag & Drop	Bidirectional
📁 Shared Folder	Host Downloads folder (Auto-mount)
🪜 Lab Setup Procedure
Step 1. Install 7-Zip
7-Zip is used to extract the Kali Linux VM archive (.7z). Download: https://7-zip.org/download.html

Step 2. Install VirtualBox
VirtualBox was installed as the hypervisor. Download: https://virtualbox.org/wiki/Downloads

Step 3. Create the NAT Network
In VirtualBox: File → Tools → Network Manager → NAT Networks

Network Name: NatNetwork
IPv4 Prefix:  10.0.0.0/24
DHCP:         Disabled
IPv6:         Disabled
DHCP was disabled so that no other VM can take the reserved static IP.

NAT Network settings

Step 4. Import Kali Linux and Configure VM Settings
Kali Linux was downloaded from https://kali.org/get-kali and imported into VirtualBox.

Network adapter:

Adapter 1
Attached to:  NAT Network
Name:         NatNetwork
Adapter Type: Intel PRO/1000 MT Desktop
Kali VM network adapter

Clipboard and Drag & Drop (Settings → General → Features):

Shared Clipboard: Bidirectional
Drag-and-Drop:    Bidirectional
Clipboard and drag-drop

Shared folder (Settings → Shared Folders): the host Downloads folder was added with Auto-mount enabled and Full access. It appears inside Kali as sf_Downloads.

Shared folder

Step 5. Configure the Kali Linux IP Address
The connection name was found with:

nmcli con show
The static IP was then set with:

sudo nmcli con mod "Wired connection 1" ipv4.method manual \
  ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8
sudo nmcli con up "Wired connection 1"
Resulting configuration:

IP Address:  10.0.0.2
Subnet Mask: 255.255.255.0 (/24)
Gateway:     10.0.0.1
DNS:         8.8.8.8
Kali IP and ping test

Step 6. Create a Clean VM Snapshot
After everything was working, a snapshot was taken (Machine → Take Snapshot):

Snapshot name: Final-Setup-10.0.0.2
It is the clean baseline, so the VM can be restored if a future exercise breaks the configuration.

Snapshot

🔎 Lab Verification
✅ Test	🧾 Command	🎯 Result
IP address	ip a	eth0 shows 10.0.0.2/24 ✅
Internet connectivity	ping -c 3 8.8.8.8	3/3 replies, 0% loss ✅
DNS resolution	ping -c 3 google.com	Resolves, 0% loss ✅
Browser access	Firefox → google.com	Page loads ✅
Shared folder	sf_Downloads on desktop	Host files visible ✅
Internet access in Firefox

🐞 Problems Encountered & Solutions
Problem 1. No Internet in Kali
Cause: The existing NatNetwork used the prefix 10.0.2.0/24, while the static IP 10.0.0.2/24 and gateway 10.0.0.1 belong to 10.0.0.0/24. The IP and the network did not match, so traffic had no route out.

Solution: The NatNetwork IPv4 prefix was changed to 10.0.0.0/24 (Network Manager → NAT Networks), DHCP was disabled, and the static IP was applied again in Kali.

Problem 2. nmcli command errors
Cause: Typing mistakes: Wired connection1 (missing space), ipv4 instead of ipv4.gateway, and a broken DNS value.

Solution: The exact connection name was taken from nmcli con show, and the command was copied and pasted instead of typed by hand.

💡 What I Learned
Network address vs host address: 10.0.0.0/24 is the whole subnet, while 10.0.0.2/24 is one host inside it.
NAT vs NAT Network: a NAT Network lets several VMs talk to each other and still reach the Internet.
Static IP configuration in Kali with nmcli (IP, gateway, DNS).
Troubleshooting: the subnet of the virtual network and the VM's IP must match.
Snapshots give a safe recovery point before risky experiments.
Documentation: recording steps, screenshots and problems is part of professional work.
🔐 Security & Ethical Use
This laboratory is intended strictly for educational purposes and authorized testing only.

🔗 Tools & Resources
7-Zip: https://7-zip.org/download.html
VirtualBox: https://virtualbox.org/wiki/Downloads
Kali Linux: https://kali.org/get-kali
👤 Author
Katyani Singh Cybersecurity Intern, B084C

LinkedIn: https://www.linkedin.com/in/katyani-singh-853bb5321

📌 Project Information
Program Name: Cybersecurity at Networkwalks | Week: 01 | Project: Cybersecurity & Pentesting Lab Setup | Repository: GitHub
