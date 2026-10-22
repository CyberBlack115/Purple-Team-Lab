# Purple-Team-Lab

Purple team means the red team (attackers) and the blue team (defenders) working together. In every lab here I do both jobs: first I attack something in my own lab, then I write down how a SOC would catch it and how it gets fixed.

Part of my [CyberSecurity-Journal](https://github.com/CyberBlack115/CyberSecurity-Journal).

## Lab setup

| Machine | Role | Address |
|---|---|---|
| Kali Linux | Attacker / analyst | 192.168.10.11 |
| Ubuntu Server | Target (DVWA running in Docker) | 192.168.10.10 |

Everything here is on machines I own. I never scan or attack anything I don't have permission to test.

## Labs

| # | Lab | 🔴 Red side | 🔵 Blue side | MITRE ATT&CK |
|---|---|---|---|---|
| 01 | [Nmap recon](01-nmap-recon.md) | Full-port scan with version detection | IDS signature and SIEM threshold for port scans | T1046 Network Service Discovery |
| 02 | [Nmap cheat sheet](02-nmap-cheatsheet.md) | My reference for flags and go-to scans | | |
| 03 | [Burp brute force](03-burp-bruteforce.md) | Burp Intruder against a login | Failed-login rule, lockout, rate limiting | T1110 Brute Force |
| 04 | [Command injection](04-command-injection.md) | `;` payload in DVWA's ping box | WAF/IDS detection and the code-level fix | T1190 Exploit Public-Facing Application |
| 05 | [Wireshark analysis](05-wireshark-analysis.md) | Capturing a plain HTTP login | Enforce HTTPS and HSTS | T1040 Network Sniffing |

## What's ATT&CK?
MITRE ATT&CK is a public catalog of the techniques attackers use, each with an ID like T1110. SOC teams use it as a common language: saying "we saw T1110" tells everyone exactly what kind of attack happened.

## Tools
Nmap · Burp Suite · Wireshark · DVWA · Docker · Kali Linux

## 📸 Screenshots
Screenshots live in [`images/`](images). Each lab has a box that says *Screenshot goes here* with the file name to use.
