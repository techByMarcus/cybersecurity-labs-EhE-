# Lab 3 — Suricata to Splunk Brute-Force Detection

## Objective

Generate FTP brute-force traffic in an isolated lab, detect it with Suricata, forward the alert log to Splunk, and verify that the event reached the SIEM.

This was the most SOC-oriented exercise in the set because it connected attack simulation, IDS detection, log forwarding, and analyst verification.

## Lab path

```text
Attacker: 10.10.1.50
        |
        | Hydra FTP brute-force traffic
        v
Target: 10.10.1.16:21
        |
        | Suricata inspects traffic
        | fast.log receives alert
        v
Splunk Universal Forwarder
        |
        | sends events to 10.10.1.2:9997
        v
Splunk Enterprise
        |
        v
Alert verified in SIEM
```

## Detection rule

The exercise used a local Suricata rule to identify repeated FTP authentication attempts from a source.

```text
alert tcp any any -> 10.10.1.16 21 (
    msg:"FTP Brute Force Detected";
    flow:to_server,established;
    content:"USER";
    nocase;
    threshold:type threshold, track by_src, count 5, seconds 60;
    sid:1000001;
    rev:1;
)
```

A copy is kept in [`artifacts/suricata-local.rules`](../artifacts/suricata-local.rules).

## Pipeline configuration

The lab configuration connected the following components:

| Component | Configuration used |
| --- | --- |
| Suricata | Local rule enabled in the lab configuration |
| Alert log | `C:\Program Files\Suricata\log\fast.log` |
| Universal Forwarder | Monitored the Suricata log location |
| Splunk output | Forwarded to `10.10.1.2:9997` |
| Splunk receiver | Listening on port 9997 |

## Attack simulation

From the attacker system, the exercise generated FTP authentication attempts with Hydra:

```bash
hydra -L wrd.txt -P pwd.txt ftp://10.10.1.16
```

This command was run only in the isolated training environment.

## How I verified the detection

I followed the event through the pipeline:

1. Hydra generated FTP authentication traffic against the target.
2. Suricata evaluated the traffic against the local rule.
3. The alert was written to `fast.log`.
4. Splunk Universal Forwarder sent the log data to the Splunk receiver.
5. I confirmed the Suricata event was visible in Splunk.

The important part of the exercise was not just generating the attack traffic. It was verifying that each stage of the detection path was working.

## Analyst relevance

This lab gave me hands-on practice with several tasks that appear in SOC environments:

- understanding what behavior a detection rule is intended to identify
- validating that an IDS actually generated an event
- tracing whether the event reached the SIEM
- distinguishing a detection problem from a log-forwarding problem
- confirming the alert before treating the pipeline as operational

The activity maps to **MITRE ATT&CK T1110 — Brute Force**.

## Scope

This was a controlled EC-Council training lab. It does not represent production IDS or Splunk administration, and the versions and addresses shown here are specific to the course environment.
