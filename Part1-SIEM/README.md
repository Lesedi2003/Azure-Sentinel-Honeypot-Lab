# 📌 Project Scenario

Imagine you're a SOC analyst responsible for monitoring a Windows server that's connected to the internet.

The first question isn't **"Are we under attack?"**

It's **"What activity is happening on this machine?"**

To answer that question, security events first need to be collected, stored, and analyzed.

In this Lab, I built a simple SOC environment that allows exactly that.

As soon as the virtual machine was exposed to the internet, it began receiving unsolicited authentication attempts from automated scanners and bots. Microsoft Sentinel collected those events, allowing them to be investigated using KQL, enriched with geographic information, and visualized through an interactive dashboard.

---

# 🎯 Lab Objectives

The main goals of this lab were to:

- Deploy an internet-facing Windows honeypot.
- Configure Microsoft Sentinel as the SIEM platform.
- Collect Windows Security Events using the Azure Monitor Agent (AMA).
- Investigate authentication events using Kusto Query Language (KQL).
- Enrich attacker IP addresses with geographic information.
- Visualize attack activity using Microsoft Sentinel Workbooks.

---

## 🏗️ Lab Architecture
This diagram shows how security events move from the virtual machine into Microsoft Sentinel for investigation.
```text
                           Internet
                               │
                               ▼
                       Windows Server VM
                           (Honeypot)
                               │
                    Windows Security Events
                         (Event Viewer)
                               │
                               ▼
                    Azure Monitor Agent (AMA)
                               │
                               ▼
                   Log Analytics Workspace
                        (SecurityEvent)
                               │
                               ▼
                   Microsoft Sentinel (SIEM)
                               │
              KQL Queries & Threat Investigation
                               │
                               ▼
                  GeoIP Watchlist Enrichment
                               │
                               ▼
                  Microsoft Sentinel Workbook
```

---

## 🛠️ Technologies Used

| Technology | Purpose |
|------------|---------|
| Microsoft Azure | Cloud platform used to host the lab |
| Windows Server | Internet-facing honeypot |
| Azure Virtual Network | Network for the virtual machine |
| Network Security Group (NSG) | Controls inbound and outbound network traffic |
| Azure Monitor Agent (AMA) | Collects Windows Security Events |
| Log Analytics Workspace (LAW) | Stores collected logs |
| Microsoft Sentinel | SIEM platform used for monitoring and investigation |
| Kusto Query Language (KQL) | Used to query and investigate security events |
| Microsoft Sentinel Watchlists | Used for GeoIP enrichment |
| Microsoft Sentinel Workbooks | Used to visualize attack activity |

---

# 🏗️ Building the Azure Environment

Before Microsoft Sentinel could collect and analyze security events, the Azure environment first needed to be built.

This section covers the infrastructure that forms the foundation of the lab.

---

## 📁 Creating the Resource Group

Every Azure deployment starts with a **Resource Group**.

Think of it as a container that keeps all related Azure resources together. Instead of managing each resource individually, the Resource Group allows everything to be organized in one place, making the lab easier to manage, monitor, and eventually remove when it is no longer needed.

For this project, I created a dedicated Resource Group specifically for the SOC lab, thats gonna contain resources.

![Resource Group](../SIEM-Screenshots/01.Resource-group-overview.png)

---

## 🌐 Creating the Virtual Network

Next, I created an Azure **Virtual Network (VNet)**.

A Virtual Network works much like a private network inside Azure. It provides secure communication between Azure resources while allowing the virtual machine to receive both a private IP address for internal communication and a public IP address for internet access.

Even though this lab only uses a single virtual machine, deploying it inside its own Virtual Network reflects how cloud environments are commonly designed in production.

![Virtual Network](../SIEM-Screenshots/02.Virtual-Network.png)

---

## 💻 Deploying the Honeypot

With the network in place, I deployed a **Windows Server Virtual Machine**.

Rather than hosting applications or business services, this virtual machine was created to act as a **honeypot**.

A honeypot is a system that is intentionally exposed to potential attackers so that suspicious activity can be observed and analyzed without placing production systems at risk.

Throughout this project, the virtual machine generates the Windows Security Events that Microsoft Sentinel later collects and investigates.

![Virtual Machine](../SIEM-Screenshots/03.Virtual-Machine.png)

---

## 🔓 Intentionally Exposing the Virtual Machine

By default, Azure virtual machines are protected through Network Security Groups (NSGs). An NSG is a set of rules that allow or deny inbound and outbound traffic to resources like VMs or subnets

For this lab, I intentionally created an inbound rule named **DANGER_AllowAnyCustomAnyInbound**, which allows **all inbound network traffic** to reach the virtual machine.

Allowing unrestricted inbound traffic makes the virtual machine much more visible to internet scanners, bots, and attackers. This increases the chances of receiving real attack activity that can later be investigated inside Microsoft Sentinel.

> ⚠️ **Note:** This configuration was created only for this controlled lab. In a production environment, you’d never do that, it’s a huge security risk — it’s like leaving your front door wide open, just to see who walks in. Definitely not something you’d do at home, but perfect for this experiment.

![Network Security Group](../SIEM-Screenshots/04.NSG-Allow-Traffic.png)

---

# ✅ Environment Ready

At this stage, the Azure infrastructure was fully deployed.

The environment now consisted of:

- A dedicated Azure Resource Group
- A Virtual Network
- A Windows Server virtual machine acting as a honeypot
- A public IP address
- A Network Security Group intentionally allowing inbound traffic

> 💡 **Did you know?**
>
> It took less than an hour after exposing the virtual machine to the internet before automated login attempts started appearing in the Windows Security logs. This highlights how quickly internet-facing systems are discovered by automated scanners.

With the infrastructure complete, the next step was preparing the honeypot so it could begin attracting and recording security events.

# 🛠️ Bringing the Honeypot Online

With the Azure infrastructure in place, the next step was preparing the virtual machine so it could begin generating meaningful security events.

The goal wasn't simply to deploy a Windows Server—it was to create a machine that would be visible on the internet, capable of attracting connection attempts, and able to produce the Windows Security Events that Microsoft Sentinel would later analyze.

---

## 🔑 Connecting to the Virtual Machine

After the virtual machine was deployed, I connected to it using **Remote Desktop Protocol (RDP)**.

This confirmed that:

- The virtual machine was running successfully.
- The public IP address was reachable.
- Remote Desktop was functioning correctly.
- The environment was ready for the remaining configuration steps.

Successfully connecting to the server also confirmed that the Network Security Group configuration was working as expected.

![RDP Connection](../SIEM-Screenshots/05.Connected-VM-to-Windows.png)

---

## 🔥 Disabling Windows Defender Firewall

By default, Windows Defender Firewall blocks many types of incoming network traffic.

For this project, I temporarily disabled the Windows Firewall to make the virtual machine more visible to internet scanners and automated attack tools.

This increases the likelihood of receiving unsolicited traffic, allowing Microsoft Sentinel to capture real authentication attempts and other security events.

> ⚠️ **Note:** Disabling Windows Firewall is intentionally insecure and should never be done on production systems. It was performed only for this controlled cybersecurity lab.

![Windows Firewall Disabled](../SIEM-Screenshots/06.Diable-Windows-Firewall.png)

---

## 🌍 Verifying Internet Connectivity

To confirm that the virtual machine could be reached from the internet, I performed a connectivity test using the server's public IP address.

Receiving successful responses confirmed that:

- The virtual machine was online.
- Network traffic could successfully reach the server.
- The honeypot was now visible to external systems.

At this point, the environment was ready to begin attracting real-world network activity.

![Connectivity Test](../SIEM-Screenshots/07.Ping-VM.png)

---

## 📝 Understanding Windows Security Events

Before forwarding logs into Microsoft Sentinel, I first wanted to understand how Windows records security activity locally.

Whenever someone attempts to sign in to Windows, the operating system automatically records the event inside **Windows Event Viewer**.

These logs become extremely valuable during security investigations because they contain details such as:

- The time of the event
- The username that was used
- Whether the login succeeded or failed
- The source IP address (for network logons)
- The authentication method

Later in this project, these same events are collected by the Azure Monitor Agent and stored inside the **SecurityEvent** table in the Log Analytics Workspace.

![Windows Event Viewer](../SIEM-Screenshots/Windows-Event-Viewer.png)

---

## ❌ Generating a Failed Logon (Event ID 4625)

To generate a real security event, I intentionally attempted to log into the virtual machine using incorrect credentials.

Windows immediately recorded this as **Event ID 4625 – An account failed to log on.**

Failed logon events are some of the most commonly investigated events in a Security Operations Center because they can indicate:

- Incorrect passwords
- Brute-force attacks
- Password spraying
- Automated login attempts
- Unauthorized access attempts

Later in the project, these same events become the primary source of data used for threat hunting within Microsoft Sentinel.

![Failed Logon](../SIEM-Screenshots/08.Logon-Failed.png)

---

## ✅ Generating a Successful Logon (Event ID 4624)

I also performed a successful login to compare the difference between successful and failed authentication events.

Windows records successful authentication using **Event ID 4624 – An account was successfully logged on.**

Having both successful and failed logon events available provides valuable context during an investigation.

For example, an analyst might investigate whether a failed login attempt was eventually followed by a successful login from the same IP address or account.

Understanding the relationship between these events helps identify suspicious authentication activity.

![Successful Logon](../SIEM-Screenshots/Successful-Logon.png)

---

> 💡 **Why does this matter?**

Everything that happens later in this project starts here.

Microsoft Sentinel cannot investigate attacks unless Windows first records them.

The Event Viewer acts as the starting point of the investigation, capturing authentication events before they are collected by Azure Monitor and stored in the Log Analytics Workspace.

---
# 📡 Collecting Security Events with Microsoft Sentinel

At this stage, the honeypot was online and Windows was successfully recording authentication events inside Event Viewer.

However, those events were still stored locally on the virtual machine.

To investigate them using Microsoft Sentinel, they first needed to be collected and sent to Azure.

This section explains how that process works.

---

## 🗄️ Creating the Log Analytics Workspace

The first service I deployed was an **Azure Log Analytics Workspace (LAW).**

Think of the Log Analytics Workspace as a central storage location for logs.

Instead of leaving Windows Security Events on the virtual machine, Azure collects and stores them inside the Log Analytics Workspace where they can later be searched using Kusto Query Language (KQL).

Without a Log Analytics Workspace, Microsoft Sentinel would have no security data to analyze.

![Log Analytics Workspace](../SIEM-Screenshots/09.LAW.png)

---

## 🛡️ Connecting Microsoft Sentinel

After creating the Log Analytics Workspace, I connected it to **Microsoft Sentinel**.

Microsoft Sentinel is Microsoft's cloud-native **Security Information and Event Management (SIEM)** platform.

Rather than storing logs itself, Sentinel uses the Log Analytics Workspace as its data source.

Once connected, Sentinel can search, investigate, visualize, and alert on the security events stored inside the workspace.

![Microsoft Sentinel](../SIEM-Screenshots/Sentinel-Connected.png)

---

## 📥 Configuring the Windows Security Events Connector

The next step was enabling the **Windows Security Events via AMA** data connector.

A data connector tells Microsoft Sentinel **what type of logs should be collected**.

In this project, the connector was configured to collect Windows Security Events generated by the virtual machine.

Without enabling this connector, Windows authentication events would never reach Microsoft Sentinel.

![Windows Security Events Connector](../SIEM-Screenshots/10.Windows-Security-Event.png)

---

## 🚀 Installing the Azure Monitor Agent (AMA)

Once the connector was configured, I installed the **Azure Monitor Agent (AMA)** on the virtual machine.

The Azure Monitor Agent acts like a courier between Windows and Azure.

Whenever Windows records a new Security Event, the Azure Monitor Agent collects it and securely sends it to the Log Analytics Workspace.

Without the Azure Monitor Agent, Windows would continue recording events locally, but Microsoft Sentinel would never see them.

![Azure Monitor Agent](../SIEM-Screenshots/11.AMA.png)

---

## ✅ Verifying Log Collection

To confirm that everything was configured correctly, I queried the SecurityEvent table and verified that Windows authentication events were appearing inside Microsoft Sentinel.

Seeing these events arrive successfully confirmed that:

- Windows was generating Security Events.
- The Azure Monitor Agent was forwarding those events.
- The Log Analytics Workspace was storing them.
- Microsoft Sentinel was able to query and investigate the collected data.

With log collection successfully configured, the environment was now ready for threat hunting using Kusto Query Language (KQL).

### Verify Security Event Ingestion

Once the Azure Monitor Agent began forwarding telemetry, Kusto Query Language (KQL) was used to verify that Windows Security Events were successfully arriving in the Log Analytics Workspace.

The following query displays all Windows Security Events:

![Security Event table](../SIEM-Screenshots/12.Logs-generated.png)


---
# 🔍 Investigating Attack Activity

With Windows Security Events successfully flowing into Microsoft Sentinel, I could begin investigating the activity being recorded against the honeypot.

The primary objective during this stage was to identify failed authentication attempts, investigate where they were coming from, and enrich the collected data to gain a better understanding of the attacks.

Kusto Query Language (KQL) was used throughout this investigation to search, filter, and analyze the collected logs.

---

## ❌ Investigating Failed Logon Attempts

One of the most common indicators of suspicious activity is repeated failed login attempts.

Windows records these events using **Event ID 4625**, making them easy to identify with KQL.

```kusto
SecurityEvent
| where EventID == 4625
```

This query returns every failed authentication attempt recorded by the virtual machine.
> ⚠️ **Note:**
>
> Earlier in this project, I examined Windows Security Events directly on the virtual machine using **Event Viewer** to understand how Windows records authentication activity.
>
> In this section, I use the **same event IDs and KQL queries**, but this time the investigation is performed against the **SecurityEvent** table in the **Log Analytics Workspace**. These are the same Windows Security Events, now collected by the Azure Monitor Agent and made available in Microsoft Sentinel for centralized analysis.
>
> This demonstrates how security events move from a single Windows machine into a SIEM, where they can be searched, investigated, and correlated using KQL.

Each event contains useful information including:

- Time of the attempt
- Username used
- Source IP address
- Authentication type
- Logon type

These details help analysts determine whether login attempts are likely to be legitimate user mistakes or malicious activity such as brute-force attacks.

![Failed Logons](../SIEM-Screenshots/13.Failed-Logon.png)

---

## 🌐 Investigating Attacker IP Addresses

After identifying failed login attempts, I investigated the source IP addresses responsible for the activity.

Looking at the IP address alone can already reveal useful information, including:

- Whether multiple usernames are being targeted
- Whether one IP is generating repeated login attempts
- Whether attacks originate from the same location

To better understand one of the attackers, I copied one of the observed IP addresses and searched it using a public IP lookup service.

This provided additional information such as:

- Country
- Internet Service Provider (ISP)
- Network owner
- Autonomous System Number (ASN)

Although the external IP lookup provided useful context, it required leaving Microsoft Sentinel and relying on a third-party service.

So, the goal of the was to bring that same geographic information directly into Microsoft Sentinel. By enriching the authentication logs with a Watchlist, I could view details such as the attacker's country, city, latitude, and longitude without leaving the SIEM platform.

This creates a more efficient investigation workflow, allowing analysts to investigate authentication events and their geographic origin from a single interface.

![IP Lookup](../SIEM-Screenshots/14.IPLookUp.png)

---

# 🌍 Adding Geographic Context

Knowing an IP address is useful.

Knowing **where it came from** is even more useful.

To enrich the authentication logs with geographic information, I downloaded a GeoIP dataset and uploaded it into Microsoft Sentinel as a **Watchlist**.

According to Microsoft, a **Watchlist** enables the collection of data from external sources so it can be correlated with events in a Microsoft Sentinel environment.

In this project, the Watchlist acts as a reference table that Microsoft Sentinel uses to match attacker IP addresses with geographic information such as:

- Country Name
- City name
- Latitude
- Longitude

This additional context makes it much easier to understand where authentication attempts are originating and helps identify patterns during the investigation.

![GeoIP Dataset](../SIEM-Screenshots/15.geo-spreadsheet.png)

---

## 📋 Creating the GeoIP Watchlist

After preparing the dataset, I created a Microsoft Sentinel Watchlist named **geoip**.

This allows KQL to compare attacker IP addresses against the GeoIP database and automatically return location information.

Instead of only seeing an IP address, I could now identify the country and city associated with each attack in sentinel, not having to go out to look for it somewhere else.

![GeoIP Watchlist](../SIEM-Screenshots/16.Watchlist.png)

---
## 📋 Verifying the GeoIP Watchlist

After creating the GeoIP Watchlist, I verified that Microsoft Sentinel had successfully imported the data.

Using the `_GetWatchlist()` function, I was able to retrieve the contents of the Watchlist and confirm that it contained the expected geographic information.

The Watchlist now included details such as:

- IP address ranges (network)
- Country Name
- City Name
- Latitude
- Longitude

These are the same geographic details that were identified during the external IP lookup earlier in the investigation. However, instead of relying on a third-party service, the information is now available directly within Microsoft Sentinel.

Rather than modifying the original **SecurityEvent** table, the Watchlist acts as a reference dataset. This provides additional context for investigations while keeping the entire analysis within Microsoft Sentinel.

This verification step confirmed that the Watchlist was ready to be used for data enrichment during the investigation.

```kusto
_GetWatchlist("geoip")
```
![Get Watchlist](../SIEM-Screenshots/17.Location-Detected-Logs.png)

---

## 🧠 Enriching Security Events with KQL

Once the Watchlist had been uploaded, I used the `ipv4_lookup()` function to combine the failed authentication logs with the GeoIP dataset.The `ipv4_lookup()` function enriches the authentication logs by adding geographic information to the query results.

```kusto
let GeoIPDB_FULL = _GetWatchlist("geoip");

let WindowsEvents =
SecurityEvent
| where EventID == 4625
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);

WindowsEvents
| project
    TimeGenerated,
    Account,
    IpAddress,
    Country = countryname,
    City = cityname,
    Latitude = latitude,
    Longitude = longitude
```

This process is known as **data enrichment**.

For example, instead of only seeing:

```
5.228.117.132
```

I could now see something like:

```
IP Address: 5.228.117.132
Country: Russia
City: Moscow
Latitude: ...
Longitude: ...
```

This makes it much easier to understand where authentication attempts are originating.

![GeoIP KQL Query](../SIEM-Screenshots/18.Specified-IPadress-Location.png)

---

## 💡 What I Learned

Before completing this section, I assumed Microsoft Sentinel automatically knew where every IP address was located.

Building this lab taught me that this isn't the case.

Microsoft Sentinel only knows what exists in the logs.
Rather than thinking of Microsoft Sentinel as a tool that magically "knows" what's happening on a machine, I learned that every investigation begins with Windows generating security events, the Azure Monitor Agent collecting them, and the Log Analytics Workspace storing them before Sentinel can analyze them.

To add geographic information, the logs first need to be enriched using external reference data such as a GeoIP Watchlist.

Understanding this concept helped me appreciate how security analysts combine multiple data sources to build a clearer picture during an investigation.


---

# 🌍 Visualizing Attack Activity

After enriching the authentication logs with geographic information, the final step was to visualize the collected data using a **Microsoft Sentinel Workbook**.

A Workbook provides an interactive dashboard that transforms raw log data into charts, tables, and maps, making it much easier to identify attack patterns and communicate findings.

---

## 📊 Creating the Workbook

Using Microsoft Sentinel, I created a Workbook that queries the enriched authentication logs and displays them in a geographical view.

The workbook uses the latitude and longitude values from the GeoIP Watchlist to plot failed authentication attempts on a world map.

This provides a much clearer view of where attack activity is originating compared to viewing raw log entries alone.

![Workbook Creation](../SIEM-Screenshots/19.workbook-creation.png)

---

## 🗺️ Investigating Attack Locations

Once the workbook was configured, the authentication attempts were displayed on an interactive world map.

Each marker represents one or more failed authentication attempts from a specific location.

Selecting a marker displays additional information about the attacks, including:

- Source IP Address
- Country
- City
- Number of authentication attempts

This visualization makes it easier to identify attack hotspots and understand where malicious activity is originating.
It’s oddly satisfying to see the attacks light up across the globe.

![Attack Map](../SIEM-Screenshots/20.VM-workbook-Graph.png)

---

# 💡 Lessons Learned

Before completing this project, I understood the individual Azure services but didn't fully understand how they worked together in a security monitoring environment.

Building this lab helped me understand the complete lifecycle of a security event:

1. Windows generates a Security Event.
2. The Azure Monitor Agent collects the event.
3. The event is sent to the Log Analytics Workspace.
4. Azure stores the event in the **SecurityEvent** table.
5. Microsoft Sentinel queries the logs using KQL.
6. Watchlists enrich the data with additional context.
7. Workbooks visualize the investigation results.

Understanding this workflow gave me a much clearer picture of how Microsoft Sentinel functions as a cloud-native SIEM platform.

---
## Conclusion

In this phase of the project, a cloud-based SIEM environment was successfully deployed using Microsoft Azure and Microsoft Sentinel. A Windows Server virtual machine was configured as a honeypot and exposed to the internet to generate real-world security events. Azure Monitor and Log Analytics Workspace were used to collect and store Windows Security Event logs, while Microsoft Sentinel provided centralized monitoring and analysis capabilities.

Using Kusto Query Language (KQL), security events were queried and analyzed to identify failed Remote Desktop Protocol (RDP) authentication attempts originating from various locations around the world. The collected telemetry was further visualized on a geographic attack map, providing valuable insight into attacker activity and demonstrating the effectiveness of centralized log analysis.

This implementation established the detection and monitoring foundation of a modern Security Operations Center (SOC). In the next phase of the project, the environment will be enhanced with Security Orchestration, Automation, and Response (SOAR) capabilities by implementing analytics rules, incident management, automation rules, and Logic App playbooks to automate threat response workflows.


