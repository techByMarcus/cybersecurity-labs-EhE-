# Lab 1 — pfSense Domain Blocking

## Objective

Use a pfSense firewall alias to block access to a specified destination and verify that the rule changed network behavior.

## What I configured

In the lab environment, I created a pfSense alias named `BlockedWebsites` and associated it with the target domain and IP used by the exercise.

```text
Alias name: BlockedWebsites
Type: Host(s)
Target domain: www.rediff.com
Target IP: 84.53.185.208
```

I then created a LAN firewall rule that referenced the alias.

```text
Action: Block
Interface: LAN
Protocol: TCP/UDP
Source: Any
Destination: BlockedWebsites
```

## How I verified it

After applying the rule, I attempted to reach the target from the lab system. The destination that had been reachable before the rule was no longer accessible after the policy was applied.

I also confirmed the rule was present and active in the pfSense LAN rules view.

## What I took from the exercise

The useful part of this lab was seeing how an alias separates the object being controlled from the firewall rule itself. The policy can continue to reference the same alias while the objects inside it are maintained separately.

It also reinforced that a firewall change should be validated from the client side after it is applied rather than assuming that a saved rule is working as intended.

## Scope

This was a controlled EC-Council training exercise. The domain and addresses shown here were part of that lab environment.
