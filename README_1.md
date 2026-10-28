# Segmented Network Defense & Visibility Lab (pfSense)

A hands-on lab building a **segmented network security architecture** using a pfSense
firewall in VirtualBox, with a focus on **network segmentation, exposure reduction, and
defensive visibility (monitoring blind spots)**.

> Coursework: CYBR 502 — Master's in Cyber Security Engineering

---

## Overview

I deployed and configured a pfSense firewall to enforce trust boundaries between four
networks, then analyzed **where security telemetry can and cannot exist** — the core
tradeoff in defensive monitoring.

**Skills demonstrated:** network segmentation · firewall configuration · DHCP/DNS services ·
trust-boundary analysis · defensive visibility & monitoring blind spots · virtualization ·
technical documentation

---

## Architecture

| pfSense Interface | VirtualBox Adapter | Network         | Subnet            | Gateway        | Hosts |
|-------------------|--------------------|-----------------|-------------------|----------------|-------|
| WAN               | NAT                | Internet        | 10.0.2.0/24 (DHCP)| 10.0.2.2       | —     |
| Management        | Host-Only          | Admin plane     | 192.168.97.0/24   | 192.168.97.1   | Host, Wazuh |
| LAN               | Internal Network   | Internal hosts  | 192.168.100.0/24  | 192.168.100.1  | Kali, Wazuh, AlmaLinux |
| Sandbox           | Internal Network   | Isolated/vuln   | 192.168.105.0/24  | 192.168.105.1  | Metasploitable |

**Trust boundaries enforced:**
- **WAN ↔ internal** — nothing upstream can initiate inbound connections.
- **LAN ↔ Sandbox** — the deliberately vulnerable segment is isolated so a compromise
  there cannot pivot into the LAN.

![Traffic Flow & Visibility](diagrams/traffic-flow.png)

---

## What I Built

- Configured a **Host-Only management network** (DHCP disabled) so pfSense is the single,
  auditable DHCP authority — reducing rogue-DHCP / MITM risk.
- Assigned and addressed **four interfaces** (WAN, Management, LAN, Sandbox), verified by
  MAC address.
- Enabled **DHCP per segment** with defined address pools.
- Created a **dedicated admin account** (accountability / least privilege) instead of the
  shared default.
- Captured a **VM snapshot** as a known-good baseline (repeatability & incident reconstruction).
- Verified connectivity from the firewall shell (`ping`, `nslookup`, `curl`).

---

## Key Finding: East-West Visibility Blind Spot

pfSense only observes traffic that **crosses** an interface. Host-to-host traffic **within**
a single subnet (e.g. LAN ↔ LAN) never reaches the firewall, so it is invisible to it.
Closing that gap requires **host-based telemetry** (e.g. Wazuh agents) or network
mirroring — a central lesson in detection engineering and monitoring coverage.

---

## Screenshots

| | |
|---|---|
| VM network adapters | ![adapters](screenshots/vm-adapters.png) |
| Snapshot baseline | ![snapshot](screenshots/snapshot.png) |
| pfSense dashboard | ![dashboard](screenshots/pfsense-dashboard.png) |
| Firewall connectivity tests | ![shell](screenshots/shell-connectivity.png) |

---

## Tools

VirtualBox · pfSense CE · Kali Linux · Metasploitable 2 · AlmaLinux · Wazuh
