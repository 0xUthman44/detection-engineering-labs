# Lab Architecture

## Overview

This document describes the architecture of the SOC homelab environment used throughout this portfolio. The lab is built entirely on an isolated VirtualBox network and combines an attacker workstation, a vulnerable web server, a monitored Windows endpoint, and a centralized Wazuh SIEM deployment with Suricata IDS for network-level visibility.

The environment is designed to mirror the core components of an enterprise security monitoring stack — endpoint telemetry (Sysmon), network telemetry (Suricata), web server logging (Apache), and centralized detection/investigation (Wazuh) — at a scale suitable for hands-on detection engineering and SOC analyst practice.

---

## Network Diagram

```text
                        ┌─────────────────────────────┐
                        │        Wazuh OVA             │
                        │   192.168.56.104              │
                        │  Manager · Indexer · Dashboard│
                        └───────────────┬───────────────┘
                                        │
                     Telemetry ingestion & alerting
                                        │
      ┌────────────────────┬───────────┴───────────┬────────────────────┐
      │                    │                       │                    │
┌─────▼──────┐      ┌──────▼───────┐        ┌──────▼───────┐     ┌──────▼──────┐
│ Kali Linux  │      │ Ubuntu DVWA  │        │  Windows 10   │     │  (future)    │
│ Attacker    │─────►│ + Apache2    │        │  Endpoint     │     │  Active      │
│ Workstation │      │ + Suricata   │        │  + Sysmon     │     │  Directory   │
│ 192.168.56  │      │  IDS         │        │  + Wazuh      │     │  Lab         │
│   .103      │      │ 192.168.56   │        │  Agent        │     │              │
└─────────────┘      │   .102       │        │ 192.168.56    │     └─────────────┘
                      └──────────────┘        │   .101        │
                                              └───────────────┘
```

All systems share the isolated `192.168.56.0/24` Host-Only network. Each Linux and Windows guest additionally retains a secondary NAT adapter for outbound internet access (package installation, Wazuh agent downloads, rule updates), keeping the internal attack-simulation traffic fully separated from external networks.

---

## Components

| Component | Role | IP Address | Details |
|---|---|---|---|
| **Wazuh OVA** | SIEM Platform | 192.168.56.104 | Wazuh Manager, Indexer, and Dashboard deployed via the official OVA appliance. See [Wazuh OVA Setup](../lab-setup/wazuh-ova-setup.md) and [Wazuh Reachability Verification](../lab-setup/wazuh-reachability.md). |
| **Kali Linux** | Attacker Workstation | 192.168.56.103 | Hosts offensive tooling (Nmap, Nikto, Gobuster, Hydra, Wireshark, Burp Suite) used to generate realistic attack telemetry. See [Kali Linux Setup](../lab-setup/kali-setup.md). |
| **Ubuntu DVWA Server** | Vulnerable Web Server | 192.168.56.102 | Runs Apache2, PHP, and MariaDB hosting DVWA (Damn Vulnerable Web Application), plus a co-located Suricata IDS instance monitoring inbound traffic. See [DVWA Deployment](../lab-setup/dvwa-setup.md) and [Suricata IDS Setup](../lab-setup/suricata-ids-setup.md). |
| **Windows 10 Endpoint** | Monitored Workstation | 192.168.56.101 | Simulates an enterprise endpoint. Runs Sysmon for process, registry, and file-creation telemetry, forwarded via the Wazuh Windows agent. See [Windows 10 Setup](../lab-setup/windows-10-setup.md). |

---

## Telemetry Flow

```text
Endpoint / Network Activity
        │
        ├── Sysmon (Windows 10)  ───────────► Wazuh Agent ───────┐
        │                                                        │
        ├── Apache access.log (DVWA)  ──────────────────────────┤
        │                                                        ├──► Wazuh Manager ──► Wazuh Indexer ──► Wazuh Dashboard
        ├── auth.log (SSH, DVWA host) ───────────────────────────┤              (Threat Hunting / Investigation)
        │                                                        │
        └── Suricata eve.json / fast.log (Network IDS) ──────────┘
```

* **Sysmon** on the Windows 10 endpoint captures process creation (Event ID 1), registry modification (Event ID 13), and file creation (Event ID 11) events, forwarded to Wazuh by the Windows agent.
* **Apache access logs** on the DVWA server capture all HTTP requests, including reconnaissance activity such as directory brute-forcing and vulnerability scanning.
* **Linux authentication logs** (`/var/log/auth.log`) capture SSH login attempts, including brute-force activity.
* **Suricata**, deployed on the DVWA host, inspects network traffic and writes alerts to `/var/log/suricata/` (`fast.log`, `eve.json`, `stats.log`), providing network-layer detection alongside host-based telemetry.
* All of the above sources are centralized in **Wazuh**, where they can be searched, correlated, and investigated through the Threat Hunting module.

---

## Design Principles

* **Isolation** — All attack simulation traffic stays within the internal `192.168.56.0/24` network; outbound internet access is only used for updates and tooling, never for live attack traffic.
* **Layered visibility** — Endpoint (Sysmon), network (Suricata), web (Apache), and authentication (auth.log) telemetry are all collected so that investigations can be cross-correlated rather than relying on a single log source.
* **Realistic workflows** — Investigations follow the same detection → threat hunting → analyst assessment → recommendation structure used in production SOC environments.

See [Detection Strategy](detection-strategy.md) for how this telemetry is used during investigations, and [Roadmap](roadmap.md) for planned architecture expansions (e.g. Active Directory).
