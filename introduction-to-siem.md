# TryHackMe: Introduction to SIEM

## Overview
A theory-focused room introducing SIEM — what it is, why a SOC
relies on it, and how it turns scattered logs into detections.
This was my grounding in the concepts before working hands-on
with a tool like Splunk.

**Platform:** TryHackMe
**Focus:** SIEM fundamentals, log sources, correlation, detection rules

## What I learned

### Why SIEM exists
Every device on a network — endpoints, servers, firewalls — is
constantly producing logs. On their own they're scattered and hard
to act on. A SIEM pulls all of that into one place so an analyst
isn't logging into each machine individually to piece events together.

### Log sources
- **Host-centric** — events on a single machine: logins, file access,
  process execution, registry changes, PowerShell activity.
- **Network-centric** — events from traffic between hosts or out to
  the internet: SSH/VPN connections, web traffic, FTP transfers,
  file shares.

### What a SIEM actually does
- **Centralised collection** — gathers logs from every source via
  agents or APIs into one platform.
- **Parsing & normalisation** — raw logs arrive in different formats
  (a Windows log isn't shaped like a Linux log). Parsing breaks a log
  into fields; normalisation converts everything into one consistent
  format so it's searchable and comparable.
- **Correlation** — links events across sources to spot patterns.
  Individually harmless actions (an unusual VPN login → accessing a
  share → running a script → an outbound connection) can together
  signal something like data exfiltration.
- **Real-time alerting** — detection rules fire alerts when their
  conditions are met, so analysts get notified instead of hunting blind.
- **Dashboards** — summarise the noise into something readable: failed
  logins, alert highlights, events ingested, rules triggered.

### How detection rules work
Rules are logic conditions watching field values. Two examples that
made it click:
- Clearing Windows event logs generates Event ID 104 — a rule firing
  on that flags an attacker covering their tracks.
- Process execution is logged under Event ID 4688 — matching that
  event with a process name like `whoami` flags recon activity.

This is why normalisation matters: rules depend on consistent
field-value pairs to trigger reliably.

### Investigating an alert
Once an alert fires, the analyst reviews the events behind it against
the rule conditions and decides:
- **False positive** → tune the rule so it stops firing on noise.
- **True positive** → investigate further: confirm with the asset
  owner, isolate the host, block the source IP.

## Key takeaways
- A SIEM's value isn't storage — it's correlation and detection.
- Good detections depend on clean, normalised logs.
- The analyst's core loop: alert → investigate → true/false positive → act or tune.

## Next steps
- Apply these concepts hands-on in Splunk: ingest logs, write searches,
  build a detection.