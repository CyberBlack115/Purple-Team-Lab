# Command Injection Lab: Attack & Defense 

**Author:** @CyberBlack
**Target Environment:** Local Ubuntu Server running DVWA in Docker
**Attacker Machine:** Kali Linux 

## 🎯 Lab Goal
To exploit a command injection flaw in a deliberately vulnerable web app (DVWA), see what an attacker can read from the server, then explain how a SOC would detect it and how developers fix it.
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
**How a SOC actually detects this:**
Since basic web logs are blind to this, a SOC analyst would need better visibility. To catch this in the real world, I would need:
* **A WAF (Web Application Firewall) or IDS (like Snort/Zeek):** Configured to inspect the *body* of POST requests for Linux shell characters like `;`, `|`, or `&&`.
* **A SIEM (like Splunk):** To ingest those WAF/IDS alerts and trigger a high-severity alarm for the SOC team when command injection signatures are detected.

---

## 🔧 How to fix it (Developer side)
- **Don't pass user input to a shell at all.** Use a built-in function or library that does the job without a shell.
- **Validate with an allow-list.** The ping box should accept only a valid IP address. Anything else gets rejected.
- **Escape what's left.** If a shell call can't be avoided, escape the input (in PHP, functions like `escapeshellarg`).
- **Least privilege.** The web server runs as `www-data`, a low-privilege user, so even a successful injection can't touch everything. That limits the damage.

## 🎤 Be ready to answer
**Q: Why did the semicolon work?**
In a Linux shell, `;` ends one command and starts another. The app glued my input into a command, so the shell ran my second command right after the ping.

**Q: What other characters would do the same thing?**
`&&`, `||`, `|`, backticks, and `$( )`.

**Q: What's the impact of this flaw?**
It's remote command execution: the attacker runs commands on the server. Here I could read `/etc/passwd`. A real attacker would go further, which is why it's treated as serious.

**Q: How do you fix it?**
Don't send user input to a shell, and validate the input against an allow-list (only a valid IP). Run the app with least privilege.

**Q: What was the DVWA security level?**
*(Fill this in. If you don't remember, run the lab again and note it down.)*

## 📸 More screenshots (optional)
> 📸 **Screenshot goes here** — the DVWA security level setting (`images/dvwa-security-level.png`)
>
> <!-- When ready, delete this box and use: ![DVWA security level](images/dvwa-security-level.png) -->
