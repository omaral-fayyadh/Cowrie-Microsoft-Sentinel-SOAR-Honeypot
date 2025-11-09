 <div align="center">

# 🛡️ Cowrie Honeypot → Microsoft Sentinel SOAR Automation (End-to-End)

![Azure](https://img.shields.io/badge/Azure-Cloud-blue?logo=microsoftazure)
![Microsoft Sentinel](https://img.shields.io/badge/Microsoft%20Sentinel-SIEM-blue?logo=microsoft)
![SOAR](https://img.shields.io/badge/SOAR-Automation-green)
![Docker](https://img.shields.io/badge/Docker-Container-lightblue?logo=docker)
![KQL](https://img.shields.io/badge/KQL-Detection-orange?logo=databricks)

</div>


---

## 🧩 Overview

This project implements a **full cloud security pipeline** combining deception technology (Cowrie honeypot), threat telemetry ingestion through **Microsoft Defender/XDR connectors**, **Microsoft Sentinel (SIEM)**, and cloud-native **SOAR automation** using Logic Apps.

> 🎯 **Goal:** Automatically enrich Sentinel incidents with real-world attacker data collected from an internet-exposed honeypot.

This reduces analyst triage time and surfaces actionable threat intelligence with zero manual effort.

---

## 🚀 High-Level Flow

```text
Internet Attackers
        │
        ▼
Azure VM (Ubuntu) running Cowrie (ports 2222/2223)
        │  cowrie.json logs
        ▼
Azure Monitor Agent (AMA) + Data Collection Rule (DCR)
        │  custom table (Cowrie_CL)
        ▼
Microsoft Sentinel (Log Analytics Workspace)
        │
Scheduled Analytics Rule triggers Incident
        │
        ▼
Logic App (Playbook)
  Parse Entities → Filter IPs → Append Summary → Post Comment
```

---

## 🎯 Project Objectives

✅ Deploy an Internet-facing honeypot to collect attacker telemetry  
✅ Route security events into Microsoft Sentinel  
✅ Generate incidents from suspicious behavior  
✅ Automate enrichment inside the incident response workflow  
✅ Demonstrate **SOAR and Cloud Security Engineering** skills  
✅ Showcase an **end-to-end defensive architecture**

---
💡 Why This Matters
---

Security analysts handle dozens of alerts per shift. Automating triage saves valuable time by immediately adding context like:

🔁 Repeated attacker IPs

🌎 Geographic clusters

💥 Brute-force attempts

⚙️ Automated scans and probes

This playbook automatically:

**1.** Extracts entities

**2.** Loops through each indicator

**3.** Filters by type (IPs)

**4.** Formats a triage summary

**5.** Posts results into the Sentinel incident comment pane

✨ Impact: Small automation, huge SOC efficiency gain.

## 🔐 Technologies Used

| **Category**        | **Tools** |
|----------------------|-----------|
| Honeypot             | Cowrie (SSH/Telnet) |
| Cloud Platform       | Azure Virtual Machine |
| Log Ingestion        | Azure Monitor Agent (AMA), Data Collection Rules (DCR) |
| SIEM                 | Microsoft Sentinel |
| SOAR                 | Azure Logic Apps |
| Detection            | KQL Analytics Rules |
| Entity Modeling      | Microsoft Defender XDR |
| Containerization     | Docker |

🧱 Architecture Diagram
---
```text
+--------------------+
|  Attackers         |
|  Internet Traffic  |
+---------+----------+
          |
          v
+---------+----------+
| Azure VM (Cowrie)  |
| Ports 2222/2223    |
+---------+----------+
          |
          v
+--------------------+
| Azure Monitor Agent|
| DCR → Cowrie_CL    |
+---------+----------+
          |
          v
+--------------------+
| Log Analytics      |
| Microsoft Sentinel |
+---------+----------+
          |
          v
+--------------------+
| Analytics Rule     |
| Incident Creation  |
+---------+----------+
          |
          v
+--------------------+
| Logic App (SOAR)   |
| Parse Entities     |
| Loop IPs           |
| Comment Incident   |
+--------------------+
```

🛠️ Azure Deployment Steps
---
Full details in ```text docs/02-azure-setup.md ```

**1. Create the VM**

**OS:** Ubuntu LTS  
**Size:** B1s (sufficient for testing)  
**Authentication:** SSH Key  
**Public IP:** Static


**2. Configure NSG Rules**
| **port**        | **Purpose**    | **Protocol** |
|-----------------|----------------|--------------|
| 2222            | Cowrie SSH     | TCP          |
| 2223            | Cowrie Telnet  | TCP          |

|⚠️ Warning: Never host a honeypot inside private or production networks.

🎣 Deploying Cowrie (Docker)
---
```text
sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl enable --now docker

sudo docker run -d --name cowrie \
  -p 2222:2222 -p 2223:2223 \
  cowrie/cowrie:latest

View live logs:
sudo docker logs -f cowrie
```

📥 Log Ingestion to Sentinel
---

- Install Azure Monitor Agent (AMA)

- Create a Data Collection Rule (DCR) targeting ```/opt/cowrie-data/cowrie.json```

- Output to a custom table: Cowrie_CL_CL

Example validation query:
```
Cowrie_CL_CL
| take 5
```

🔔 Analytics Rule (KQL)
---

Detect repeated attacker attempts and create incidents automatically:
```
Cowrie_CL_CL
| extend Ip = coalesce(src_ip, tostring(src_ip_s))
| summarize AttemptCount = count() by Ip
| where AttemptCount > 5
```

📄 Template: ```analytics/cowrie-incident-rule.json```

🤖 Sentinel Playbook (Logic App)
---
This Logic App parses incident entities, loops through indicators, extracts IPs, builds a summary, and posts it back into the incident.

🧩 **Workflow Steps**

**1.Trigger:** Sentinel incident

**2.Get incident details**

**3.Parse JSON:**

- **Content: ```@{triggerBody()?['Entities']}```

**4.Initialize variable:** ``summary`` (String)

**5.For each entity:** ``body('Parse_JSON')``

**6.Condition:** ``toLower(coalesce(item()?['Type'], item()?['$type'])) == 'ip'``

**7.Append to summary:**

```concat('• IP: ', coalesce(item()?['Address'], item()?['IpAddress']), '\n')```

**8.Add comment:** ```variables('summary')```

📝 Example Output (Incident Comment)
---
```
• IP: 185.220.101.8
• IP: 103.21.244.1
• IP: 45.133.1.12
```
✅ Validation Checklist
---

 - [x] Cowrie receiving hits on ports 2222/2223

 - [x] Logs populating custom table ```Cowrie_CL_CL```

 - [x] Analytics rule triggers incidents

 - [x] Playbook executes successfully

 - [x] Comment appears in Sentinel incident

🧠 Lessons Learned
---

- Entity schemas differ between alerts

- Always handle $type and Type casing

- Export Logic Apps for reusability

- AMA + DCR combo simplifies log forwarding

- SOAR automation greatly reduces triage fatigue

🎓 Professional Takeaways
---

- Cloud-native SOC automation

- Real-world threat telemetry ingestion

- KQL-based detection engineering

- Incident enrichment & response automation

- End-to-end SIEM + SOAR pipeline buildout

|👩‍💻 This mirrors workflows of SOC Automation Engineers, Cloud Security Analysts, and Detection Engineers.

🔮 Future Enhancements

🌍 GeoIP + ASN enrichment

🧠 VirusTotal / AbuseIPDB integration

🏷️ Auto-tagging incidents

🚨 Severity escalation by IOC reputation

👥 Auto-assign analysts based on workload

🏁 Conclusion

This project captures real-world attacker telemetry, forwards it to Sentinel, and enriches incidents automatically — demonstrating an end-to-end threat detection and automation pipeline built entirely on Microsoft Azure.

💡 From honeypot to SOAR — every alert now tells a story.

📄 License

MIT © 2025 Omar Alfayyadh

