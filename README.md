# Cowrie Honeypot → Microsoft Sentinel SOAR Automation (End-to-End)

This project implements a **full cloud security pipeline** combining deception technology (Cowrie honeypot), threat telemetry ingestion through Microsoft Defender/XDR data connectors, **Microsoft Sentinel SIEM**, and cloud-native **SOAR automation** using Logic Apps.

The end result:
> Automated enrichment notes are posted directly into Sentinel incidents the moment attackers hit the honeypot.

This reduces analyst triage time and surfaces high-value intelligence with zero manual effort.

---

## 🚀 High-Level Flow


---

## 🎯 Goals of This Project

✅ Deploy an Internet-facing honeypot to collect attacker telemetry  
✅ Route security events into Microsoft Sentinel  
✅ Generate incidents from suspicious behavior  
✅ Automate enrichment inside the incident response workflow  
✅ Demonstrate cloud SOAR engineering skills  
✅ Showcase end-to-end defensive architecture  

---

## 💡 Why This Is Useful

Security analysts often triage dozens of alerts per shift.  
Automating context such as:

- repeated attacker IPs
- brute force attempts
- geographic clusters
- probing behavior

…dramatically reduces manual investigation.

This playbook automatically:
- extracts entities
- loops indicator objects
- formats a triage summary
- posts results in the incident comment pane

It’s small automation with **large SOC impact**.

---

## 🔐 Technologies Used

- **Cowrie** (SSH/Telnet honeypot)
- **Azure Virtual Machine**
- **Azure Monitor Agent (AMA)**
- **Data Collection Rules (DCR)**
- **Log Analytics (Custom Tables)**
- **Microsoft Sentinel**
- **Analytics Rules (KQL)**
- **Logic Apps (SOAR Playbook)**
- **Defender XDR Entity Model**

---

## 🧱 Architecture Diagram

