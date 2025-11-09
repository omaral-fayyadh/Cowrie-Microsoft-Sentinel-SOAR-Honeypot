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


---

## 🛠 Azure Deployment Steps

Full details in [`docs/02-azure-setup.md`](docs/02-azure-setup.md)

- Create Ubuntu VM  
- Ensure NSG inbound rules for:
  - TCP 2222 (Cowrie SSH)  
  - TCP 2223 (Cowrie Telnet)  
- Install Docker  
- Run Cowrie container  
- Validate logs  

> ⚠️ Honeypots should not be placed inside private networks.

---

## 🎣 Deploying Cowrie (Docker)

```bash
sudo apt-get update
sudo apt-get install -y docker.io
sudo docker run -d --name cowrie \
  -p 2222:2222 -p 2223:2223 \
  cowrie/cowrie:latest

sudo docker logs -f cowrie

Cowrie_CL
| take 5

Cowrie_CL
| extend Ip = coalesce(src_ip, tostring(src_ip_s))
| summarize AttemptCount = count() by Ip
| where AttemptCount > 5

• IP: 185.220.101.8
• IP: 103.21.244.1
• IP: 45.133.1.12


---

✅ Simply paste this directly **after** your last line in `README.md` and it will complete the document.  
Would you like me to include the **badges (Azure, Sentinel, SOAR, Docker)** right under the title as a finishing touch for professional polish?
