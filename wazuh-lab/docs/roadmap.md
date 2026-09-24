# Roadmap

## Completed Investigations

* [x] [Nmap Scan Simulation](../attack-simulations/nmap-scan.md) — network reconnaissance and service enumeration
* [x] [Web Enumeration with Nikto](../detections/nikto-enumeration-detection.md) — web vulnerability scanning and IDS/SIEM correlation
* [x] [Directory Enumeration with Gobuster](../attack-simulations/directory-enumeration-gobuster.md) — web reconnaissance and Apache log analysis
* [x] [SSH Brute Force Detection](../attack-simulations/ssh-bruteforce-detection.md) — credential attack simulation and authentication log analysis
* [x] [Windows Endpoint Activity Investigation with Sysmon](../attack-simulations/windows-endpoint-activity-investigation-sysmon.md) — process creation and PowerShell threat hunting
* [x] [Windows Persistence Mechanisms Investigation](../attack-simulations/windows-persistence-detection.md) — Registry, Scheduled Task, Service, and Startup Folder persistence
* [x] [Credential Access Attempts & Suspected Lateral Movement Investigation](../attack-simulations/credential-access-lateral-movement-investigation/README.md) — multi-host discovery, scanning, and brute-force correlation (no confirmed lateral movement)

## Lab Infrastructure Completed

* [x] Kali Linux attacker workstation
* [x] Ubuntu DVWA vulnerable web server
* [x] Windows 10 monitored endpoint with Sysmon
* [x] Wazuh SIEM (Manager, Indexer, Dashboard)
* [x] Suricata network IDS

---

## Planned Investigations

* [ ] Malicious document / macro execution detection
* [ ] PowerShell Empire / C2 beacon detection
* [ ] Credential dumping detection (e.g. LSASS access)
* [ ] Log tampering / defense evasion detection
* [ ] Data exfiltration detection scenario
* [ ] Web application exploitation walkthrough (SQL injection / XSS on DVWA) with full SOC investigation

---

## Active Directory Roadmap

* [ ] Deploy a Windows Server domain controller
* [ ] Join the existing Windows 10 endpoint to the domain
* [ ] Onboard Active Directory logs (Security, Directory Service, Kerberos) into Wazuh
* [ ] Simulate and detect Kerberoasting
* [ ] Simulate and detect *confirmed* lateral movement (e.g. Pass-the-Hash / PsExec) — the current multi-host investigation documents only a suspected, unconfirmed pivot attempt
* [ ] Simulate and detect privilege escalation via AD misconfiguration

---

## Threat Hunting Roadmap

* [ ] Build a library of reusable Wazuh Threat Hunting queries per MITRE ATT&CK technique
* [ ] Formalize MITRE ATT&CK mapping for the Nmap and Nikto investigations (the multi-host investigation already maps Nmap `-sV` to T1046)
* [ ] Introduce hypothesis-driven hunts not tied to a specific prior simulation

## Malware Analysis Roadmap

* [ ] Build an isolated malware analysis / detonation environment
* [ ] Static analysis workflow documentation
* [ ] Dynamic analysis workflow using the existing Sysmon + Wazuh telemetry pipeline

---

## Long-Term Goals

* Expand the lab into a small simulated enterprise environment (multiple endpoints, a domain controller, and segmented subnets)
* Build custom Wazuh detection rules for the persistence and reconnaissance techniques already demonstrated
* Publish a recurring "detection engineering write-up" for each new technique added to the lab
* Use this portfolio as the primary technical reference for SOC Analyst and Detection Engineering job applications
