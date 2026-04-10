# Cybersecurity Labs Portfolio

Hands-on network security labs covering firewall configuration, intrusion detection, and SIEM integration. Labs completed as part of the NDE Module 05 — Network Security Controls (Technical Controls) course.

---

## Table of Contents

- [Tools & Technologies](#tools--technologies)
- [Exercise 3: Blocking Unwanted Websites using pfSense](#exercise-3-blocking-unwanted-websites-using-pfsense)
- [Exercise 4: Blocking Insecure Ports using pfSense](#exercise-4-blocking-insecure-ports-using-pfsense)
- [Exercise 6: Implementing Network-Based IDS using Suricata + Splunk](#exercise-6-implementing-network-based-ids-using-suricata--splunk)

---

## Tools & Technologies

| Tool | Version | Purpose |
|------|---------|---------|
| pfSense | 2.4.5-RELEASE-p1 | Firewall / Router |
| Suricata IDS | 4.1.4 | Network Intrusion Detection |
| Splunk Enterprise | 8.0.1 | SIEM / Log Analysis |
| Splunk Universal Forwarder | 7.3.2 | Log Forwarding |
| Npcap | 0.99-r7 | Network Packet Capture |
| Hydra | Latest | Brute-Force Attack Simulation |

---

## Exercise 3: Blocking Unwanted Websites using pfSense

**Module:** NDE Module 05 — Network Security Controls  
**Platform:** pfSense 2.4.5-RELEASE-p1 (Microsoft Azure)

### Objective
Use the pfSense firewall alias feature to block access to unwanted or malicious websites. Using aliases allows multiple hosts to be managed under a single firewall rule, reducing complexity.

### Overview
pfSense aliases act as placeholders for real hosts, networks, or ports. Instead of creating one rule per host, a single alias can contain multiple IPs or domains, and one firewall rule can block them all.

### Key Steps
1. Logged into pfSense web interface at `https://10.10.1.1`
2. Navigated to **Firewall → Aliases** and created a new alias:
   - **Name:** `BlockedWebsites`
   - **Type:** Host(s)
   - **Description:** Restrict the access of unwanted websites
3. Pinged `rediff.com` from command prompt to retrieve its IP address
4. Added `www.rediff.com` and its resolved IP (`84.53.185.208`) to the alias
5. Navigated to **Firewall → Rules → LAN** and created a new rule:
   - **Action:** Block
   - **Protocol:** TCP/UDP
   - **Source:** Any
   - **Destination:** `BlockedWebsites` alias
   - **Description:** Restrict access to unwanted Websites
6. Applied changes and verified that `www.rediff.com` was no longer reachable from the Web Server

### Questions & Answers

**Q 5.3.1:** Use the pfSense Firewall alias to block access to website www.rediff.com. Enter the BIOS version of the pfSense machine.  
**A:** Retrieved via shell using `dmidecode -s bios-version` on the pfSense console (pfSense 2.4.5-RELEASE-p1 running on Microsoft Azure — Netgate Device ID: 34b7106cf81823c5e558)

---

## Exercise 4: Blocking Insecure Ports using pfSense

**Module:** NDE Module 05 — Network Security Controls  
**Platform:** pfSense 2.4.5-RELEASE-p1

### Objective
Block insecure ports using pfSense firewall rules to prevent users from accessing HTTP-only websites, enforcing HTTPS-only traffic across the network.

### Overview
Firewall rules can be configured for inbound or outbound traffic. In this exercise, an outbound rule was created to block port 80 (HTTP), forcing users to only access HTTPS-enabled sites.

### Key Steps
1. Logged into pfSense web interface at `https://10.10.1.1`
2. Verified `http://certifiedhacker.com` was accessible before the rule
3. Navigated to **Firewall → Rules → LAN** and created a new rule:
   - **Action:** Reject
   - **Interface:** LAN
   - **Protocol:** TCP/UDP
   - **Source:** Any
   - **Destination:** Any
   - **Destination Port Range:** HTTP (80)
   - **Description:** Rule for Rejecting any website using http (80) port
4. Applied changes and verified `http://certifiedhacker.com` returned "This site can't be reached"
5. Configured a time-based schedule named `WorkingHours` under **Firewall → Schedules**
6. Created a second rule applying the HTTP block only during working hours using the `WorkingHours` schedule
7. Deleted both rules and rebooted pfSense after completing the exercise

### Questions & Answers

**Q 5.4.1:** Use pfSense Firewall rules to block access to HTTP-enabled websites by blocking port HTTP 80. Enter the Destination Port Range option selected under the Destination section of the Edit Firewall Rule window.  
**A:** `HTTP (80)`

---

## Exercise 6: Implementing Network-Based IDS using Suricata + Splunk

**Module:** NDE Module 05 — Network Security Controls  
**Tools:** Splunk Enterprise 8.0.1, Suricata 4.1.4, Npcap 0.99-r7, Splunk Universal Forwarder 7.3.2, Hydra

### Objective
Configure Suricata IDS on a web server to detect network intrusions, forward generated alerts to Splunk Enterprise SIEM using the Splunk Universal Forwarder, and simulate a brute-force attack using Hydra to trigger and verify detection.

### Overview
Suricata is an open-source network intrusion detection and prevention system. It inspects network traffic using rules and signatures and outputs logs in formats like JSON and plain text. In this lab, Suricata alerts were forwarded to Splunk for centralized monitoring and analysis.

### Key Steps
1. Installed **Splunk Enterprise 8.0.1** on Admin Machine-1 (10.10.1.2)
   - Set credentials: admin / admin@123
   - Updated `limits.conf` → set `max_searches_per_cpu=2` at line 145
2. Installed **Npcap 0.99-r7** on Web Server in WinPcap API-compatible mode
3. Installed **Suricata 4.1.4** on Web Server
   - Created `threshold.config` file at `C:\Program Files\Suricata\`
   - Created custom FTP brute-force detection rule in `local.rules`:
     ```
     alert tcp any 21 -> any any (msg:"ET SCAN Potential FTP Brute-Force attempt"; flow:from_server,established; dsize:<100; content:"530 "; depth:4; pcre:"/530\s+(Login|User|Failed|Not)/smi"; classtype:unsuccessful-user; threshold: type threshold, track by_dst, count 5, seconds 300;)
     ```
   - Edited `suricata.yaml` to comment out default rules (lines 1866–1910) and added `- local.rules`
4. Installed **Splunk Universal Forwarder 7.3.2** on Web Server
   - Receiving indexer set to `10.10.1.2:9997`
   - Configured `inputs.conf` to monitor `C:\Program Files\Suricata\log`
   - Configured `outputs.conf`, `props.conf`, and `transforms.conf` for IIS log parsing
   - Restarted SplunkForwarder service
5. Launched Suricata on Web Server:
   ```
   suricata.exe -c suricata.yaml -i 10.10.1.16
   ```
6. On Attacker Machine (10.10.1.50), ran Hydra FTP brute-force attack:
   ```
   hydra -L 'wrd.txt' -P 'pwd.txt' ftp://10.10.1.16
   ```
7. On Admin Machine-1, configured Splunk to receive on port 9997:
   - **Settings → Forwarding and Receiving → Configure Receiving → Add port 9997**
   - Enabled and made visible the SplunkForwarder app
   - Restarted Splunk
8. Verified `fast.log` appeared under **Data Summary → Sources** in Splunk
9. Confirmed brute-force events from 10.10.1.50 → 10.10.1.16 were logged and visible in Splunk search

### Questions & Answers

**Q 5.6.1:** Install and configure Splunk Enterprise SIEM, Npcap, Suricata IDS, and Splunk Forwarder to forward Suricata logs to Splunk. Perform a brute-force attack using Hydra. Enter the name of the default alert log file in which Suricata stores logs.  
**A:** `fast.log`

---

*Labs completed as part of NDE Module 05 — Network Security Controls (Technical Controls)*
