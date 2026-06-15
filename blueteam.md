## 🔵 Phase 2: />
 Blue Team (The Defense & Detection)

### Log Analysis
Analyzed the target's Apache web server access logs via the Docker container to identify the attack footprint. 

### The Detection Blind Spot
The logs revealed successful `POST` requests to `/vulnerabilities/exec/` returning `200 OK` status codes. Because the command injection payload (`ip=127.0.0.1%3B+cat+%2Fetc%2Fpasswd`) is transmitted within the HTTP POST body, it remains invisible to default web server logging. To an untrained observer, this remote code execution looks identical to normal, benign user traffic.

*(Screenshot: Standard Apache logs failing to capture the POST body payload)*
<img width="1268" height="651" alt="Screenshot 2026-06-14 212951" src="https://github.com/user-attachments/assets/ee30d205-50b0-4b58-bcc0-60f468a3056f" />

### SOC / SIEM Recommendations
Relying solely on basic access logs is insufficient for detecting this class of attack. A robust defense architecture requires:
* **Web Application Firewall (WAF):** To actively inspect POST request bodies and block payloads containing OS shell meta-characters (`;`, `|`, `&&`).
* **Network Intrusion Detection System (IDS):** Implementing rules (e.g., via Snort/Zeek) to alert on malicious signatures traversing the network.
* **Endpoint Detection and Response (EDR):** Monitoring the host OS to flag unexpected processes spawning from the `www-data` service account (e.g., the web server suddenly spawning `cat` or `whoami` commands).
