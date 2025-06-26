# 🛡️ AWS-Based SOC Home Lab

## 🎯 Overview
This Security Operations Center (SOC) lab simulates a cloud-based environment where attacks from a local Kali Linux VM are monitored and analyzed using Security Onion. It consists of cloud-hosted targets (Ubuntu Server, Windows Server with Sysmon) and a centralized SIEM system (Security Onion) for log collection, detection, and analysis.

---

## 🧱 Lab Architecture

```text
                 +-----------------------+
                 |     AWS VPC/Subnet    |
                 |                       |
                 |  +-----------------+  |
Local Kali VM <---> | Security Onion |<-- Sysmon (Windows)
 (VMware) Attacker |    (SIEM)       |<-- rsyslog (Ubuntu)
                 |  +-----------------+  |
                 |                       |
                 +-----------------------+
```

---

## 🔧 Components & Setup

### 🔹 1. Security Onion (SIEM) on AWS
- **AMI:** Security Onion 2 (Marketplace)
- **Instance Type:** t3.large or larger
- **Disk:** ≥ 100GB
- **Mode:** Evaluation Mode
- **Services:** Suricata, Zeek, Wazuh, Syslog
- **Access:** https://<SO_public_IP>

### 🔹 2. Ubuntu Linux Server (Target)
- **Purpose:** Host vulnerable services
- **Configuration:**
```bash
sudo apt update && sudo apt install apache2 vsftpd netcat -y

# Enable syslog forwarding
sudo nano /etc/rsyslog.conf
# Add:
*.* @<SecurityOnion_Private_IP>:514
sudo systemctl restart rsyslog
```

### 🔹 3. Windows Server 2019 (Target)
- **Install Sysmon:** [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- **Sysmon Config:** [SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config)
- **Log Forwarding:** Install [Winlogbeat](https://www.elastic.co/guide/en/beats/winlogbeat/current/index.html)

#### Winlogbeat Configuration Snippet
```yaml
output.logstash:
  hosts: ["<SecurityOnion_Private_IP>:5044"]
```

### 🔹 4. Kali Linux (Attacker - Local VM)
- **Tools:** `nmap`, `hydra`, `sqlmap`, `gobuster`, `smbclient`, `metasploit`
- **Attack Targets:**
  - Ubuntu (Apache, FTP)
  - Windows (SMB, RDP brute force)

---

## 📈 Detection & Monitoring
- **Security Onion Web UI:** `https://<SO_Public_IP>`
- **Analyze:**
  - Suricata alerts
  - Zeek logs
  - Sysmon/Windows logs (via Winlogbeat)
  - Ubuntu logs (via rsyslog)
- **Hunt:** Security Onion’s built-in hunt interface for threat analysis

---

## 🧪 Simulated Attacks
- Nmap scanning
- SSH brute force
- HTTP fuzzing (Gobuster)
- FTP password guessing
- SMB enumeration
- Reverse shells

---

## 🔐 Optional Enhancements
- Add Wazuh agent to Ubuntu
- Integrate with TheHive or MISP
- Enable CloudWatch for AWS log insights
- Daily snapshot backups of EC2s

---

## ✅ Learning Objectives
- Configure and manage a SIEM
- Forward logs from Windows/Linux systems
- Simulate real-world attacks and detect them
- Use Security Onion to analyze security events

---

## 📂 Directory Structure (if uploaded to GitHub)
```
aws-soc-home-lab/
├── README.md
├── sysmon-config.xml
├── winlogbeat.yml
├── attack-scripts/
│   └── nmap_scan.sh
├── diagrams/
│   └── architecture.png
└── notes/
    └── log-analysis-checklist.md
```

---

## 📌 Notes
- Security Onion needs time to ingest and index logs.
- Use Elastic and Kibana dashboards for custom visualizations.
- Limit inbound IPs in Security Groups to secure access.

---

## 📬 Questions or Feedback?
You can submit issues or improvements via GitHub Issues once published.

---

> **Author:** Wayne Whye Jr  
> **Lab Date:** June 2025
