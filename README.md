# Command Injection Lab: Attack & Defense

**Author:** @CyberBlack
**Target Environment:** Local Ubuntu Server running DVWA in Docker
**Attacker Machine:** Kali Linux 

## 🎯 Lab Goal
To execute a Command Injection attack against a vulnerable web application, and then put on the SOC Analyst hat to see how this attack looks (and hides) in standard server logs.

---

## 🔴 Phase 1: The Attack (Red Team)

**What is the vulnerability?**
The DVWA application has a tool that lets you ping an IP address. But it doesn't check the user input properly. If you add a semicolon (`;`), the Linux server thinks it's a command separator and will run whatever you type next.

**How I exploited it:**
1. I tested the normal function by pinging `127.0.0.1`.
2. I injected my payload: `127.0.0.1; cat /etc/passwd`
3. The server executed the ping, and then immediately executed `cat /etc/passwd`, dumping the server's core user account file directly onto the web page. This gave me full visibility into the system's users (like `root` and `www-data`).

*(Screenshot: Typing the payload)*
<img width="1218" height="425" alt="Screenshot 2026-06-14 193849" src="https://github.com/user-attachments/assets/60f26aca-9fe4-4cd9-ad61-e16fccb48dce" />


*(Screenshot: The /etc/passwd file dumped on screen)*
<img width="1226" height="272" alt="Screenshot 2026-06-14 193944" src="https://github.com/user-attachments/assets/7468873c-c1d8-411e-9ada-dfb23f9f2375" />

---

## 🔵 Phase 2: The Defense & SOC Analysis (Blue Team)

**Hunting the attack in the logs:**
After the breach, I pulled the Apache web server logs straight from the Docker container to see what footprint I left behind.

**The Blind Spot:**
The logs only showed a standard HTTP `POST` request to `/vulnerabilities/exec/` with a `200 OK` success code. Because my payload was sent inside the *body* of the POST request, the standard web logs didn't record it. To a basic sysadmin, this attack looks like completely normal network traffic.

*(Screenshot: Standard Apache logs missing the payload)*
<img width="1268" height="651" alt="Screenshot 2026-06-14 212951" src="https://github.com/user-attachments/assets/5441b36b-daa2-4556-9223-8180a7fef0dc" />


**How a SOC actually detects this:**
Since basic web logs are blind to this, a SOC analyst would need better visibility. To catch this in the real world, I would need:
* **A WAF (Web Application Firewall) or IDS (like Snort/Zeek):** Configured to inspect the *body* of POST requests for Linux shell characters like `;`, `|`, or `&&`.
* **A SIEM (like Splunk):** To ingest those WAF/IDS alerts and trigger a high-severity alarm for the SOC team when command injection signatures are detected.
