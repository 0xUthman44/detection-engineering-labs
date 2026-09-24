# Wazuh Detection Lab

[← Back to Detection Engineering Labs](../README.md)


## Professional Overview

This portfolio demonstrates practical, demonstrable competence using wazuh as the SIEM:

* Alert triage and log analysis
* SIEM investigation and threat hunting (Wazuh)
* Endpoint detection using Sysmon telemetry
* Network intrusion detection (Suricata)
* Web server log analysis (Apache)
* Authentication and credential-attack monitoring
* Attack simulation and detection validation
* MITRE ATT&CK-mapped incident reporting

The objective is straightforward: detections I've written, attacks I've simulated, incidents I've investigated, and tooling I can confidently explain in an interview setting.

---

## Architecture Overview

The lab is built on an isolated VirtualBox network combining an attacker workstation, a vulnerable web server, a monitored Windows endpoint, and a centralized Wazuh SIEM with Suricata IDS for network visibility.

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
│ Kali Linux  │      │ Ubuntu DVWA  │        │  Windows 10   │     │  (planned)   │
│ Attacker    │─────►│ + Apache2    │        │  Endpoint     │     │  Active      │
│ Workstation │      │ + Suricata   │        │  + Sysmon     │     │  Directory   │
│ 192.168.56  │      │  IDS         │        │  + Wazuh      │     │  Lab         │
│   .103      │      │ 192.168.56   │        │  Agent        │     │              │
└─────────────┘      │   .102       │        │ 192.168.56    │     └─────────────┘
                      └──────────────┘        │   .101        │
                                              └───────────────┘
```

Full architecture, telemetry flow, and design rationale: [`docs/lab-architecture.md`](docs/lab-architecture.md)
Detection philosophy and SIEM workflow: [`docs/detection-strategy.md`](docs/detection-strategy.md)

---

## Technology Stack

| Category | Tools |
|---|---|
| SIEM | Wazuh (Manager, Indexer, Dashboard) |
| Network IDS | Suricata |
| Endpoint Telemetry | Sysmon, Windows Security Event Log |
| Web Server / App | Apache2, PHP, MariaDB, DVWA |
| Offensive Tooling | Nmap, Nikto, Gobuster, Hydra, Wireshark, Burp Suite |
| Virtualization | VirtualBox (Kali Linux, Ubuntu, Windows 10, Wazuh OVA) |

---

## Featured Investigations

| Investigation | Focus | MITRE Tactics |
|---|---|---|
| [Credential Access Attempts & Suspected Lateral Movement Investigation](attack-simulations/credential-access-lateral-movement-investigation/README.md) | Multi-host correlation across Windows and Ubuntu: PowerShell-driven discovery, Nmap scan, failed SSH brute force, SMB and logon-failure activity; unconfirmed pivot documented as such | Discovery, Credential Access |
| [Windows Persistence Mechanisms Investigation](attack-simulations/windows-persistence-detection.md) | Registry Run Keys, Scheduled Tasks, Windows Services, Startup Folder persistence, process-tree reconstruction | Persistence |
| [Windows Endpoint Activity Investigation with Sysmon](attack-simulations/windows-endpoint-activity-investigation-sysmon.md) | Process creation analysis, encoded PowerShell, discovery command investigation | Discovery, Execution |
| [SSH Brute Force Detection](attack-simulations/ssh-bruteforce-detection.md) | Hydra-based credential attack, `auth.log` analysis, Wazuh threat hunting | Credential Access, Initial Access |
| [Directory Enumeration with Gobuster](attack-simulations/directory-enumeration-gobuster.md) | Web reconnaissance, Apache log analysis, Wazuh correlation | Reconnaissance, Discovery |
| [Web Enumeration with Nikto](detections/nikto-enumeration-detection.md) | Web vulnerability scanning across Apache, Suricata, and Wazuh | Reconnaissance |
| [Nmap Scan Simulation](attack-simulations/nmap-scan.md) | Host discovery, port scanning, service enumeration | Reconnaissance |

Each write-up includes lab environment details, commands executed, annotated screenshots, Wazuh threat-hunting queries, detection indicators, a SOC-style analyst assessment, and defensive recommendations.

---

## Skills Demonstrated

* **SIEM Operations** — Wazuh deployment, service verification, dashboard investigation, and threat hunting query construction
* **Endpoint Forensics** — Sysmon-based process tree reconstruction, parent-child process analysis, registry and file-creation event review
* **Network Security Monitoring** — Suricata IDS deployment, rule updates, and alert review
* **Log Analysis** — Apache access logs, Linux `auth.log`, Windows Security Event Log correlation
- **Attack Simulation** — Nmap, Nikto, Gobuster, and Hydra used to generate realistic, investigable telemetry
* **Cross-Source Correlation** — tying Sysmon, Windows Security events, Wazuh rules, and Linux `auth.log` into one timeline, and separating confirmed links from temporal-only ones
* **Incident Documentation** — MITRE ATT&CK mapping, indicators of compromise, timelines, and remediation recommendations
* **Linux & Windows Administration** — service management, registry editing, scheduled tasks, and Windows service configuration

---

## MITRE ATT&CK Coverage

| Tactic | Techniques Covered |
|---|---|
| Reconnaissance | T1595 – Active Scanning |
| Discovery | T1083, T1033, T1082, T1057, T1087, T1069, T1016, T1046 |
| Execution | T1059.001 – PowerShell |
| Persistence | T1547.001, T1053.005, T1543.003 |
| Credential Access | T1110, T1110.001 – Brute Force / Password Guessing |
| Initial Access | T1078 – Valid Accounts *(conditional)* |

Full technique-to-investigation matrix and planned expansion (confirmed Lateral Movement, Privilege Escalation, Defense Evasion, C2): [`docs/mitre-attck-coverage.md`](docs/mitre-attck-coverage.md)

---

## Repository Structure

```text
wazuh-lab/
│
├── README.md
│
├── docs/
│   ├── lab-architecture.md
│   ├── detection-strategy.md
│   ├── mitre-attck-coverage.md
│   └── roadmap.md
│
├── attack-simulations/
│   ├── credential-access-lateral-movement-investigation/
│   │   ├── README.md
│   │   └── screenshots/
│   ├── windows-endpoint-activity-investigation-sysmon.md
│   ├── windows-persistence-detection.md
│   ├── ssh-bruteforce-detection.md
│   ├── directory-enumeration-gobuster.md
│   └── nmap-scan.md
│
├── detections/
│   └── nikto-enumeration-detection.md
│
├── lab-setup/
│   ├── dvwa-setup.md
│   ├── suricata-ids-setup.md
│   ├── kali-setup.md
│   ├── windows-10-setup.md
│   ├── wazuh-ova-setup.md
│   └── wazuh-reachability.md
│
├── assets/
└── scripts/
```

---

## Screenshots

Every investigation and lab-setup document includes inline, annotated screenshots (Wazuh dashboards, terminal output, threat-hunting query results, and process trees) embedded directly in the corresponding Markdown file. See any file under [`attack-simulations/`](attack-simulations), [`detections/`](detections), or [`lab-setup/`](lab-setup) for the full visual walkthrough.

---

## Roadmap

* Active Directory lab expansion (Kerberoasting, confirmed lateral movement, AD-based privilege escalation)
* C2 beacon and credential-dumping detection scenarios
* Log tampering / defense evasion investigations
* Custom Wazuh detection rules for techniques already demonstrated in this lab

Full roadmap: [`docs/roadmap.md`](docs/roadmap.md)

---

## Contact

* **LinkedIn:** [linkedin.com/in/uthman-hammed](https://linkedin.com/in/uthman-hammed)
* **Email:** hammeduthman90@gmail.com

Open to SOC Analyst, Detection Engineering, and Blue Team opportunities.
