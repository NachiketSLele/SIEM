# Splunk: The Basics

## Overview
Splunk is one of the most widely used SIEM platforms — it collects logs from
across a network, normalises them, and makes them searchable in real time.
This room was my first hands-on look at how Splunk is built and how data flows
through it, right after grounding myself in the SIEM concepts.

**Focus:** Splunk architecture, log ingestion, navigation, SPL basics

## What I learned

### What Splunk does
Splunk pulls in logs from servers, endpoints, and firewalls, normalises them
into a consistent format, and lets you search them in real time. For a SOC
analyst, that means investigating activity across hundreds of machines from
one place instead of logging into each one.

### How Splunk is built
Splunk has three components that pass data down a pipeline:
- **Forwarder** — a lightweight agent on the monitored machine. It collects
  logs (Windows Event Logs, PowerShell, Sysmon, Linux logs, database and web
  activity) and ships them on without slowing the host down.
- **Indexer** — parses and normalises the raw logs into consistent field-value
  pairs, then stores them as searchable events. This turns messy, mismatched
  logs into something usable.
- **Search Head** — where you query the indexed data using SPL, and turn the
  results into tables and charts.

The flow: **logs → Forwarder (collect) → Indexer (process) → Search Head (analyse).**

### Searching with SPL
SPL (Search Processing Language) is how you query the data. You search an index,
then pipe the results through commands to filter and shape them — much like
chaining commands in a Linux terminal. The simplest search is just `index=main`,
and from there you add commands to count, sort, filter by field, and build tables.

### Why normalisation matters
The key idea: Splunk's value isn't storage, it's the indexer turning inconsistent
logs into uniform field-value pairs. That consistency is what makes fast searching
— and reliable detection rules — possible.

## Key takeaways
- Splunk centralises logs from across a network into one searchable platform.
- Its three components map onto a simple pipeline: collect → process → analyse.
- Clean, normalised data is the foundation everything else is built on.
