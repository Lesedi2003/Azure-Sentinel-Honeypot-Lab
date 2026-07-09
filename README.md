# 🛡️Azure Sentinel Honeypot - Attack Detection and Investigation Lab

## 📖 Overview

This project demonstrates the deployment of a cloud-based Security Operations Center (SOC) lab using Microsoft Azure and Microsoft Sentinel.

An internet-facing Windows Server virtual machine was intentionally configured as a honeypot to attract unauthorized authentication attempts. Windows Security Events were collected through the Azure Monitor Agent, stored in a Log Analytics Workspace, and investigated using Kusto Query Language (KQL).

To provide additional context during the investigation, a GeoIP Watchlist was used to enrich attacker IP addresses with geographic information. The enriched data was then visualized using a Microsoft Sentinel Workbook, allowing authentication attempts to be viewed on an interactive world map.

This project demonstrates practical experience with SIEM deployment, Windows event collection, threat investigation, log analysis, data enrichment, and security visualization using Microsoft Sentinel.

---



# 🚀 Future Improvements

This project focused on collecting and investigating authentication events using Microsoft Sentinel.

With the continuation of developing my SOC skills, I plan to expand this lab by implementing additional Microsoft security technologies, including:

- ⚡ Microsoft Sentinel Automation Rules
- 🤖 Microsoft Sentinel SOAR Playbooks using Azure Logic Apps
- 🚨 Automated incident creation and response
- 🛡️ Microsoft Defender for Endpoint integration
- 📧 Microsoft Defender for Office 365 integration
- ☁️ Microsoft Defender for Cloud integration
- 📈 Advanced threat hunting using KQL
- 🔔 Custom analytics rules and alerting

---

# 🙏 Thank You

Thank you for taking the time to explore this project.

---

## 👨‍💻 Author

**Lesedi**

- SC-200: Microsoft Security Operations Analyst
- SC-300: Microsoft Identity and Access Administrator
- Aspiring SOC Analyst
