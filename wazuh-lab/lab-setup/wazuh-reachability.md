
# Wazuh Reachability Verification

## Overview

This document outlines the steps performed to verify operational status and service reachability within the Wazuh SIEM environment.

The verification process ensured that all critical Wazuh services were:

* running correctly
* accessible internally
* operational for telemetry collection
* available for dashboard access and monitoring workflows

---

## Objective

The primary goals of this verification process were:

* confirm Wazuh Manager availability
* validate Wazuh Dashboard accessibility
* verify Wazuh Indexer operational status
* ensure SIEM service readiness
* confirm platform stability before attack simulation activities

---

## Lab Environment

| Component   | Role                  | IP Address     |
| ----------- | --------------------- | -------------  |
| Wazuh OVA   | SIEM Platform         | 192.168.56.104 |
| Kali Linux  | Attacker Workstation  | 192.168.56.103 |
| Windows 10  | Monitored Endpoint    | 192.168.56.101 |
| DVWA Server | Vulnerable Web Server | 192.168.56.102 |

---

## Wazuh Service Verification

The following commands were used to verify that all core Wazuh services were operational.

---

## Wazuh Manager Status

The Wazuh Manager service was verified using:

```bash id="mx3qk7"
sudo systemctl status wazuh-manager
```
<img width="1277" height="788" alt="Screenshot (689)" src="https://github.com/user-attachments/assets/e0353979-a580-45ed-b533-07a0e464d791" />

This service is responsible for:

* log processing
* alert generation
* rule evaluation
* agent communication

A successful status output confirmed that the Wazuh Manager was actively running.

---

## Wazuh Dashboard Status

Dashboard availability verified using:

```bash id="p8n2v4"
sudo systemctl status wazuh-dashboard
```
<img width="1276" height="785" alt="Screenshot (690)" src="https://github.com/user-attachments/assets/5efd4636-1d37-4d1e-8ef2-ec9adbf56c55" />

This service provides:

* SIEM dashboard access
* alert visualization
* investigation workflows
* management interface functionality

Successful service status confirmed dashboard availability.

---

## Wazuh Indexer Status

Indexer operational status verified using:

```bash id="u5m7x1"
sudo systemctl status wazuh-indexer
```
<img width="1268" height="791" alt="Screenshot (691)" src="https://github.com/user-attachments/assets/7ac86766-fd4c-4916-ae14-d3a15e9a5516" />

The Wazuh Indexer is responsible for:

* data indexing
* search functionality
* telemetry storage
* log retrieval operations

Successful status output confirmed indexing functionality.

---

## Dashboard Reachability

Dashboard access tested through a web browser using:

```text id="j9x4k2"
https://192.168.56.104
```
<img width="1920" height="820" alt="Screenshot (692)" src="https://github.com/user-attachments/assets/9e120fef-af4f-4646-a7da-29e7c5085712" />

Successful access confirmed:

* internal network connectivity
* dashboard availability
* proper service communication
* SIEM accessibility

---

## Connectivity Validation

Internal communication between lab systems was verified using:

```bash id="v7q2m8"
ping 192.168.56.102
```
<img width="1269" height="501" alt="Screenshot (693)" src="https://github.com/user-attachments/assets/45360b89-7203-4594-b5ad-50cd15c06d00" />

This confirmed successful communication with the Wazuh SIEM platform.

---

## Operational Importance

Verifying service reachability ensures:

* stable SIEM operation
* successful telemetry ingestion
* alert generation capability
* dashboard accessibility
* investigation readiness

This step was completed prior to attack simulation and detection engineering exercises.

---

## Learning Outcomes

* Linux service management
* SIEM operational validation
* Dashboard accessibility verification
* Wazuh architecture familiarity
* Internal network troubleshooting
* Security monitoring readiness
