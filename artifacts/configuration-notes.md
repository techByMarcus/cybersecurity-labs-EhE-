# Lab Configuration Notes

These notes capture configuration values documented from the EC-Council Ethical Hacking Essentials training exercises represented in this repository.

They are provided so a reviewer can see the actual objects, addresses, paths, and commands involved in the labs. They are not production recommendations.

## pfSense domain-blocking exercise

```text
Alias: BlockedWebsites
Type: Host(s)
Domain: www.rediff.com
Resolved IP used in lab: 84.53.185.208

Firewall action: Block
Interface: LAN
Protocol: TCP/UDP
Source: Any
Destination: BlockedWebsites
```

## pfSense HTTP and schedule exercise

```text
Firewall action: Reject
Interface: LAN
Protocol: TCP/UDP
Source: Any
Destination: Any
Destination port: HTTP / 80
Schedule: WorkingHours
```

## Suricata / Splunk exercise

```text
Attacker: 10.10.1.50
Target: 10.10.1.16
Target service: FTP / 21
Splunk receiver: 10.10.1.2
Splunk receiving port: 9997
Suricata alert log: C:\Program Files\Suricata\log\fast.log
```

Attack simulation command:

```bash
hydra -L wrd.txt -P pwd.txt ftp://10.10.1.16
```

Detection rule:

```text
alert tcp any any -> 10.10.1.16 21 (msg:"FTP Brute Force Detected"; flow:to_server,established; content:"USER"; nocase; threshold:type threshold, track by_src, count 5, seconds 60; sid:1000001; rev:1;)
```

## Evidence note

The original training environment was temporary. This repository preserves the configuration details and observations that were documented from the exercises. It does not fabricate screenshots or raw logs that were not retained.
