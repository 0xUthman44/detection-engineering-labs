# Detection Strategy

## Detection Philosophy

Every investigation in this portfolio follows the same underlying philosophy: **generate realistic telemetry, detect it with the same tools a SOC analyst would use, and investigate it the same way an analyst would in a live environment.** Rather than reading about attacker techniques, each project simulates the technique, captures the resulting telemetry, and works backward from raw logs to a documented analyst assessment — including MITRE ATT&CK mapping, indicators, and defensive recommendations.

This mirrors the core SOC analyst workflow:

```text
Simulate / Observe Activity
        │
        ▼
   Detect (SIEM / IDS alert or telemetry review)
        │
        ▼
  Threat Hunt (targeted queries across log sources)
        │
        ▼
  Investigate (process trees, timelines, correlation)
        │
        ▼
  Assess (determine legitimate vs. malicious)
        │
        ▼
  Report (indicators, MITRE mapping, recommendations)
```

---

## SIEM Workflow

Wazuh serves as the central point of investigation for this lab. The general workflow used across investigations is:

1. **Telemetry review** — confirm the relevant log source (Sysmon, Apache, auth.log, Suricata) is being ingested and indexed.
2. **Threat Hunting queries** — targeted searches such as `event.provider:Sysmon`, `process.name:reg.exe`, or filtering by source IP, to isolate the activity of interest.
3. **Event correlation** — cross-referencing multiple event types (e.g. Sysmon Event ID 1 process creation alongside Windows Security Event 4624/4625) to build a complete picture.
4. **Timeline reconstruction** — ordering events chronologically to understand the sequence of attacker or administrative activity.
5. **Analyst assessment** — a written determination of whether the observed activity is benign, simulated, or indicative of compromise.

---

## Endpoint Detection

Endpoint visibility is provided by **Sysmon** on the Windows 10 workstation, forwarded to Wazuh via the Windows agent. Key event types used throughout the investigations:

| Event ID | Description | Used In |
|---|---|---|
| 1 | Process Creation | [Windows Endpoint Activity Investigation](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md), [Windows Persistence Investigation](../attack-simulations/windows-persistence-detection.md) |
| 11 | File Create | [Windows Persistence Investigation](../attack-simulations/windows-persistence-detection.md) (Startup folder persistence) |
| 13 | Registry Value Set | [Windows Persistence Investigation](../attack-simulations/windows-persistence-detection.md) (Registry Run Key) |
| 4624 / 4625 | Windows Security Logon Success/Failure | [Windows Endpoint Activity Investigation](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |
| 4688 | Windows Security Process Creation | [Windows Endpoint Activity Investigation](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |
| 4720 / 4726 | User Account Created/Deleted | [Windows Endpoint Activity Investigation](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) |

Process tree reconstruction (parent-child relationships) is used consistently to distinguish legitimate interactive administration (e.g. processes spawned from `explorer.exe`) from anomalous execution chains.

---

## Network Detection

Network-layer visibility is provided by **Suricata**, deployed alongside the DVWA server, and by **Apache access logs** on the same host. These sources are used to detect:

* Reconnaissance and directory enumeration ([Gobuster investigation](../attack-simulations/directory-enumeration-gobuster.md))
* Web vulnerability scanning ([Nikto investigation](../detections/nikto-enumeration-detection.md))
* Port and service scanning ([Nmap investigation](../attack-simulations/nmap-scan.md))

Indicators reviewed at the network layer typically include source IP, request volume, HTTP response code distribution (high rates of 403/404), and repeated requests to sensitive paths (`.htaccess`, `.htpasswd`, `server-status`).

---

## Threat Hunting Methodology

Threat hunting in this lab is deliberately **hypothesis-driven** rather than alert-driven: each investigation starts from a plausible technique (e.g. "an attacker used Registry Run Keys for persistence") and uses targeted Wazuh queries to confirm or rule out that hypothesis, rather than waiting for a pre-built alert to fire. Representative queries used across investigations include:

```text
event.provider:Sysmon
win.system.providerName:Microsoft-Windows-Sysmon
process.name:reg.exe
process.name:schtasks.exe
process.name:sc.exe
sshd
authentication failure
failed password
```

---

## Detection Lifecycle

1. **Preparation** — verify lab connectivity and telemetry pipelines before simulating activity (see [Wazuh Reachability Verification](../lab-setup/wazuh-reachability.md)).
2. **Simulation** — execute the attacker technique from Kali Linux or directly on the target system.
3. **Detection / Hunting** — identify the resulting telemetry in Wazuh.
4. **Investigation** — reconstruct timelines and process trees, correlate multiple log sources.
5. **Assessment** — document findings, indicators, and a clear analyst conclusion.
6. **Remediation validation** (where applicable) — confirm that persistence mechanisms or test accounts were fully removed and no residual artifacts remain.
7. **Reporting** — MITRE ATT&CK mapping and defensive recommendations for each investigation.

---

## Telemetry Sources Summary

| Source | Collected From | Primary Use |
|---|---|---|
| Sysmon | Windows 10 endpoint | Process, registry, and file-level endpoint detection |
| Windows Security Event Log | Windows 10 endpoint | Logon and account management correlation |
| Apache `access.log` | Ubuntu DVWA server | Web reconnaissance and enumeration detection |
| `auth.log` | Ubuntu DVWA server | SSH authentication and brute-force detection |
| Suricata `eve.json` / `fast.log` | Ubuntu DVWA server | Network-layer IDS alerting |
| Wazuh Indexer / Dashboard | Wazuh OVA | Centralized search, correlation, and investigation |

See [Lab Architecture](lab-architecture.md) for how these sources fit into the overall environment, and [MITRE ATT&CK Coverage](mitre-attck-coverage.md) for the techniques validated by this telemetry.
