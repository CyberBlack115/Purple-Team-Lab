# Network Traffic Analysis: Credential Sniffing with Wireshark

**Author:** @CyberBlack
**Target Environment:** Ubuntu Server (192.168.10.10)
**Analyst Machine:** Kali Linux (192.168.10.11)

## 🎯 Lab Goal
Capture and analyze unencrypted network traffic (HTTP) to demonstrate how easily plaintext credentials can be extracted from a network stream, and highlight the necessity of TLS/SSL encryption.

---

## 🔴 Phase 1: The Capture (Execution)

**The Strategy:**
Unencrypted HTTP traffic transmits data exactly as it is typed. By placing a packet sniffer on the network interface, I can capture the transmission of data between a client and a server.

**The Execution:**
1. Initiated a packet capture on the `eth0` interface using Wireshark.
2. Simulated a user logging into the target web application via `http://192.168.10.10`.
3. Stopped the capture to analyze the resulting PCAP (Packet Capture) file.

---

## 🔵 Phase 2: Traffic Analysis (Blue Team)

**Filtering the Noise:**
A raw packet capture contains thousands of irrelevant background packets. To isolate the login event, I applied a strict display filter to show only data submitted to the server:
`http.request.method == "POST"`

**The Extraction:**
The filter successfully isolated the HTTP POST request directed at `/login.php`. By inspecting the packet payload (HTML Form URL Encoded data), I extracted the user's login credentials in absolute plain text.

*(Screenshot: Wireshark packet details revealing the plaintext username and password)*
<img width="1212" height="614" alt="Screenshot 2026-06-20 175927" src="https://github.com/user-attachments/assets/f2f6aedc-241f-4fb8-ab0c-113b56bd5a89" />
<img width="1279" height="626" alt="Screenshot 2026-06-20 175854" src="https://github.com/user-attachments/assets/528de61f-2d55-46be-955d-4993a3f8fda1" />


### SOC / Security Recommendation
This demonstrates a critical infrastructure flaw. To prevent credential harvesting:
* **Enforce HTTPS:** All web traffic must be routed through TLS/SSL encryption (Port 443) so that packet payloads appear as unreadable ciphertext to anyone sniffing the network.
* **HSTS Configuration:** Implement HTTP Strict Transport Security (HSTS) on the web server to force clients to connect via HTTPS, preventing downgrade attacks.
