# Wazuh OVA Setup

## Overview

The Wazuh SIEM platform was deployed within the homelab using the official Wazuh OVA (Open Virtual Appliance) image, providing a pre-configured Wazuh Manager, Indexer, and Dashboard for centralized log collection, detection, and investigation.

> **Note:** This document is a placeholder for the detailed OVA deployment write-up (import steps, initial network configuration, and first-boot credentials). See [`wazuh-reachability.md`](wazuh-reachability.md) for verification that the deployed services are operational.

---

## Lab Environment

| Component   | Role                  | IP Address     |
| ----------- | --------------------- | -------------- |
| Wazuh OVA   | SIEM Platform         | 192.168.56.104 |
| Kali Linux  | Attacker Workstation  | 192.168.56.103 |
| Windows 10  | Monitored Endpoint    | 192.168.56.101 |
| DVWA Server | Vulnerable Web Server | 192.168.56.102 |

---

## Related Documentation

* [Wazuh Reachability Verification](wazuh-reachability.md) — confirms the Wazuh Manager, Indexer, and Dashboard services are running after deployment.
* [Lab Architecture](../docs/lab-architecture.md) — full network and telemetry diagram showing how this appliance fits into the environment.
