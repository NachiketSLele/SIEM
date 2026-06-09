# 📊 SIEM & File Integrity Monitoring Home Lab (Wazuh)

A hands-on home lab where I deployed a **Wazuh SIEM** manager, onboarded a **Windows endpoint agent**, and set up **File Integrity Monitoring (FIM)** to detect and alert on file changes in real time — then verified detections through the Wazuh dashboard.

**Tech:** Wazuh (Manager + Indexer + Dashboard) · Ubuntu · Windows · VirtualBox

---

## 📋 Overview

The goal of this lab was to get hands-on with a real SIEM — the kind of platform a SOC analyst lives in. I set up the Wazuh manager on Ubuntu, connected a Windows host as an agent, and configured File Integrity Monitoring so that any change to a watched folder would generate an alert I could see on the dashboard.

This is the lab most directly tied to where I want to go: a **blue team / SOC analyst** role.

---

## 🗺️ Lab Architecture

| Component | Host | Role |
|---|---|---|
| Wazuh Manager | Ubuntu (VirtualBox) | Collects, analyses, and stores data from agents |
| Wazuh Agent | Windows host | Sends logs and system events to the manager |

Networking: the Ubuntu VM used a **bridged adapter** so the manager and the Windows agent were on the same network and could communicate.

> 📸 **Screenshot needed:** A simple diagram (Windows agent → Wazuh manager), or the Wazuh dashboard "Agents" page showing the connected agent.

---

## 🔧 What I Did

**1. Installed the Wazuh manager**
On Ubuntu, I added the Wazuh GPG key, then downloaded and ran the all-in-one install script, which deploys the manager, indexer, and dashboard together.

```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh
sudo bash ./wazuh-install.sh -a -i
```

The installer finished by giving me the dashboard URL and admin credentials.

> 📸 **Screenshot needed:** Terminal showing the install completing with the generated credentials (blur the password).

**2. Accessed the dashboard**
I logged into the Wazuh dashboard over HTTPS using the Ubuntu VM's IP, accepting the self-signed certificate warning.

> 📸 **Screenshot needed:** Wazuh dashboard home/overview after first login.

**3. Onboarded the Windows agent**
I installed the Wazuh agent on a Windows host, generated an agent key on the manager with `manage_agents`, applied that key in the Windows agent, pointed it at the manager's IP, and restarted the agent service. The agent then showed up as **Active** on the dashboard.

```bash
sudo /var/ossec/bin/manage_agents   # add agent, extract key
```

> 📸 **Screenshot needed:** Wazuh Agent Manager on Windows with the key applied and manager IP set.
> 📸 **Screenshot needed:** Wazuh dashboard "Agents" page showing the Windows agent as Active.

**4. Configured File Integrity Monitoring**
On the Windows agent I edited `ossec.conf` to monitor a specific folder in real time using Syscheck, then restarted the agent to apply it.

```xml
<directories realtime="yes">C:\Users\abc\Test</directories>
```

> 📸 **Screenshot needed:** The `ossec.conf` snippet showing the monitored directory.

**5. Generated and verified alerts**
I created, modified, and deleted files inside the monitored folder, then went to the **Integrity Monitoring** section of the dashboard and confirmed the alerts appeared for each action.

> 📸 **Screenshot needed:** Integrity Monitoring view showing FIM alerts for the file create/modify/delete events.

---

## 🧠 What I Learned

- **What a SIEM actually does.** Setting one up myself — manager, indexer, dashboard, agents — turned "SIEM" from a buzzword into something I understand the moving parts of.
- **The agent/manager model.** Generating and applying an agent key, and pointing the agent at the manager, taught me how endpoints get securely registered and how their data flows in.
- **File Integrity Monitoring is intuitive but powerful.** Watching a file change instantly raise an alert showed me how FIM detects tampering — and why it matters for spotting things like unauthorised changes or malware.
- **Detection needs verification.** Generating my own test events and confirming the alerts taught me to *validate* that monitoring actually works rather than assuming it does after install.
- **This is the analyst's workflow.** Navigating agents, events, and the integrity-monitoring views gave me a real feel for the kind of console a SOC analyst uses day to day.

---

## 🚀 Next Steps

- Add Linux agents and monitor system logs and authentication events.
- Generate security events (failed logins, suspicious processes) and practise investigating them.
- Feed in logs from my other labs (pfSense firewall, SafeLine WAF) to correlate network, web, and endpoint events in one place.
- Explore Wazuh's vulnerability detection and active response features.

---

## 📚 References

- Wazuh official documentation
- Built while following a community home-lab walkthrough, then explored and documented in my own words.
