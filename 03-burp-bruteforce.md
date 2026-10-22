# Web Application Attack: Brute Forcing with Burp Suite

**Author:** @CyberBlack
**Target Environment:** Ubuntu Server (192.168.10.10)
**Attacker Machine:** Kali Linux (192.168.10.11)

## 🎯 Lab Goal
Demonstrate how attackers intercept web traffic to execute automated brute-force attacks against login portals, and explain the SIEM detection logic used to catch them.

---

## 🔴 Phase 1: The Attack (Red Team)

**The Strategy:**
To bypass authentication, I used Burp Suite as a web proxy to intercept a failed login request. I then passed this request to Burp Intruder to automate testing a list of potential passwords against the target account.

**The Execution:**
1. Configured Burp Suite to intercept traffic between the Kali browser and the DVWA server.
2. Captured a baseline login attempt for the `admin` user. {This is the default user}
3. Sent the intercepted request to Burp Intruder and set a payload marker on the password parameter.
4. Loaded a custom wordlist and launched the sniper attack.
5. Identified the correct password (`password`) by analyzing the HTTP response lengths; the successful login returned a dynamically different response size than the failed attempts which is shown in the belowed screenshot.

*(Screenshot: Burp Intruder results showing the successful payload)*
<img width="1266" height="617" alt="image" src="https://github.com/user-attachments/assets/68503014-afa1-41c4-b50f-edbfa5286bb3" />


---

## 🔵 Phase 2: Defense & Detection (Blue Team)

**How a SOC catches a Brute Force attack:**
This attack generates a massive, identifiable spike in web server traffic.

**The Detection Strategy:**
A SOC analyst looks for a high volume of authentication failures. In a SIEM (like Splunk), the detection rule would look like this:
* **The Rule:** Alert if `Source IP` generates > 10 failed login attempts (HTTP 401 Unauthorized or specific error page sizes) within 5 minutes.
* **The Escalation:** If those 10 failed attempts are immediately followed by 1 successful login (HTTP 200 OK or 302 Redirect) from the same IP, trigger a CRITICAL alert for "Successful Brute Force / Account Compromise."

**Mitigation:**
To stop this at the architecture level, the application must enforce account lockouts (e.g., locking the account for 15 minutes after 5 failed attempts) and implement rate limiting on the login endpoint.

---

## 🎤 Be ready to answer
**Q: How did you know which password was right?**
Burp showed the length of every response. The correct password produced a response of a different size from all the failures, so it stood out.

**Q: What is a Sniper attack in Intruder?**
It uses one payload list on one marked position, trying one value at a time. Perfect for a single field like a password when the username is known.

**Q: Brute force vs password spraying vs credential stuffing?**
Brute force tries many passwords on one account. Password spraying tries one or two common passwords across many accounts, so it avoids lockouts. Credential stuffing tries username and password pairs leaked from other breaches.

**Q: Why did this attack work?**
The login had no lockout and no rate limiting, so I could try as many guesses as I wanted. A weak password made it quick.

**Q: How do you stop it?**
Account lockout, rate limiting, MFA, strong password rules, and alerts on many failed logins.

## 📸 More screenshots (optional)
> 📸 **Screenshot goes here** — Burp's Intruder payload position set on the password field (`images/burp-payload-position.png`)
>
> <!-- When ready, delete this box and use: ![Burp payload position](images/burp-payload-position.png) -->
