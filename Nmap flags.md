# 🛡️ Practical Nmap Recon & Network Enumeration Guide

Here is my hands-on, field-tested reference guide to Nmap. I built this while running real-world security labs, enumerating targets, and auditing infrastructure. It covers modern scanning techniques, fingerprinting, and scripting.

***

## 🎯 Host Discovery
Host discovery stops you from wasting time scanning dead IP addresses. These commands tell Nmap how to find active targets before it starts probing their ports.

### The Ping Sweep (`-sn`)
**What it does:** Disables port scanning completely. It just sends out a few quick requests to see if a target is actually awake.  
**When to use it:** When you need to quickly map out an entire subnet to see who is online.

```bash
nmap -sn 192.168.10.0/24
```

### Treat as Online (`-Pn`)
**What it does:** Skips the initial ping check completely and assumes the host is up and running.  
**When to use it:** Crucial when your target is hiding behind a strict firewall that drops standard ICMP ping packets.

```bash
nmap -Pn 192.168.10.10
```

***

## 🔌 Port Scanning Techniques
Different scan types interact with the target's network stack in different ways. Choosing the right one depends on your permission levels and how quiet you need to be.

### Stealth Scan (`-sS`)
**What it does:** The absolute gold standard. It sends a SYN packet, waits for a response, and immediately tears down the connection before a full handshake finishes.  
**When to use it:** This is your go-to. Because it never opens a full TCP session, it is fast and much less likely to trigger application logs. Requires `sudo`.

```bash
sudo nmap -sS 192.168.10.10
```

### TCP Connect Scan (`-sT`)
**What it does:** Completes the full 3-way TCP handshake with the target.  
**When to use it:** Use this when you do not have root privileges to craft raw packets (which the Stealth Scan requires). It is louder and easily logged, but it works everywhere.

```bash
nmap -sT 192.168.10.10
```

### UDP Scan (`-sU`)
**What it does:** Probes target ports with raw UDP packets.  
**When to use it:** When you are hunting for critical services like DNS, DHCP, or SNMP. UDP scanning is notoriously slow by nature, so it is best to pair this flag with specific ports rather than scanning all of them.

```bash
sudo nmap -sU -p 53,67,161 192.168.10.10
```

***

## 🔍 Service & OS Fingerprinting
Knowing a port is open is just step one. You need to extract the actual software identities and system architectures sitting behind those ports.

### Service Version Detection (`-sV`)
**What it does:** Interrogates open ports to grab banners and identify exact software names and version numbers.  
**When to use it:** Always. Knowing a server runs "Apache 2.4.41" instead of just "HTTP" is exactly how you find matching vulnerabilities.

```bash
nmap -sV 192.168.10.10
```

### OS Detection (`-O`)
**What it does:** Analyzes how the target responds to various packets and compares it against a massive database to guess the operating system.  
**When to use it:** When you need to know if you are attacking a Linux server, a Windows domain controller, or a random IoT device.

```bash
sudo nmap -O 192.168.10.10
```

### Aggressive Scan (`-A`)
**What it does:** The ultimate shortcut. It combines OS detection, version detection, default scripting, and traceroute all into one massive scan.  
**When to use it:** Great for local labs and CTF environments where you do not care about being loud and just want all the info immediately.

```bash
nmap -A 192.168.10.10
```

***

## ⚡ Speed & Output
If you don't document your scans, they never happened. You also need to control Nmap's speed so you don't wait hours for a result.

### Timing Template (`-T4`)
**What it does:** Speeds up the scan execution. Nmap scales from T0 (ultra-slow) to T5 (insane).  
**When to use it:** T4 is the sweet spot. It is optimized for modern, reliable local networks without dropping packets.

```bash
nmap -T4 192.168.10.10
```

### All-Port Scan (`-p-`)
**What it does:** By default, Nmap only scans the top 1,000 common ports. This flag forces it to scan every single valid port from 1 to 65,535.  
**When to use it:** When you want to ensure the target isn't hiding a backdoor or admin panel on a high, non-standard port.

```bash
nmap -p- 192.168.10.10
```

### Output All Formats (`-oA`)
**What it does:** Saves your scan results in three formats simultaneously: a normal text file, an XML file, and a grepable file.  
**When to use it:** Always. You can read the text file, feed the XML into other security tools, and use grep to quickly search the third file.

```bash
nmap -A -p- -T4 -oA target_initial_scan 192.168.10.10
```

***

## 💣 Real-World Go-To Scans
Here are the exact one-liners I run during practical engagements and lab evaluations.

### 1. The Workhorse Recon Scan
Runs a stealthy full-port scan, extracts versions, runs default scripts, optimizes speed, and saves everything. This is usually the very first command run against a new target.

```bash
sudo nmap -sS -sV -sC -p- -T4 -oA initial_recon 192.168.10.10
```

### 2. Fast Network Sweep
Quickly maps out an entire IP subnet to find live hosts without wasting time doing a full port scan on dead IPs.

```bash
nmap -sn -T4 -oA subnet_discovery 192.168.10.0/24
```

### 3. High-Value UDP Target Scan
Focuses explicitly on finding critical UDP infrastructure services while skipping standard host discovery.

```bash
sudo nmap -sU -Pn -p 53,67,123,161,500 -sV -T4 -oA udp_services 192.168.10.10
```
