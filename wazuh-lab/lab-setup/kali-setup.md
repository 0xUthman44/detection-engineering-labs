# Kali Linux Setup

## Overview

Kali Linux was deployed within the SOC homelab environment as the primary attacker and testing workstation.

The system provides a platform for:

* offensive security tooling
* vulnerability assessment
* network reconnaissance
* attack simulation
* traffic generation
* security validation

The VM is used to simulate realistic attacker activity against internal lab systems in order to generate telemetry for monitoring and investigation within Wazuh SIEM.

---

## Objective

The Kali Linux virtual machine was configured to support:

* attack simulation workflows
* vulnerability assessment
* network enumeration
* web application testing
* detection engineering validation
* SIEM visibility testing
* incident investigation exercises

---

## Virtual Machine Configuration

| Setting                 | Value                        |
| ----------------------- | ---------------------------- |
| Operating System        | Kali Linux                   |
| Virtualization Platform | VirtualBox                   |
| RAM                     | 4 GB                         |
| CPUs                    | 2                            |
| Disk Size               | 40 GB                        |
| Network Adapter         | Host-Only / Internal Network |

---

## Network Configuration

The Kali Linux virtual machine operates within the isolated SOC homelab subnet:

```text
192.168.56.103/24
```


---

## Internet Connectivity

The virtual machine retains outbound internet access through a secondary VirtualBox NAT adapter.

This enables:

* package installation
* system updates
* tool downloads
* repository cloning
* vulnerability database updates

while still maintaining an isolated internal lab environment for attack simulation activities.

---

## Connectivity Verification

Internal communication validated using:

```bash
ping 192.168.56.102 (Ubuntu DVWA)
```


---

## System Update

Initial package updates performed using:

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Installed Tooling

The Kali Linux environment includes commonly used offensive security and analysis tools.

| Tool       | Purpose                      |
| ---------- | ---------------------------- |
| Nmap       | Network enumeration          |
| Nikto      | Web server scanning          |
| Gobuster   | Directory enumeration        |
| Wireshark  | Packet analysis              |
| Curl       | HTTP request testing         |
| Netcat     | Network connectivity testing |
| Burp Suite | Web application testing      |

---

## Operational Role in the Lab

Kali Linux functions as the primary attack simulation system within the environment.

The VM is used to:

* generate attack telemetry
* perform enumeration activities
* validate SIEM detections
* simulate adversary behavior
* test network visibility
* support investigation exercises

---

## Security Considerations

The Kali Linux VM is isolated within an internal VirtualBox network and is not exposed directly to external systems.

This ensures:

* safe attack simulation
* controlled telemetry generation
* isolated testing conditions
* secure vulnerability research

---

## Learning Outcomes

* Linux system administration
* Offensive security tooling
* Internal network testing
* Attack simulation workflows
* SIEM telemetry generation
* Detection engineering support
