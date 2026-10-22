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

---

## 🎤 Be ready to answer
**Q: Why could you read the password?**
HTTP doesn't encrypt anything. The login form sent the username and password exactly as typed, so anyone who can see the traffic can read them.

**Q: How was I able to see that traffic in my capture?**
*(Answer in your own words. Be clear about how your setup worked, for example: I captured on my own Kali machine's interface while I logged in from it. On a real switched network you would only see other people's traffic with something extra, such as port mirroring, an ARP spoofing attack, or being on shared Wi-Fi.)*

**Q: Capture filter vs display filter?**
A capture filter decides what Wireshark records in the first place. A display filter only hides things from view after recording. I used a display filter, `http.request.method == "POST"`.

**Q: Would HTTPS fully fix this?**
It fixes the password exposure: the attacker sees encrypted data, not the login. They could still see which server you're talking to and how much traffic there is.

**Q: What does HSTS do?**
It tells the browser to always use HTTPS for that site, so an attacker can't trick it into dropping back to plain HTTP.

## 📸 More screenshots (optional)
> 📸 **Screenshot goes here** — the Wireshark capture interface selected, before starting (`images/wireshark-capture-start.png`)
>
> <!-- When ready, delete this box and use: ![Wireshark capture start](images/wireshark-capture-start.png) -->
