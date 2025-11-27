# 🛡️ Building a Cloud SOC + Honeynet in Microsoft Azure (Live Attack Traffic)

![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

## 📘 Overview

This project demonstrates how I built a **live cloud-based honeynet** in Microsoft Azure to capture **real-world cyberattacks**, analyze them in **Microsoft Sentinel**, and then **harden the environment** to measure the impact of security controls.

I intentionally deployed a vulnerable Windows VM to the internet, collected brute-force and scanning activity from global attackers, visualized the data using Sentinel workbooks, and then secured the environment using NSGs, firewalls, and private endpoints.

This project showcases the full lifecycle of SOC work:  
**exposure → detection → investigation → enrichment → hardening → validation.**

---

# 🧰 Skills Demonstrated

- Azure Cloud Architecture  
- Security Operations (SOC)  
- Microsoft Sentinel (SIEM)  
- Log Analytics Workspace (LAW)  
- Windows Event Log Analysis  
- Kusto Query Language (KQL)  
- Threat Detection & Investigation  
- Attack Surface Reduction  
- NSG, Firewall & Private Endpoint Hardening  
- Visualizing attacker behavior & global pattern analysis  

---

# 🏗️ Architecture

## **Before Hardening (Deliberately Vulnerable)**

To attract real attackers, I deployed the VM with:

- Public RDP exposure  
- NSGs allowing **Any / Any / 0.0.0.0/0**  
- Windows Firewall disabled  
- Public endpoints on key services  

The goal was to **simulate a misconfigured production environment** and observe how attackers behave when given access to an exposed system.

**Diagram:**  
`/assets/diagrams/before-hardening.png`

---

## **After Hardening (Secure Configuration)**

Once data was collected, I secured the entire environment:

- NSGs locked down to a single trusted admin IP  
- Windows Firewall fully enabled  
- All Azure resources moved behind **Private Endpoints**  
- Public access removed  
- RDP access isolated  
- Continuous monitoring enabled through Sentinel  

The goal was to demonstrate how security controls **dramatically reduce** malicious traffic while maintaining operational functionality.

**Diagram:**  
`/assets/diagrams/after-hardening.png`

---

# 📊 Key Findings

### 🔥 Before Hardening
During the exposed phase, the honeypot collected:

- **Thousands of brute-force login attempts** within the first 24 hours  
- Attacks from **40+ countries**  
- Repeated attempts using common usernames like:  
  `admin`, `administrator`, `test`, `user1`  
- Continuous port scanning and probing of RDP/SMB services  

This validated that **any public-facing VM is immediately targeted** by automated bots and malicious actors.

---

### 🔒 After Hardening
Once hardening was implemented:

- **100% reduction** in unauthorized RDP login attempts  
- No new failed logon (4625) events  
- Public traffic dropped to zero  
- All services became reachable only from trusted sources  
- Attack surface was fully eliminated  

This demonstrates a complete SOC remediation cycle with **measurable security improvement**.

---

# 🔍 Example KQL Queries Used

### **1. Detecting Failed RDP Logons (Event ID 4625)**

```kql
SecurityEvent
| where EventID == 4625
| extend IPAddress = tostring(parse_json(AdditionalFields).IpAddress)
| summarize FailedLogins = count() by IPAddress
| sort by FailedLogins desc

## 🌐 Geo-IP Enrichment for Attack Mapping

### KQL Query

```kql
SecurityEvent
| where EventID == 4625
| extend IP = tostring(parse_json(AdditionalFields).IpAddress)
| lookup kind=leftouter geoWatchlist on $left.IP == $right.ip
| summarize count() by Country

These queries powered the interactive **attack map**, visualizing which countries were actively targeting the honeypot.

---

# 🌍 Attack Map (Sentinel Workbook)

Using a custom watchlist and KQL enrichment, I created a workbook that plotted attacker origins globally.

This revealed real-world attack distribution, which is useful for:

- Threat intelligence  
- Reporting  
- SOC dashboards  
- Understanding attacker access patterns  

**Screenshot:**  
`/assets/images/attack-map.png`

---

# 🪪 Why This Project Matters

This project replicates the real responsibilities of a SOC Analyst, including:

✔ Building and monitoring cloud assets  
✔ Collecting and analyzing security logs  
✔ Investigating alerts using a SIEM  
✔ Correlating data using KQL  
✔ Visualizing attacker patterns  
✔ Hardening infrastructure  
✔ Proving the impact of security controls  

It demonstrates not only strong technical execution—but also an understanding of **why** each step matters from a security operations perspective.

---

# 🎯 Final Summary

This Azure honeynet project demonstrates my ability to:

- Deploy and manage Azure infrastructure  
- Capture and investigate real cyberattacks  
- Write KQL queries for detection and analytics  
- Use Microsoft Sentinel effectively  
- Build enriched visual dashboards  
- Apply and validate security hardening  
- Think like both an attacker *and* a defender  

---
