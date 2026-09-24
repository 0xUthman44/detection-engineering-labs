# Windows 10 Setup

## Overview

Windows 10 was deployed within the SOC homelab environment as a monitored endpoint system.

The virtual machine simulates a standard enterprise workstation and is used to generate endpoint telemetry for monitoring and investigation within Wazuh SIEM.

The system supports:

* endpoint monitoring
* Windows event logging
* attack telemetry generation
* detection engineering validation
* incident investigation workflows

---

## Objective

The Windows 10 virtual machine was configured to support:

* endpoint visibility
* log collection
* security event monitoring
* SIEM alert generation
* attack simulation monitoring
* investigation workflows
* detection engineering exercises

---

## Virtual Machine Configuration

| Setting                 | Value                        |
| ----------------------- | ---------------------------- |
| Operating System        | Windows 10                   |
| Virtualization Platform | VirtualBox                   |
| RAM                     | 4 GB                         |
| CPUs                    | 2                            |
| Disk Size               | 50 GB                        |
| Network Adapter         | Host-Only / Internal Network |

---

## Network Configuration

The Windows 10 virtual machine operates within the isolated SOC homelab subnet:

```text id="y4m8x2"
192.168.56.101/24
```


All systems are configured on the same internal VirtualBox network to allow:

* internal communication
* telemetry generation
* SIEM monitoring
* alert correlation
* attack simulation workflows


---

## Internet Connectivity

The Windows 10 VM retains outbound internet access through a secondary VirtualBox NAT adapter.

This enables:

* Windows updates
* software installation
* Wazuh agent downloads
* browser access
* telemetry updates

while maintaining an isolated internal lab environment.

---

## System Preparation

Initial configuration steps included:

* Windows updates
* hostname configuration
* network verification
* firewall review
* remote management preparation

---

## Connectivity Verification

Internal network communication verified using:

```powershell id="k2x7m5"
ping 192.168.56.102 (Ubuntu DVWA)
```


---

## Wazuh Agent Installation

The Windows endpoint was onboarded into Wazuh SIEM using the Wazuh Windows agent.

The agent enables:

* endpoint telemetry collection
* event forwarding
* alert generation
* security monitoring
* log correlation

---

## Windows Event Monitoring

The Windows endpoint generates telemetry from:

* Security Event Logs
* PowerShell logs
* Authentication events
* Process creation activity
* Network events
* System logs

This data is forwarded to Wazuh for centralized monitoring and investigation.

---

## Operational Role in the Lab

The Windows 10 VM functions as a monitored enterprise endpoint within the SOC environment.

The system is used to:

* generate endpoint telemetry
* simulate user activity
* validate SIEM visibility
* investigate alerts
* monitor attack activity
* support incident response exercises

---

## Security Considerations

The Windows 10 VM is isolated within the internal VirtualBox lab network and is not directly exposed externally.

This ensures:

* controlled attack simulation
* safe telemetry generation
* isolated testing conditions
* secure monitoring workflows

---

## Screenshots


<img width="1920" height="902" alt="Screenshot (683)" src="https://github.com/user-attachments/assets/71ed02f6-ba10-412e-b153-4aca12e5deef" />

<img width="1920" height="900" alt="Screenshot (684)" src="https://github.com/user-attachments/assets/d2ee850c-fd10-4966-84c0-e7e77d830633" />


---

## Learning Outcomes

* Windows endpoint monitoring
* SIEM integration
* Event log analysis
* Endpoint telemetry collection
* Detection engineering workflows
* Incident investigation support

