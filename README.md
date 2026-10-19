# Security Controls and Detection | Hands-On Lab Portfolio

**Firewall policies · IDS alert generation · Splunk verification · Controlled training environment**

> **Start here:** [Suricata → Splunk detection workflow](labs/03-suricata-splunk-detection.md). This lab follows simulated authentication attack traffic through detection and SIEM visibility.

## Three lab outcomes

| Exercise | What I configured / evaluated | Evidence to inspect |
|---|---|---|
| **[Suricata to Splunk alert workflow](labs/03-suricata-splunk-detection.md)** | Detection rule, log forwarding, controlled Hydra traffic, and alert verification | Lab procedure and configuration notes |
| **[pfSense domain restriction](labs/01-pfsense-domain-blocking.md)** | Alias and LAN blocking rule; connectivity verification | Lab walkthrough |
| **[pfSense port and time policy](labs/02-pfsense-port-policy.md)** | Port restriction with schedule | Lab walkthrough |

## Detection chain

```text
Authorized lab traffic → Suricata rule match → alert log → Splunk ingestion → analyst review
```

The goal of the third exercise was **verification of the complete visibility path**, not a claim to operating a production SOC.

## Inspect the configuration

[Suricata detection rule](artifacts/suricata-local.rules) · [Lab configuration notes](artifacts/configuration-notes.md)

## Training context

EC-Council Ethical Hacking Essentials exercises in an isolated training network using pfSense 2.4.5, Suricata 4.1.4, Splunk 8.0.1, Splunk Universal Forwarder 7.3.2 and Hydra. Versions describe the lab environment, **not** recommended production versions.

Original logs/screenshots were not retained for every exercise; the documentation does not invent missing evidence.

**Relevant skills:** Network access policy, IDS/SIEM concepts, log routing, detection verification, and documentation.

[Portfolio](https://techbymarcus.github.io/aboutMarcus/) · [SOC cases](https://github.com/techByMarcus/soc-analyst-portfolio)
