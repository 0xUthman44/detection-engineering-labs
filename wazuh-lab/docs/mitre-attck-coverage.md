# MITRE ATT&CK Coverage

## Overview

This page consolidates the MITRE ATT&CK techniques explicitly mapped within the individual investigations in this portfolio. Each technique links back to the investigation where it was identified, simulated, and analyzed.

---

## Portfolio-Wide ATT&CK Matrix

| Tactic | Technique | Investigation |
|---|---|---|
| Reconnaissance | T1595 – Active Scanning | [Directory Enumeration with Gobuster](../attack-simulations/directory-enumeration-gobuster.md) |
| Discovery | T1083 – File and Directory Discovery | [Directory Enumeration with Gobuster](../attack-simulations/directory-enumeration-gobuster.md) |
| Credential Access | T1110 – Brute Force | [SSH Brute Force Detection](../attack-simulations/ssh-bruteforce-detection.md) |
| Initial Access *(if successful)* | T1078 – Valid Accounts | [SSH Brute Force Detection](../attack-simulations/ssh-bruteforce-detection.md) |
| Discovery | T1033 – System Owner/User Discovery | [Windows Endpoint Activity Investigation with Sysmon](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |
| Discovery | T1082 – System Information Discovery | [Windows Endpoint Activity Investigation with Sysmon](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |
| Discovery | T1057 – Process Discovery | [Windows Endpoint Activity Investigation with Sysmon](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |
| Discovery | T1087 – Account Discovery | [Windows Endpoint Activity Investigation with Sysmon](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |
| Execution | T1059.001 – PowerShell | [Windows Endpoint Activity Investigation with Sysmon](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |
| Persistence | T1547.001 – Registry Run Keys / Startup Folder | [Windows Persistence Mechanisms Investigation](../attack-simulations/windows-persistence-detection.md) |
| Persistence | T1053.005 – Scheduled Task | [Windows Persistence Mechanisms Investigation](../attack-simulations/windows-persistence-detection.md) |
| Persistence | T1543.003 – Windows Service | [Windows Persistence Mechanisms Investigation](../attack-simulations/windows-persistence-detection.md) |
| Discovery | T1069 – Permission Groups Discovery | [Credential Access & Suspected Lateral Movement Investigation](../attack-simulations/credential-access-lateral-movement-investigation/README.md) |
| Discovery | T1016 – System Network Configuration Discovery | [Credential Access & Suspected Lateral Movement Investigation](../attack-simulations/credential-access-lateral-movement-investigation/README.md) |
| Discovery | T1046 – Network Service Discovery | [Credential Access & Suspected Lateral Movement Investigation](../attack-simulations/credential-access-lateral-movement-investigation/README.md) |
| Credential Access | T1110.001 – Brute Force: Password Guessing | [Credential Access & Suspected Lateral Movement Investigation](../attack-simulations/credential-access-lateral-movement-investigation/README.md) |

---

## Investigation Mapping Summary

| Investigation | Tactics Covered |
|---|---|
| [Nmap Scan Simulation](../attack-simulations/nmap-scan.md) | Reconnaissance (network/service enumeration — not yet formally ATT&CK-mapped in the write-up) |
| [Web Enumeration with Nikto](../detections/nikto-enumeration-detection.md) | Reconnaissance (web vulnerability scanning — not yet formally ATT&CK-mapped in the write-up) |
| [Directory Enumeration with Gobuster](../attack-simulations/directory-enumeration-gobuster.md) | Reconnaissance, Discovery |
| [SSH Brute Force Detection](../attack-simulations/ssh-bruteforce-detection.md) | Credential Access, Initial Access |
| [Windows Endpoint Activity Investigation with Sysmon](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) | Discovery, Execution |
| [Windows Persistence Mechanisms Investigation](../attack-simulations/windows-persistence-detection.md) | Persistence |
| [Credential Access & Suspected Lateral Movement Investigation](../attack-simulations/credential-access-lateral-movement-investigation/README.md) | Discovery, Credential Access, Execution (PowerShell as the discovery vector); Windows-side pivot attempt documented as temporal correlation only, not confirmed lateral movement |

---

## Future ATT&CK Expansion

The following tactics are not yet represented in this portfolio and are tracked in the [Roadmap](roadmap.md):

* **Lateral Movement** — no confirmed lateral movement is demonstrated yet. The multi-host investigation documents a suspected pivot attempt (SMB-port activity and logon failures) that could not be source-attributed, so it is deliberately not counted as coverage. Confirmed lateral movement is planned with the Active Directory lab expansion
* **Privilege Escalation** — planned local/AD privilege escalation scenarios
* **Defense Evasion** — planned log tampering / detection evasion investigations
* **Command and Control** — planned C2 beacon detection exercise
* **Exfiltration** — planned data exfiltration detection scenario
* **Formal ATT&CK mapping for the Nmap and Nikto reconnaissance investigations** (currently documented narratively but without a dedicated mapping table)
