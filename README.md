# EC-Council Ethical Hacking Essentials — Security Labs

Selected hands-on security labs completed as part of my EC-Council Ethical Hacking Essentials (EHE) training and certification work.

I created this repository to document the systems I worked with, the controls I configured, the attack activity I generated in the lab, and how I verified the defensive result. The goal is to show the work itself rather than just list tools on a resume.

## Lab environment

The exercises used a controlled training network with the following components:

| Component | Lab system |
| --- | --- |
| Firewall / router | pfSense 2.4.5-RELEASE-p1 |
| IDS | Suricata 4.1.4 |
| SIEM | Splunk Enterprise 8.0.1 |
| Log forwarder | Splunk Universal Forwarder 7.3.2 |
| Packet capture support | Npcap 0.99-r7 |
| Attack simulation | Hydra |
| Network | 10.10.1.0/24 |

These versions reflect the training environment used in the exercises, not recommendations for current production deployments.

## What I worked on

| Lab | Work performed | Tools |
| --- | --- | --- |
| [1. pfSense domain blocking](./labs/01-pfsense-domain-blocking.md) | Created a firewall alias, applied a LAN blocking rule, and verified the destination became unreachable | pfSense |
| [2. pfSense port and schedule policy](./labs/02-pfsense-port-policy.md) | Created a port-based HTTP restriction and applied a time-based schedule | pfSense |
| [3. Suricata to Splunk detection pipeline](./labs/03-suricata-splunk-detection.md) | Configured a Suricata rule, forwarded the alert log into Splunk, generated FTP brute-force traffic with Hydra, and verified the alert | Suricata, Splunk, Hydra |

The third lab is the most directly relevant to SOC work because it covers the full detection path from simulated attack activity to IDS alert generation and SIEM verification.

## Technical artifacts

The repository includes the configuration details retained from the exercises:

- [Suricata detection rule](./artifacts/suricata-local.rules)
- [Lab configuration notes](./artifacts/configuration-notes.md)

These files document the values used in the training environment. They are included as portfolio evidence, not as production-ready configuration templates.

## What this work demonstrates

The labs gave me hands-on exposure to:

- pfSense firewall rules and aliases
- port-based and scheduled access controls
- Suricata IDS rule configuration
- Splunk Universal Forwarder and Splunk Enterprise ingestion
- Hydra-based brute-force simulation in an isolated lab
- alert verification and basic detection workflow
- the relationship between attack activity, network controls, logs, and SIEM visibility

## Evidence and scope

This is training-lab work, not production security administration.

The repository documents configuration details, commands, and observations from exercises I completed. Where original screenshots or raw log exports were not retained, I do not recreate them and present them as original evidence. The documentation instead identifies what was configured, how the result was verified, and what the exercise demonstrated.

## Certification context

**EC-Council Ethical Hacking Essentials (EHE)**

This repository represents selected hands-on work completed during that training and certification path. It is one part of a broader cybersecurity portfolio that also includes SOC investigations, authentication-log analysis, network-attack labs, and a Python security-audit project.

## Related work

- [SOC Analyst Portfolio](https://github.com/techByMarcus/soc-analyst-portfolio)
- [Authentication Log Investigation](https://github.com/techByMarcus/real-world-log-investigation)
- [Security Audit Tool](https://github.com/techByMarcus/security-audit-tool)
- [Portfolio](https://techbymarcus.github.io/aboutMarcus/)
