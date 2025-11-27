# Building a SOC + Honeynet in Azure (Live Traffic)  
![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

## Overview

In this project, I built a **mini honeynet** in **Microsoft Azure** to simulate a real-world security operations center (SOC). The honeynet was designed to attract and log malicious traffic, allowing me to monitor and analyze security events using **Microsoft Sentinel** and **Azure Monitor**. The project demonstrates the full process of building a cloud-based SOC, from initial setup to security hardening, and then analyzing the results to assess the impact of security controls.

This project is valuable for **SOC Analyst** and **Security Analyst** roles, as it shows practical experience in cloud security, incident detection, and the use of SIEM solutions for real-time monitoring and response.

## Architecture Overview

The architecture consists of the following components:

- **Virtual Network (VNet)**: Segregates resources within Azure for controlled traffic flow.
- **Network Security Group (NSG)**: Initially wide open to simulate real-world vulnerability, then hardened to block malicious traffic.
- **Virtual Machines**: 2 Windows VMs and 1 Linux VM acting as honeypots to attract attacks.
- **Log Analytics Workspace**: Centralized logging solution where all logs from the VMs and other resources are aggregated.
- **Microsoft Sentinel**: Used to monitor logs, create incidents, and visualize attacks.
- **Azure Key Vault & Storage Account**: Secure storage for sensitive data and logs.
  
### Before Hardening (Insecure Environment)

In the initial setup, all resources were deployed with **public endpoints visible to the internet**. Both the **Network Security Groups** and firewalls on the VMs were wide open to simulate a vulnerable environment. This setup allowed the honeynet to attract malicious traffic, which was then logged and analyzed.

**![Architecture Diagram - Before Hardening](path_to_image_before_hardening.png)**  
*Insert Image Here: Architecture before hardening (VNet, NSG, open firewalls, public access)*

### After Hardening (Secured Environment)

Security controls were implemented to harden the environment:

- **Network Security Groups (NSGs)** were configured to block all inbound traffic except from a designated **admin workstation**.
- **Private Endpoints** were used for resources like **Azure Key Vault** and **Azure Storage Account**, ensuring no public exposure.
- **Built-in firewalls** were enabled on all resources to prevent unauthorized access.

**![Architecture Diagram - After Hardening](path_to_image_after_hardening.png)**  
*Insert Image Here: Architecture after security controls were applied (NSGs hardened, private endpoints, firewalls enabled)*

# Building a SOC + Honeynet in Azure (Live Attack Traffic)

## 📌 Overview

In this project, I built a cloud-based **honeynet** in Microsoft Azure designed to attract **real-world cyberattacks**, capture logs, forward them into a **Log Analytics Workspace**, and analyze them with **Microsoft Sentinel**.  
I intentionally exposed a Windows VM to the public internet, monitored attacks, created an attack map, and then documented the before/after impact.

This project demonstrates hands-on experience in:

- Cloud Security (Azure)
- SIEM (Microsoft Sentinel)
- Log Analytics (LAW)
- Windows Event Log analysis
- Threat detection & attack visualization
- Network security (NSGs, firewalls)
- KQL (Kusto Query Language)

Below is the full walkthrough of the project *including exactly where screenshots belong* and why each step matters.

---

# 🪪 **Step-by-Step Documentation (with Screenshot Instructions)**

---

## **1) VM Deployed & Reachable (Starting Point)**

### 📍 Where to Screenshot  
Azure Portal → **Virtual Machines** → Your VM → **Overview**

### 📸 What to Capture  
- VM name  
- VM OS  
- VM size  
- Public IP address  
- Resource Group  
- **Connect → RDP** button  
(Or the RDP dialog without credentials)

### 💡 Why  
Shows the honeypot exists, is intentionally exposed, and reachable from the internet.

### 🖼️ Screenshot Filename  
`01-vm-overview.png`  
`02-vm-connect.png` (optional RDP dialog)

### 📝 README Placement  
![VM Overview](assets/images/01-vm-overview.png)  
*VM overview showing public IP and RDP connection option.*

---

## **2) NSG / Networking — Intentionally Open to the Internet**

### 📍 Where to Screenshot  
Azure Portal → Virtual Machines → **Networking** →  
Click the NSG link → **Inbound Security Rules**

### 📸 What to Capture  
- Inbound NSG rules showing **Any / Any / 0.0.0.0/0**  
- NIC + VNet mapping

### 💡 Why  
Proves the VM was intentionally vulnerable to attract live attacks.

### 🖼️ Screenshot Filename  
`03-nsg-inbound.png`

### 📝 README Placement  
![NSG Inbound Rules](assets/images/03-nsg-inbound.png)  
*NSG rules intentionally left wide open for honeypot exposure.*

---

## **3) Windows Firewall Disabled Inside the VM**

### 📍 Where to Screenshot  
Inside RDP session → **wf.msc**  
(or Windows Firewall control panel)

### 📸 What to Capture  
All three profiles:  
- Domain = Off  
- Private = Off  
- Public = Off  

### 💡 Why  
Shows the VM was configured to be deliberately vulnerable.

### 🖼️ Screenshot Filename  
`04-windows-firewall-off.png`

### 📝 README Placement  
![Windows Firewall Off](assets/images/04-windows-firewall-off.png)  
*Windows Firewall intentionally disabled to increase honeypot visibility.*

---

## **4) Failed Login Events (Event Viewer Evidence)**

### 📍 Where to Screenshot  
Inside VM → Event Viewer →  
Windows Logs → **Security** → Filter Event ID **4625**

### 📸 What to Capture  
Event 4625 entries showing:  
- Account Name  
- Failure Reason  
- Source IP address  

### 💡 Why  
This is direct proof attackers are interacting with the honeypot.

### 🖼️ Screenshot Filename  
`05-eventviewer-4625.png`

### 📝 README Placement  
![Event Viewer 4625](assets/images/05-eventviewer-4625.png)  
*Failed login attempts (4625) captured from live attackers.*

---

## **5) Log Analytics Workspace (LAW) Created & Sentinel Connected**

### 📍 Where to Screenshot  
- Azure Portal → **Log Analytics Workspaces** → Overview  
- Azure Portal → **Microsoft Sentinel** → Overview (show linked workspace)

### 📸 What to Capture  
- Workspace Name  
- Workspace ID  
- Region  
- Sentinel connected to LAW  
- Data connectors screen (optional)

### 💡 Why  
Confirms the SOC backend pipeline is set up.

### 🖼️ Screenshot Filenames  
`06-law-overview.png`  
`07-sentinel-connected.png`

### 📝 README Placement  
![LAW Overview](assets/images/06-law-overview.png)  
*Log Analytics Workspace hosting all ingested logs.*

![Sentinel Connected](assets/images/07-sentinel-connected.png)  
*Microsoft Sentinel connected to the environment.*

---

## **6) Azure Monitor Agent Installed & Data Collection Rule Configured**

### 📍 Where to Screenshot  
Azure Portal → Virtual Machines → Your VM →  
**Extensions + applications**  
AND  
Azure Monitor / Sentinel → Data Collection Rules

### 📸 What to Capture  
- Azure Monitor Agent status = **Installed**  
- DCR (example: `DCR-Windows-VM`)  
- Data source = Windows Security Events → All Security Events  

### 💡 Why  
Shows the pipeline forwarding logs into LAW/Sentinel.

### 🖼️ Screenshot Filenames  
`08-ama-extension.png`  
`09-dcr-created.png`

### 📝 README Placement  
![AMA Installed](assets/images/08-ama-extension.png)  
*Azure Monitor Agent installed on honeypot VM.*

![Data Collection Rule](assets/images/09-dcr-created.png)  
*DCR created to send Windows Security events to Sentinel.*

---

## **7) Watchlist Upload (Geo-IP Data) & Attack Map Workbook**

### 📍 Where to Screenshot  
**Watchlist Upload:**  
Sentinel → Configuration → Watchlists → Your watchlist (alias “go”)

**Attack Map Workbook:**  
Sentinel → Workbooks → Windows VM Attack Map → Map View

### 📸 What to Capture  
- Watchlist row count + alias  
- Workbook showing the global attack map  
- Pins / heatmap of attacker locations

### 💡 Why  
This is the **visual proof** of real attackers hitting your honeypot.

### 🖼️ Screenshot Filenames  
`10-watchlist-go.png`  
`11-attack-map-workbook.png`

### 📝 README Placement  
![Watchlist](assets/images/10-watchlist-go.png)  
*Geo-IP watchlist imported for IP-to-location enrichment.*

![Attack Map](assets/images/11-attack-map-workbook.png)  
*Attack map showing live attacker geographic origins.*

---

# 🎯 **Conclusion**

This project simulates a real SOC workflow:

- Deploy a vulnerable asset  
- Capture attacker behavior  
- Forward logs to a SIEM  
- Analyze Windows & Linux security logs  
- Create visualizations & detections  
- Harden the environment  
- Compare before/after results  
