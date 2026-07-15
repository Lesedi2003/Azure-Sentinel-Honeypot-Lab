# 🛡️ Building a Cloud SOC with Microsoft Sentinel (SIEM & SOAR)

## 📖 Overview

This project demonstrates the design and implementation of a cloud-native **Security Operations Center (SOC)** using **Microsoft Azure**, **Microsoft Sentinel**, and **Azure Logic Apps**.

An internet-facing Windows Server virtual machine was intentionally configured as a **honeypot** to attract unauthorized Remote Desktop Protocol (RDP) authentication attempts.

Windows Security Events were collected using the Azure Monitor Agent (AMA), stored in an Azure Log Analytics Workspace, and investigated using **Kusto Query Language (KQL)** within Microsoft Sentinel.

To provide additional context during investigations, attacker IP addresses were enriched with geographic information using a **GeoIP Watchlist**, allowing attack origins to be visualized through a Microsoft Sentinel Workbook.

The project was then extended by implementing **Security Orchestration, Automation, and Response (SOAR)**.

A custom Analytics Rule automatically detects repeated failed RDP authentication attempts and creates a Microsoft Sentinel Incident. An Automation Rule immediately launches an Azure Logic App Playbook, which automatically sends an email notification to the SOC analyst.

This project demonstrates the complete lifecycle of a modern Security Operations Center—from **log collection**, **threat detection**, **investigation**, and **visualization** to **automated incident response**.

---

# 🏗️ Overall Architecture

![SIEM + SOAR Architecture](SIEM-SOAR-Architecture/SIEM-SOAR-Architecture.png)


---

# 📂 Project Structure

```
Azure-SIEM-SOAR-Lab/
│
├── README.md
│
├── Part1-SIEM/
│   └── README.md
│
├── Part2-SOAR/
│   └── README.md
│
├── Architecture/
│   ├── Part1-SIEM-Architecture.png
│   ├── Part2-SOAR-Architecture.png
│   └── SIEM-SOAR-Architecture.png
│
├──KQL-Queries/
│
├── SIEM-Screenshots/
│
└── SOAR-Screenshots/
```

---

# 🚀 Project Walkthrough

## 🛰️ Part 1 — SIEM Implementation

The first phase of the project focuses on building the monitoring and detection environment.

Topics covered include:

- Azure Infrastructure Deployment
- Windows Server Honeypot
- Azure Virtual Network
- Network Security Groups
- Azure Monitor Agent (AMA)
- Log Analytics Workspace
- Microsoft Sentinel
- Windows Security Event Collection
- Kusto Query Language (KQL)
- Threat Hunting
- GeoIP Watchlists
- Workbook Dashboard

📖 **Read Part 1**

➡️ **[Part 1 – SIEM Implementation](Part1-SIEM/README.md)**

---

## 🤖 Part 2 — SOAR Implementation

The second phase extends Microsoft Sentinel by implementing automated incident response.

Topics covered include:

- Analytics Rules
- Incident Creation
- Logic App Playbooks
- Automation Rules
- Email Notification
- Playbook Run History
- Automated Response Workflow

📖 **Read Part 2**

➡️ **[Part 2 – SOAR Implementation](Part2-SOAR/README.md)**

---

# 🛠️ Technologies Used

| Technology | Purpose |
|------------|---------|
| Microsoft Azure | Cloud platform |
| Microsoft Sentinel | SIEM & SOAR Platform |
| Azure Logic Apps | Security automation |
| Azure Monitor Agent (AMA) | Windows Event Collection |
| Log Analytics Workspace | Log Storage |
| Windows Server | Honeypot |
| Azure Virtual Network | Networking |
| Network Security Group | Traffic Control |
| Kusto Query Language (KQL) | Threat Hunting |
| Microsoft Sentinel Watchlists | GeoIP Enrichment |
| Microsoft Sentinel Workbooks | Security Visualization |
| Outlook Connector | Automated Email Notification |

---

# 🎯 Skills Demonstrated

This project demonstrates practical experience with:

- Cloud Security
- Microsoft Sentinel
- Security Information and Event Management (SIEM)
- Security Orchestration, Automation and Response (SOAR)
- Windows Event Logging
- Azure Monitor
- Azure Logic Apps
- Log Analytics Workspace
- Incident Response
- Threat Hunting
- Kusto Query Language (KQL)
- Detection Engineering
- Analytics Rules
- Automation Rules
- Security Monitoring
- Data Enrichment
- Security Visualization

---

# 📚 What You'll Learn

By following this project, you'll see how a security event progresses through an entire SOC workflow:

```
Internet
      │
      ▼
Windows Honeypot
      │
      ▼
Windows Security Events
      │
      ▼
Azure Monitor Agent
      │
      ▼
Log Analytics Workspace
      │
      ▼
Microsoft Sentinel
      │
Analytics Rule
      │
      ▼
Security Incident
      │
      ▼
Automation Rule
      │
      ▼
Logic App Playbook
      │
      ▼
Email Notification
      │
      ▼
SOC Analyst
```

---

# 💡 Key Takeaways

Throughout this project I gained hands-on experience with:

- Building a cloud-native SOC in Microsoft Azure.
- Collecting and investigating Windows Security Events.
- Writing KQL queries for threat hunting.
- Enriching attacker IP addresses with GeoIP data.
- Creating Microsoft Sentinel Analytics Rules.
- Automating incident response using Azure Logic Apps.
- Building Microsoft Sentinel SOAR Playbooks.
- Validating automated workflows through Logic App Run History.

---

# 🔮 Future Improvements

Although this project demonstrates a complete SIEM and SOAR workflow, Microsoft Sentinel supports much more advanced automation.

Future enhancements may include:

- Microsoft Defender for Endpoint integration
- Microsoft Defender for Office 365 integration
- Microsoft Defender for Cloud integration
- Microsoft Entra ID incident response
- Microsoft Teams notifications
- ServiceNow ticket creation
- VirusTotal threat intelligence enrichment
- IP reputation lookups
- Automatic endpoint isolation
- User account remediation
- Automated incident enrichment
- Advanced KQL threat hunting
- Custom Detection Engineering

---

# 🙏 Thank You

Thank you for taking the time to explore this project.

I hope it provides useful insight into how Microsoft Sentinel can be used to build an end-to-end Security Operations Center capable of detecting, investigating, and responding to security incidents through automation.

---

# 👨‍💻 Author

**Lesedi Mogoane**

- Microsoft Certified: SC-200 – Security Operations Analyst Associate
- Microsoft Certified: SC-300 – Identity and Access Administrator Associate
- Aspiring SOC Analyst
