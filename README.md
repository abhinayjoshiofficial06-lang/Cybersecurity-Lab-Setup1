# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**

![Platform](https://img.shields.io/badge/Platform-Windows%2011-blue)
![Hypervisor](https://img.shields.io/badge/Hypervisor-VirtualBox-183A61)
![Security OS](https://img.shields.io/badge/Security%20OS-Kali%20Linux-557C94)
![Purpose](https://img.shields.io/badge/Purpose-Educational%20%26%20Authorized%20Testing-green)

---

## 📌 Project Overview

This project documents the setup of a virtual cybersecurity laboratory using **Oracle VirtualBox** and **Kali Linux**.

The lab is intended to provide a controlled environment for learning and practising cybersecurity activities such as network reconnaissance, port scanning, packet analysis, vulnerability assessment, and security-tool experimentation.

The planned network uses a private **NAT Network** so that multiple virtual machines can communicate with one another while retaining internet connectivity. Additional virtual machines can be added later as authorized target systems.

> **Safety notice:** Only scan or test systems that you own or have explicit permission to assess. Do not expose intentionally vulnerable machines to public networks.

## 🎯 Objectives

- Install Oracle VirtualBox and the required supporting software.
- Import and configure a Kali Linux virtual machine.
- Create a private NAT Network using `10.0.0.0/24`.
- Configure Kali with the planned static address `10.0.0.2/24`.
- Configure and verify gateway, DNS, and internet connectivity.
- Enable host–guest integration features where required.
- Configure a shared folder between Windows and Kali.
- Create a clean VM snapshot for recovery.
- Record the setup process, verification steps, and troubleshooting notes.

## 🏗️ Planned Lab Architecture

```text
                 Windows 11 Host
                       |
                Oracle VirtualBox
                       |
          NAT Network: 10.0.0.0/24
             Gateway: 10.0.0.1
                       |
                Kali Linux VM
                 10.0.0.2/24
                       |
                    Internet
```

The diagram represents the **intended configuration**. Confirm the actual adapter mode and NAT Network selection in VirtualBox before treating the screenshots as final verification evidence.

## ⚙️ Lab Configuration

| Component | Planned configuration |
|---|---|
| Host operating system | Windows 11 |
| Hypervisor | Oracle VirtualBox 7.2.22 (version visible in supplied installer screenshot) |
| Security operating system | Kali Linux |
| VM memory | 2048 MB (2 GB) |
| Virtual network | NAT Network (`NatNetwork`) |
| Network prefix | `10.0.0.0/24` |
| Kali IPv4 address | `10.0.0.2/24` |
| Default gateway | `10.0.0.1` |
| DNS server | `8.8.8.8` |
| Virtual adapter | Intel PRO/1000 MT Desktop |
| Shared clipboard | Bidirectional (planned) |
| Drag and drop | Bidirectional (planned) |
| Shared folder | Windows Downloads folder (Auto-mount; planned) |

**Configuration evidence to re-check:** The supplied NAT Network screenshot currently displays DHCP as **Enabled**, and the supplied Kali adapter screenshot displays **Attached to: NAT**. The target configuration described above requires the VM to be attached to the named **NAT Network (`NatNetwork`)**. If you want DHCP disabled, turn it off in Network Manager and capture a new screenshot. If DHCP remains enabled, ensure the static address does not conflict with its lease pool.

## 🪜 Lab Setup Procedure

### Step 1 — Install 7-Zip

7-Zip is used to extract the Kali Linux virtual-machine archive (`.7z`).

Download: [7-Zip official website](https://7-zip.org/download.html)

*Screenshot evidence:* Not provided.

### Step 2 — Install Oracle VirtualBox

Oracle VirtualBox is the hypervisor used to run the Kali Linux virtual machine. The supplied installer screenshot shows VirtualBox **7.2.22 (amd64)**.

Download: [VirtualBox downloads](https://virtualbox.org/wiki/Downloads)

![VirtualBox installer](screenshots/01-virtualbox-installer.png)

*Figure 1. Oracle VirtualBox 7.2.22 installer.*

After installation, VirtualBox Manager can be opened to create, import, and manage virtual machines.

![VirtualBox Manager](screenshots/02-virtualbox-manager.png)

*Figure 2. Oracle VirtualBox Manager.*

### Step 3 — Create the NAT Network

In VirtualBox, open **File → Tools → Network Manager → NAT Networks**.

Configure the intended network as follows:

| Setting | Intended value |
|---|---|
| Name | `NatNetwork` |
| IPv4 Prefix | `10.0.0.0/24` |
| DHCP | Disabled (if using the documented static-IP-only plan) |
| IPv6 | Disabled |

A private `/24` network provides addresses in the `10.0.0.0/24` subnet. The network address is `10.0.0.0`; the planned Kali host address is `10.0.0.2`.

![NAT Network settings](screenshots/03-nat-network-settings.png)

*Figure 3. NAT Network configuration screen. Note: the supplied screenshot shows DHCP Enabled; update the setting and recapture if DHCP is intended to be disabled.*

### Step 4 — Import Kali Linux and Configure the VM

Download Kali Linux from the [official Kali downloads page](https://www.kali.org/get-kali/). Import the VirtualBox image into VirtualBox Manager and review the VM settings.

For the network adapter, the intended configuration is:

| Adapter setting | Intended value |
|---|---|
| Enable Network Adapter | Enabled |
| Attached to | NAT Network |
| Name | `NatNetwork` |
| Adapter Type | Intel PRO/1000 MT Desktop |
| Virtual Cable Connected | Enabled |

![Kali network adapter settings](screenshots/04-kali-network-adapter.png)

*Figure 4. Kali VM network adapter settings. Note: the supplied screenshot shows `Attached to: NAT`; change this to `NAT Network` and select `NatNetwork` if following the architecture in this README.*

**Host–guest integration (optional):**

In **Settings → General → Advanced** (or the Features section, depending on VirtualBox version), configure:

- Shared Clipboard: Bidirectional
- Drag and Drop: Bidirectional

**Shared folder (optional):**

In **Settings → Shared Folders**, add the Windows Downloads folder. Enable Auto-mount and configure access according to your security needs. In Kali, an auto-mounted shared folder may appear under `/media/sf_<folder-name>` (for example, `/media/sf_Downloads`).

Only share folders that are appropriate for the lab. Avoid exposing sensitive host files to the virtual machine.

### Step 5 — Configure the Kali Linux Static IP

First, identify the NetworkManager connection name:

```bash
nmcli con show
```

Use the exact connection name returned by that command. The following example assumes it is `Wired connection 1`:

```bash
sudo nmcli con mod "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 10.0.0.2/24 \
  ipv4.gateway 10.0.0.1 \
  ipv4.dns 8.8.8.8

sudo nmcli con up "Wired connection 1"
```

Verify the resulting configuration:

```bash
ip address
ip route
resolvectl status
```

Expected values for the planned setup:

| Parameter | Expected value |
|---|---|
| IPv4 address | `10.0.0.2/24` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `10.0.0.1` |
| DNS server | `8.8.8.8` |

**Important:** The connection name may differ on your VM. Replace `Wired connection 1` with the exact name shown by `nmcli con show`. Also confirm that the NAT Network is configured with the expected gateway before applying these values.

*Screenshot evidence:* A screenshot showing the final `ip address`, route, and successful connectivity tests was not included in the supplied images. Add that evidence after completing verification.

### Step 6 — Verify Network and Internet Connectivity

Run the following tests from Kali:

**1. Check the assigned IP address**

```bash
ip address
```

Confirm that the relevant interface has `10.0.0.2/24`.

**2. Test connectivity to the gateway**

```bash
ping -c 3 10.0.0.1
```

**3. Test internet connectivity by IP**

```bash
ping -c 3 8.8.8.8
```

**4. Test DNS resolution**

```bash
ping -c 3 google.com
```

**5. Test browser access**

Open Firefox in Kali and visit `https://www.google.com`.

Record the actual outputs in the table below. Do not mark a test as passed until you have run it.

| Test | Command / method | Result |
|---|---|---|
| IP configuration | `ip address` | Pending screenshot / output |
| Default route | `ip route` | Pending screenshot / output |
| Gateway reachability | `ping -c 3 10.0.0.1` | Pending verification |
| Internet connectivity | `ping -c 3 8.8.8.8` | Pending verification |
| DNS resolution | `ping -c 3 google.com` | Pending verification |
| Browser access | Firefox → `google.com` | Pending screenshot |

### Step 7 — Create a Clean VM Snapshot

Once the network, integration features, and other required settings have been verified, shut down or pause the VM as appropriate and create a snapshot from the VirtualBox machine menu.

Suggested snapshot name:

```text
Final-Setup-10.0.0.2
```

A baseline snapshot provides a recovery point before later lab exercises. Take a new snapshot after major configuration changes rather than relying on an outdated baseline.

*Screenshot evidence:* Not provided. Add a screenshot of the snapshot in VirtualBox Manager.

## 🐞 Problems Encountered & Troubleshooting

### Problem 1 — No Internet Access in Kali

**Possible cause:** The VM's IP address and the NAT Network prefix do not belong to the same subnet, or the VM is attached to the wrong network mode.

For example, `10.0.0.2/24` belongs to `10.0.0.0/24`. It will not work as intended if the VM is connected to a different subnet such as `10.0.2.0/24`.

**Troubleshooting approach:**

1. Check the NAT Network IPv4 prefix in VirtualBox Network Manager.
2. Confirm that the VM adapter is attached to `NAT Network` and that `NatNetwork` is selected.
3. Confirm the gateway configured for that NAT Network.
4. Ensure the Kali IP, subnet, and gateway match the network configuration.
5. Reconnect the NetworkManager connection and repeat the connectivity tests.

### Problem 2 — `nmcli` Command Errors

**Possible causes:** A misspelled connection name, missing spaces, incorrect property names, or an incorrectly formatted DNS value.

**Troubleshooting approach:**

1. Run `nmcli con show` to obtain the exact connection name.
2. Copy the command carefully, preserving quotation marks and spaces.
3. Check the configuration with `nmcli con show "Wired connection 1"`.
4. Re-run the connection activation command.

## 💡 Key Learnings

- **Network address vs. host address:** `10.0.0.0/24` identifies the subnet, while `10.0.0.2/24` identifies a host address within it.
- **NAT vs. NAT Network:** NAT provides internet access to a VM; a NAT Network is designed to allow multiple attached VMs to communicate while using NAT for external connectivity.
- **Static IP configuration:** NetworkManager's `nmcli` can configure an IPv4 address, gateway, and DNS server from the terminal.
- **Subnet troubleshooting:** The VM's IP configuration must match the subnet and gateway of the virtual network to which it is connected.
- **Snapshots:** A clean baseline makes it easier to recover from configuration mistakes during lab exercises.
- **Technical documentation:** Clear steps, evidence, verification results, and troubleshooting notes make a lab reproducible and easier to review.

## 🔐 Security & Ethical Use

This laboratory is intended for cybersecurity education and authorized security testing only.

- Test only systems you own or have explicit permission to assess.
- Keep intentionally vulnerable targets isolated from public networks.
- Do not use the lab to disrupt services, access private data, or perform unauthorized activity.
- Take snapshots before experiments that may affect system stability.

## 🔗 Tools & Resources

- [7-Zip](https://7-zip.org/download.html)
- [Oracle VirtualBox](https://virtualbox.org/wiki/Downloads)
- [Kali Linux](https://www.kali.org/get-kali/)
- [Kali Linux Documentation](https://www.kali.org/docs/)
- [VirtualBox User Manual](https://www.virtualbox.org/manual/)

## 👤 Author

**Abhinay joshi**  
Cybersecurity Intern  
Trainee ID: `B084C`

[LinkedIn Profile](https://www.linkedin.com/in/katyani-singh-853bb5321)

## 📌 Program Information

| Field | Details |
|---|---|
| Program | Cybersecurity at Networkwalks |
| Week | 01 |
| Project | Cybersecurity & Pentesting Lab Setup |
| Repository | GitHub |

---

*This README documents the planned lab configuration and the screenshots supplied with the project. Update the verification table and replace any screenshots that do not reflect the final working configuration before submitting the repository.*
