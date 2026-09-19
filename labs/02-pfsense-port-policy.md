# Lab 2 — pfSense Port and Schedule Policy

## Objective

Create an outbound firewall restriction for HTTP traffic and then apply a schedule so the rule is active only during a defined period.

## What I configured

I created a LAN rule in pfSense that rejected outbound traffic to destination port 80.

```text
Action: Reject
Interface: LAN
Protocol: TCP/UDP
Source: Any
Destination: Any
Destination port: HTTP (80)
```

I then created a schedule named `WorkingHours` and associated the rule with that schedule.

## How I verified it

The lab required testing access before and after the rule was applied. After the HTTP rule became active, the test site used in the exercise could no longer be reached over port 80.

HTTPS traffic was not the target of the rule.

I then removed the temporary lab configuration after validation.

## What I took from the exercise

This exercise was useful because it went beyond simply creating a block rule. It showed how the same control can be limited by time, which is important when access requirements depend on operating hours or maintenance windows.

It also reinforced the difference between controlling a specific protocol or port and making a broader claim that all web traffic has been blocked.

## Scope

This was a controlled EC-Council training exercise. The rule was created for the lab scenario and should not be treated as a general production firewall standard.
