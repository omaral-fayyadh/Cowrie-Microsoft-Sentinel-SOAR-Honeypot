# 🛡️ Cowrie Honeypot → Microsoft Sentinel SOAR Automation (End-to-End)

![Azure](https://img.shields.io/badge/Azure-Cloud-blue?logo=microsoftazure)
![Microsoft Sentinel](https://img.shields.io/badge/Microsoft%20Sentinel-SIEM-blue?logo=microsoft)
![SOAR](https://img.shields.io/badge/SOAR-Automation-green)
![Docker](https://img.shields.io/badge/Docker-Container-lightblue?logo=docker)
![KQL](https://img.shields.io/badge/KQL-Detection-orange?logo=databricks)

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
