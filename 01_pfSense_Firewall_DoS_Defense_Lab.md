# 🔥 Network Firewall & DoS Defense Home Lab (pfSense)

A hands-on home lab where I built a segmented network around a **pfSense** firewall, simulated a **Denial-of-Service (DoS)** attack from Kali Linux, and then mitigated it with firewall rules — verifying the result through traffic capture and logs.

**Tech:** pfSense · Kali Linux · Ubuntu · VirtualBox · hping3 · Wireshark

---

## 📋 Overview

The goal of this lab was to understand how a firewall sits between an untrusted network and an internal one, and how it can be used to detect and stop a flood-based attack. I set up three virtual machines — a pfSense firewall, a Kali attacker, and an Ubuntu victim — across two separate network segments, launched a DoS flood, and then blocked it at the firewall.

This was as much about understanding *network segmentation and traffic flow* as it was about the attack itself.

---

## 🗺️ Lab Architecture

| Device | Segment | Role |
|---|---|---|
| pfSense | WAN (bridged) + LAN (internal) | Edge firewall between the two zones |
| Kali Linux | WAN side | External attacker |
| Ubuntu Desktop | LAN side (isolated) | Victim host |

- **WAN**: bridged to my home network so pfSense pulls a real DHCP address.
- **LAN**: an isolated internal VirtualBox network, with pfSense handing out DHCP addresses and acting as the gateway.

> 📸 **Screenshot:** A simple network/topology diagram (I can draw one, or use a VirtualBox network settings screenshot showing the bridged + internal adapters).

---

## 🔧 What I Did

**1. Built the environment**
I created three VMs in VirtualBox and gave pfSense two network adapters — one bridged (WAN) and one on an internal network (LAN). Kali went on the WAN side as the attacker; Ubuntu went on the isolated LAN side as the victim.

> 📸 **Screenshot:** pfSense VM network settings showing Adapter 1 (Bridged) and Adapter 2 (Internal Network).

**2. Configured pfSense**
I assigned the interfaces (WAN/LAN), let the WAN grab a DHCP address from my home router, and enabled a DHCP server on the LAN so the Ubuntu victim would get an address automatically. I then logged into the pfSense web GUI to manage everything.

> 📸 **Screenshot:** pfSense dashboard showing assigned WAN and LAN interfaces and IP addresses.
> 📸 **Screenshot:** DHCP server configuration page for the LAN.

**3. Set up reachability for the test**
To actually demonstrate the attack, I added a temporary firewall rule allowing the Kali host to reach the Ubuntu victim, and added a static route on Kali so it could reach the internal LAN subnet through the pfSense WAN address.

> 📸 **Screenshot:** The "allow" firewall rule on the WAN interface.
> 📸 **Screenshot:** Successful ping from Kali to the Ubuntu victim (proof of reachability before the attack).

**4. Launched the DoS attack**
From Kali, I used **hping3** to launch flood attacks against the victim (ICMP flood and a SYN flood on a target port). On the Ubuntu side I ran **Wireshark** and watched the packet rate spike dramatically.

```bash
# ICMP flood
sudo hping3 -1 --flood <victim-ip>

# SYN flood on port 80
sudo hping3 --flood -S -p 80 <victim-ip>
```

> 📸 **Screenshot needed:** Wireshark on Ubuntu showing the flood of packets during the attack (the spike is the money shot).

**5. Blocked the attack and verified**
Back in pfSense, I added a **block rule** at the top of the WAN ruleset to drop traffic from the attacker to the victim, with logging enabled. After applying it, the flood stopped — Wireshark went quiet — and I confirmed the blocked traffic in the pfSense firewall logs.

> 📸 **Screenshot** The block rule in pfSense (placed above the allow rule).
> 📸 **Screenshot** Wireshark *after* the block — traffic back to normal.
> 📸 **Screenshot:** pfSense firewall logs showing the blocked attacker traffic.

---

## 🧠 What I Learned

- **Rule order matters.** pfSense evaluates rules top-down, so the block rule had to sit *above* the allow rule to take effect. This made firewall rule processing click for me in a way reading about it never did.
- **Segmentation is powerful.** Keeping the victim on an isolated internal network meant the only path in was through pfSense — which is exactly why the firewall could shut the attack down completely.
- **Seeing traffic is believing.** Watching the packet rate explode in Wireshark and then flatline after the block rule made the impact (and the fix) concrete.
- **DoS basics.** I now understand the difference between an ICMP flood and a SYN flood, and why volume-based attacks are dangerous even in a tiny lab.
- **Logging is part of defense.** A block rule without logging tells you nothing; enabling logs let me actually *prove* the mitigation worked.

---

## 🚀 Next Steps

- Add rate-limiting / connection-limit rules instead of a flat block, to mitigate without fully denying a source.
- Forward pfSense logs into a SIEM (I cover a Wazuh lab separately) to correlate firewall events.
- Experiment with pfSense packages like Snort/Suricata for IDS/IPS on the same setup.

---

## 📚 References

- pfSense official documentation
