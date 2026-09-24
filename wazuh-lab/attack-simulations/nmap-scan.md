# Nmap Scan Simulation

## Overview

Nmap was used within the SOC homelab environment to perform network reconnaissance and service enumeration against internal lab systems.

The scan activity simulated common attacker reconnaissance behavior and generated telemetry that could be monitored and investigated inside Wazuh SIEM.

This exercise focused on:

* host discovery
* port scanning
* service enumeration
* attack telemetry generation
* detection engineering validation

---

## Objective

The goals of this simulation were:

* identify active hosts within the lab network
* enumerate open ports and services
* generate network reconnaissance telemetry
* validate SIEM visibility
* monitor attacker activity through Wazuh
* support detection engineering workflows

---

## Lab Environment

| System      | Role                  |   IP Address   |
| ----------- | --------------------- | -------------  |
| Kali Linux  | Attacker Workstation  | 192.168.56.103 |
| DVWA Server | Vulnerable Web Server | 192.168.56.102 |
| Wazuh OVA   | SIEM Platform         | 192.168.56.104 |
| Windows 10  | Monitored Endpoint    | 192.168.56.101 |

---

## Tool Used

| Tool | Purpose                                        |
| ---- | ---------------------------------------------- |
| Nmap | Network reconnaissance and service enumeration |

---

## Host Discovery Scan

Initial host discovery performed using:

```bash id="v4m8q1"
nmap -sn 192.168.56.0/24
```
 <img width="1920" height="280" alt="Screenshot (685)" src="https://github.com/user-attachments/assets/01c170da-cc9b-4e28-8407-8907559e279a" />

This scan identified active systems within the internal lab subnet.

---

## Port Scan

Port enumeration against the DVWA server performed using:

```bash id="j7x2n5"
nmap 192.168.56.102
```
<img width="1920" height="164" alt="Screenshot (688)" src="https://github.com/user-attachments/assets/75a6cbd8-e341-46db-819d-e9f7bcf05df3" />


This identified open services exposed by the target system.

---

## Service Enumeration

Version detection scan performed using:

```bash id="m2k8v4"
nmap -sV 192.168.56.102
```
<img width="1920" height="200" alt="Screenshot (687)" src="https://github.com/user-attachments/assets/daa5435d-73f0-4657-be15-ef1c7709c7de" />


This provided:

* service versions
* running applications
* exposed services
* additional reconnaissance data

---

## Example Findings

Example services identified during enumeration:

| Port | Service |
| ---- | ------- |
| 22   | SSH     |
| 80   | HTTP    |
| 3306 | MySQL   |

---

## Detection Engineering Relevance

The Nmap activity generated telemetry that can be used to:

* detect reconnaissance behavior
* identify port scanning activity
* correlate attacker IP addresses
* monitor suspicious network enumeration
* validate SIEM alerting

---

## Wazuh Monitoring

Relevant telemetry sources include:

* Apache access logs
* system authentication logs
* network connection logs
* endpoint monitoring events

These logs can be analyzed inside Wazuh to investigate attacker behavior.

---

## Operational Impact

Reconnaissance activity is often an early-stage attacker behavior associated with:

* internal network mapping
* service identification
* attack surface discovery
* lateral movement preparation

Monitoring this activity improves visibility into potential malicious behavior.

---

## Learning Outcomes

* Network reconnaissance techniques
* Port scanning methodology
* Service enumeration
* SIEM telemetry analysis
* Detection engineering validation
* Investigation workflows

