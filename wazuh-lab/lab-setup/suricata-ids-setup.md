# Suricata IDS Setup

## Overview

Suricata was deployed within the SOC homelab environment as a Network Intrusion Detection System (NIDS) to monitor network traffic and detect suspicious activity across internal lab systems.

The platform provides visibility into:

* network-based attacks
* suspicious traffic patterns
* protocol activity
* reconnaissance attempts
* intrusion detection events

Suricata complements Wazuh SIEM by generating network telemetry that can be analyzed and correlated during detection engineering and investigation workflows.

---

## Objective

The primary goals of deploying Suricata were:

* monitor internal network traffic
* detect suspicious activity
* generate IDS alerts
* improve network visibility
* support detection engineering workflows
* simulate enterprise IDS monitoring
* enhance incident investigation capabilities

---

## Suricata Deployment Location

Suricata was installed on the Ubuntu-based DVWA server within the homelab environment.

This configuration allowed the IDS to directly monitor:

* incoming HTTP traffic
* web enumeration activity
* internal network reconnaissance
* attack simulation traffic generated from the Kali Linux attacker VM

By deploying Suricata on the same system hosting DVWA, the platform was able to inspect traffic targeting the vulnerable web application while simultaneously generating logs for:

* Apache
* Suricata IDS
* Wazuh SIEM correlation

This architecture improved visibility into:

* web-based attacks
* reconnaissance activity
* suspicious network behavior
* attack telemetry generation

---

## Monitored Traffic Flow

```text id="u9m4x1"
Kali Linux  --->  DVWA + Suricata  --->  Wazuh SIEM
```

Suricata monitored traffic originating from:

* Kali Linux attacker workstation
* internal lab systems
* HTTP requests targeting DVWA

This enabled realistic IDS alert generation during attack simulation exercises.

---

## Installation

Suricata installed using:

```bash id="m5x8q2"
sudo apt update
sudo apt install suricata -y
```

Installation verified using:

```bash id="r3n7v4"
suricata --build-info
```
<img width="1920" height="273" alt="Screenshot (694)" src="https://github.com/user-attachments/assets/5081ac56-86f4-4045-8afc-6fa4a8242478" />

---

## Service Verification

Suricata service status checked using:

```bash id="q8m2x5"
sudo systemctl status suricata
```
<img width="1920" height="423" alt="Screenshot (695)" src="https://github.com/user-attachments/assets/7e9cdf43-3f4d-4a8b-bfb4-ba469bc484cd" />

Service enabled and started using:

```bash id="t6v1n9"
sudo systemctl enable suricata
sudo systemctl start suricata
```

---

## Network Interface Configuration

Available interfaces identified using:

```bash id="p4x7m2"
ip a
```

Suricata configured to monitor the primary internal network adapter associated with the lab subnet.

Example monitored interface:

```text id="k2m8v5"
enp0s8
```
<img width="1855" height="271" alt="Screenshot (696)" src="https://github.com/user-attachments/assets/40682e74-1ded-49c9-977d-a6033a8dac17" />


---

## Suricata Configuration

Primary configuration file:

```text id="v1q7x4"
/etc/suricata/suricata.yaml
```
<img width="1856" height="512" alt="Screenshot (697)" src="https://github.com/user-attachments/assets/e2da4124-fbf5-467f-a64e-572a635ccb82" />

Network interface updated within the configuration file to monitor internal lab traffic.

---

## Rule Updates

Suricata rule sets updated using:

```bash id="y5m2n8"
sudo suricata-update
```
<img width="1920" height="600" alt="Screenshot (698)" src="https://github.com/user-attachments/assets/401e7b86-a45e-45ca-9f20-094cb04e3dd9" />

This downloaded and applied the latest IDS detection rules.

---

## Alert Logging

Suricata alerts stored within:

```text id="c8x4m1"
/var/log/suricata/
```
<img width="1920" height="176" alt="Screenshot (700)" src="https://github.com/user-attachments/assets/b037ba56-2654-42c4-b02b-537126ee4b8a" />

Key log files include:

| File      | Purpose                |
| --------- | ---------------------- |
| fast.log  | Alert summaries        |
| eve.json  | Structured JSON events |
| stats.log | Engine statistics      |

---

## Traffic Monitoring

Suricata monitored traffic between:

* Kali Linux
* DVWA server
* Windows 10 endpoint
* Wazuh SIEM platform

This enabled detection of:

* network scans
* suspicious HTTP requests
* enumeration activity
* protocol anomalies

---

## Integration with Wazuh

Suricata alerts can be ingested into Wazuh SIEM for:

* centralized monitoring
* alert correlation
* investigation workflows
* detection engineering
* threat visibility

This improves overall network and endpoint visibility within the homelab environment.

---

## Operational Role in the Lab

Suricata functions as the primary network intrusion detection platform within the SOC environment.

The platform is used to:

* monitor internal traffic
* detect suspicious behavior
* generate IDS alerts
* support investigations
* validate attack simulations
* improve network visibility

---

## Security Considerations

Suricata operates within the isolated internal VirtualBox network to safely monitor simulated attack traffic generated during lab exercises.

This enables:

* controlled attack simulation
* secure IDS testing
* realistic telemetry generation
* safe detection engineering experimentation

---


## Learning Outcomes

* IDS deployment and configuration
* Network traffic monitoring
* Alert analysis
* Intrusion detection workflows
* SIEM integration
* Detection engineering fundamentals
