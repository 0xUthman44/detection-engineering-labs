# DVWA Deployment

## Overview

Damn Vulnerable Web Application (DVWA) was deployed inside the homelab environment to simulate a vulnerable web server for attack simulation and detection engineering exercises.

The application provides intentionally vulnerable web functionality that can be used to generate realistic attack telemetry for monitoring inside Wazuh SIEM.

---

## Objective

The primary goals of deploying DVWA were:

* Simulate vulnerable web infrastructure
* Generate web attack telemetry
* Practice web enumeration techniques
* Monitor attacker activity through Wazuh
* Analyze Apache access logs
* Create detection engineering workflows

---

## Lab Environment

| Component  | Role                       |
| ---------- | -------------------------- |
| DVWA       | Vulnerable Web Application |
| Apache2    | Web Server                 |
| Kali Linux | Attacker Machine           |
| Wazuh OVA  | SIEM Monitoring            |
| VirtualBox | Virtualization Platform    |

---

## Deployment Method

DVWA was deployed on a Linux-based virtual machine inside the isolated VirtualBox lab network.

The web application was configured to communicate internally with:

* Kali Linux attacker VM
* Wazuh SIEM instance
* Windows monitoring endpoint

---

## Apache Installation

Apache web server was installed using:

```bash
sudo apt install apache2 -y
```

Apache service verification:

```bash
sudo systemctl status apache2
```

---

## PHP Installation

DVWA requires PHP and supporting modules.

Installed packages:

```bash
sudo apt install php php-mysqli php-gd libapache2-mod-php mariadb-server -y
```

---

## DVWA Download

DVWA repository cloned using:

```bash
git clone https://github.com/digininja/DVWA.git
```

Application moved into Apache web root:

```bash
sudo mv DVWA /var/www/html/
```

---

## Permissions Configuration

Permissions adjusted for Apache:

```bash
sudo chown -R www-data:www-data /var/www/html/DVWA
sudo chmod -R 755 /var/www/html/DVWA
```

---

## Database Setup

MariaDB service started:

```bash
sudo systemctl start mariadb
```

Database initialized through DVWA setup page.

---

## Network Configuration

DVWA operated inside the internal lab network.

Example addressing:

| System      | IP Address  |
| ----------- | ----------- |
| Kali Linux  | 192.168.56.103  |
| DVWA Server |  192.168.56.102 |
| Wazuh OVA   |  192.168.56.104 |
| Windows 10  |  192.168.56.101 |
---

## Access Verification

DVWA successfully accessed from Kali Linux using:

```bash
http://192.168.56.102/DVWA
```

---

## Attack Simulation Usage

DVWA was used to generate:

* Web enumeration traffic
* Nikto scan activity
* HTTP requests
* Apache access logs
* SIEM alerts

---

## Log Monitoring

Apache logs monitored by Wazuh:

```bash
/var/log/apache2/access.log
```

This enabled:

* attacker IP visibility
* suspicious request detection
* alert correlation
* web attack investigations

---

## Security Relevance

DVWA provides a controlled environment for:

* web attack simulation
* SIEM visibility testing
* detection engineering
* incident investigation practice

The platform was intentionally isolated from external networks to prevent unintended exposure.

---

## Screenshots

Add screenshots here:

 <img width="1920" height="776" alt="Screenshot (680)" src="https://github.com/user-attachments/assets/ee1c5d3b-061a-428a-979a-9241c7e6c8d0" />

 <img width="1920" height="820" alt="Screenshot (681)" src="https://github.com/user-attachments/assets/505e38a5-9e12-4e88-954b-3bc8d91508b3" />


---

## Learning Outcomes

* Web application deployment
* Apache administration
* Internal network configuration
* Web attack telemetry generation
* SIEM monitoring workflows
* Detection engineering fundamentals
