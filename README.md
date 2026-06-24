# Network Security Controls Lab
### EC-Council Network Defense Essentials (NDE) · Module 05

![Security](https://img.shields.io/badge/security-network%20defense-red)
![Tools](https://img.shields.io/badge/tools-pfSense%20%7C%20Suricata%20%7C%20Splunk-blue)
![MITRE](https://img.shields.io/badge/MITRE%20ATT%26CK-T1110%20%7C%20T1190-orange)
![Status](https://img.shields.io/badge/status-complete-brightgreen)

---

## Overview

Three hands-on network security labs demonstrating firewall policy enforcement,
host-based intrusion detection, and SIEM-integrated alert verification in a
controlled lab environment. Labs were completed as part of the EC-Council
Network Defense Essentials (NDE) certification — Module 05: Network Security
Controls (Technical Controls).

Each lab follows a professional security workflow: **define the control →
configure the enforcement → simulate the threat → verify the detection.**

---

## Environment

| Component | Details |
| --- | --- |
| Firewall / Router | pfSense 2.4.5-RELEASE-p1 (Microsoft Azure) |
| IDS | Suricata 4.1.4 |
| SIEM | Splunk Enterprise 8.0.1 |
| Log Forwarder | Splunk Universal Forwarder 7.3.2 |
| Packet Capture | Npcap 0.99-r7 |
| Attack Simulation | Hydra (brute-force) |
| Network Range | 10.10.1.0/24 |

---

## Lab 1 — URL-Based Firewall Blocking (pfSense Alias Rules)

**Objective:** Enforce content filtering at the network perimeter by blocking
access to specific domains using pfSense firewall aliases — a scalable
approach that consolidates multiple hosts under a single policy rule.

**NIST CSF Reference:** PR.AC-3 (Remote access managed) · PR.IP-1 (Baseline
configurations maintained)

### What I Did

Configured a pfSense alias named `BlockedWebsites` containing the target
domain (`www.rediff.com`) and its resolved IP (`84.53.185.208`). Created a
LAN firewall rule targeting the alias with action **Block**, protocol TCP/UDP,
source Any. Applied and verified — the domain became unreachable from the web
server immediately after rule enforcement.

```
Alias Name  : BlockedWebsites
Type        : Host(s)
Target      : www.rediff.com / 84.53.185.208
Rule Action : Block  |  Protocol: TCP/UDP  |  Source: Any
```

### Why Aliases Matter

Aliases decouple policy logic from individual IPs. When a blocked domain
rotates its IP, you update one alias — not dozens of rules. This is the
same pattern enterprise firewalls use for threat-intel feed integration.

### Outcome

Domain blocked at the perimeter. Firewall rule confirmed active via pfSense
Rules → LAN view. Web server confirmed unreachable to target domain
post-enforcement.

---

## Lab 2 — Port-Based Blocking with Time-Based Policy (pfSense Rules)

**Objective:** Eliminate unencrypted HTTP traffic on the network by blocking
port 80 outbound — and demonstrate time-based policy enforcement using
pfSense scheduling.

**NIST CSF Reference:** PR.PT-3 (Principle of least functionality) ·
PR.IP-1 (Baseline configurations)

### What I Did

Created an outbound LAN rule with action **Reject**, destination port HTTP
(80). Verified that `http://certifiedhacker.com` returned "This site can't
be reached" after rule activation. Extended the lab by configuring a
time-based schedule (`WorkingHours`) and binding the block rule to
active hours only — demonstrating conditional policy enforcement.

```
Rule Action  : Reject
Interface    : LAN
Protocol     : TCP/UDP
Source       : Any → Destination: Any
Port         : HTTP (80)
Schedule     : WorkingHours (time-bound enforcement)
```

### Why Port 80 Blocking Matters

HTTP transmits credentials, session tokens, and PII in cleartext. Blocking
port 80 forces HTTPS-only traffic — a baseline hardening control in any
CIS or NIST-aligned network configuration. Time-based rules add conditional
enforcement without permanent policy changes, which matters in environments
with legitimate scheduled maintenance windows.

### Outcome

HTTP traffic blocked network-wide during policy window. HTTPS traffic
unaffected. Rule verified active and then cleanly removed post-lab.

---

## Lab 3 — Intrusion Detection + SIEM Integration + Live Attack Simulation

**Objective:** Deploy Suricata IDS to detect brute-force network attacks,
forward alerts to Splunk SIEM, and confirm end-to-end detection by
simulating a live FTP brute-force attack using Hydra.

**MITRE ATT&CK:** T1110 — Brute Force  
**NIST CSF Reference:** DE.CM-1 (Network monitored for events) ·
DE.AE-2 (Detected events analyzed) · RS.AN-1 (Notifications investigated)

This is the complete SOC detection workflow: **write the rule → configure
the pipeline → simulate the attack → confirm the alert.**

### Architecture

```
Attacker (10.10.1.50)
    │
    │  Hydra FTP brute-force
    ▼
Web Server (10.10.1.16)
    │  Suricata 4.1.4 inspects traffic
    │  Alert written to C:\Program Files\Suricata\log\fast.log
    │
    │  Splunk Universal Forwarder 7.3.2
    │  Forwards to port 9997
    ▼
Admin Machine / Splunk SIEM (10.10.1.2)
    │  Indexes and visualizes Suricata alerts
    ▼
Analyst confirms brute-force detection in Splunk search
```

### Detection Rule (Suricata custom rule — local.rules)

Written to detect repeated FTP authentication failures from a single
source — the signature of a brute-force attack:

```
alert tcp any any -> 10.10.1.16 21 \
  (msg:"FTP Brute Force Detected"; \
   flow:to_server,established; \
   content:"USER"; nocase; \
   threshold:type threshold, track by_src, count 5, seconds 60; \
   sid:1000001; rev:1;)
```

### Pipeline Configuration

| Component | Configuration |
| --- | --- |
| Suricata | `suricata.yaml` — default rules commented out (lines 1866–1910); `local.rules` added |
| Log output | `C:\Program Files\Suricata\log\fast.log` |
| Forwarder input | `inputs.conf` monitors Suricata log directory |
| Forwarder output | `outputs.conf` → `10.10.1.2:9997` |
| Splunk receiver | Port 9997 configured under Forwarding and Receiving |

### Attack Simulation

```bash
# Hydra FTP brute-force from Attacker (10.10.1.50)
hydra -L wrd.txt -P pwd.txt ftp://10.10.1.16
```

### Detection Outcome

Brute-force traffic from `10.10.1.50` against `10.10.1.16:21` triggered the
Suricata custom rule. Alert written to `fast.log`, forwarded via Splunk
Universal Forwarder, and confirmed visible in Splunk Enterprise under
Data Summary → Sources. End-to-end detection pipeline verified.

**What this demonstrates:** The ability to write a custom detection rule,
configure a SIEM ingestion pipeline from scratch, simulate a real attack
technique (T1110), and confirm the detection fired — the core loop of
detection engineering.

---

## Skills Demonstrated

| Skill | Context |
| --- | --- |
| Firewall policy design | pfSense alias rules, port blocking, time-based enforcement |
| Network IDS configuration | Suricata custom rule authoring, suricata.yaml tuning |
| SIEM pipeline setup | Splunk Universal Forwarder → Splunk Enterprise ingestion |
| Attack simulation | Hydra FTP brute-force (MITRE T1110) |
| Detection verification | End-to-end alert confirmation in Splunk |
| MITRE ATT&CK mapping | T1110 (Brute Force), T1190 (Exploit Public-Facing Application) |
| NIST CSF alignment | PR.AC-3, PR.PT-3, DE.CM-1, DE.AE-2, RS.AN-1 |

---

## Certification Context

These labs were completed as part of the **EC-Council Network Defense
Essentials (NDE)** certification — a vendor-neutral, hands-on credential
covering network security fundamentals, firewall configuration, IDS/IPS
deployment, and SIEM operations.

**Certification:** EC-Council NDE  
**Module:** 05 — Network Security Controls (Technical Controls)  
**Portfolio:** [github.com/techByMarcus](https://github.com/techByMarcus)

---

*Marcus Albright · [LinkedIn](https://www.linkedin.com/in/marcus-a-69ab2989) ·
[Portfolio](https://techbymarcus.github.io/aboutMarcus)*
