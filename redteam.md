# Purple Team Lab: Exploiting & Detecting Command Injection

**Author:** @CyberBlack
**Target Vulnerability:** OS Command Injection
**Environment:** Local Virtualized Network

## 🎯 Objective
Demonstrate a complete Purple Team lifecycle: exploiting a Command Injection vulnerability to extract sensitive system files, analyzing server logs to inform SOC/SIEM detection rules, and reviewing secure code practices for mitigation.

## 🏗️ Lab Infrastructure
* **Attacker Node:** Kali Linux (IP: 192.168.10.11)
* **Target Node:** Ubuntu Server running Docker (IP: 192.168.10.10)
* **Target Application:** Damn Vulnerable Web Application (DVWA) via Docker container

---

## 🔴 Phase 1: Red Team (The Attack)

### The Vulnerability
The target application features a diagnostic tool that accepts an IP address to execute a system `ping`. Due to a lack of server-side input sanitization, an attacker can append arbitrary Linux OS commands using the `;` operator.

### Execution Strategy
1. **Baseline:** Verified the input field successfully executes a standard `ping -c 4 <IP>`.
2. **Weaponization:** Chained a secondary command to the IP input to read the server's primary user configuration file.
3. **Payload:** `127.0.0.1; cat /etc/passwd`

### Proof of Concept (PoC)
The server executed the initial ping, followed immediately by the `cat` command. This resulted in the contents of `/etc/passwd` being dumped directly to the web interface, exposing critical service and user accounts (e.g., `root`, `www-data`).

*(Screenshot: Executing the payload)*
<img width="1226" height="272" alt="Screenshot 2026-06-14 193944" src="https://github.com/user-attachments/assets/f373ef20-e49e-472d-9108-76b18d5b70fd" />

*(Screenshot: Server configuration dumped to the UI)*
<img width="1218" height="425" alt="Screenshot 2026-06-14 193849" src="https://github.com/user-attachments/assets/59693f95-172c-4f61-8723-0ffdfcb1b40b" />
