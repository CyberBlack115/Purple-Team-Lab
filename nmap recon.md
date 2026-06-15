# Network Reconnaissance Lab: Nmap & SOC Detection

**Author:** @CyberBlack
**Target Environment:** Ubuntu Server (192.168.10.10)
**Attacker Machine:** Kali Linux (192.168.10.11)

## 🎯 Lab Goal
To execute a comprehensive network scan against a target server to map open ports and services, and outline how a SOC detects this reconnaissance activity.

---

## 🔴 Phase 1: Reconnaissance (Red Team)

**The Strategy:**
Before launching an exploit, an attacker maps the attack surface. I used Nmap to sweep all 65,535 ports on the target server and fingerprint the exact software versions running on any open ports.

**The Command:**
`sudo nmap -sV -p- 192.168.10.10`


**The Output:**
The scan successfully enumerated the server, revealing critical attack vectors. Specifically, it identified an Apache web server running on port 80 and an SSH service on port 22. This tells me exactly what to target next.

*(Screenshot: Nmap terminal output showing open ports and versions)*
<img width="1256" height="672" alt="Screenshot 2026-06-15 144659" src="https://github.com/user-attachments/assets/2efb884e-d992-424c-ae67-49796b894118" />


---

## 🔵 Phase 2: Defense & Detection (Blue Team)

**How a SOC catches a scan:**
Nmap is loud. Scanning 65,000 ports in a few seconds creates a massive spike in network traffic. 

**The Detection Strategy:**
A SOC analyst wouldn't look for this in a standard web or system log. To detect an Nmap scan, the defense infrastructure needs:
* **IDS/IPS (like Snort or Suricata):** Configured to trigger alerts on port scanning signatures (e.g., detecting a high volume of TCP SYN packets sent to different ports on the same IP within a 60-second window).
* **SIEM Threshold Alerts:** In Splunk, an analyst would write a query looking for "1 source IP hitting > 100 unique destination ports in under 1 minute" and flag it as a Reconnaissance Alert.
