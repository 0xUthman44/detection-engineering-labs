# Directory Enumeration with Gobuster

## Overview

Gobuster was used within the SOC homelab environment to perform directory enumeration against the DVWA web server. Directory enumeration is a reconnaissance technique commonly used by attackers to discover hidden directories, files, administrative panels, backup files, and other sensitive resources that are not directly linked from the web application.

This exercise generated HTTP traffic that was captured by Apache access logs and ingested into Wazuh SIEM for analysis, allowing reconnaissance activity to be investigated from a defender's perspective.

---

## Objective

The primary objectives of this exercise were to:

* Verify connectivity to the DVWA web server
* Perform directory enumeration using Gobuster
* Generate realistic web reconnaissance telemetry
* Monitor Apache access logs
* Investigate reconnaissance activity within Wazuh
* Understand how directory enumeration appears from a SOC analyst's perspective

---

## Lab Environment

| Component   | Role                  | IP Address     |
| ----------- | --------------------- | -------------- |
| Kali Linux  | Attacker Workstation  | 192.168.56.103 |
| Ubuntu DVWA | Vulnerable Web Server | 192.168.56.102 |
| Wazuh OVA   | SIEM Platform         | 192.168.56.104 |
| Windows 10  | Monitored Endpoint    | 192.168.56.101 |

---

## Attack Flow

```text
Kali Linux
      │
      ▼
Gobuster Directory Enumeration
      │
      ▼
Ubuntu DVWA Web Server
      │
      ├────────► Apache Access Logs
      │
      ▼
Wazuh SIEM
```

---

## Step 1 – Verify DVWA Connectivity

Before beginning the enumeration, connectivity to the DVWA web server was verified from the Kali Linux VM.

```bash
curl http://192.168.56.102:8080
```
 <img width="1920" height="293" alt="Screenshot (713)" src="https://github.com/user-attachments/assets/87d820a4-5f2f-4616-b9df-39ac309a2d8d" />

A successful HTML response confirmed that the web application was reachable.


---

## Step 2 – Install Gobuster

Gobuster was installed on the Kali Linux VM using:

```bash
sudo apt update
sudo apt install gobuster -y
```

Installation was verified with:

```bash
gobuster --help
```
<img width="1920" height="440" alt="Screenshot (714)" src="https://github.com/user-attachments/assets/a96053e9-a33a-4e41-92ec-c75702e1967a" />

---

## Step 3 – Locate the Wordlist

Gobuster requires a wordlist to perform directory enumeration.

The default Dirb wordlist included with Kali Linux was used.

Location:

```text
/usr/share/wordlists/dirb/common.txt
```

Verification:

```bash
ls /usr/share/wordlists/dirb/
```
<img width="1920" height="146" alt="Screenshot (715)" src="https://github.com/user-attachments/assets/55c5363b-d6cb-466b-b6ba-d7da7e0a1aa1" />

---

## Step 4 – Perform Directory Enumeration

The enumeration scan was executed using:

```bash
gobuster dir -u http://192.168.56.102:8080 -w /usr/share/wordlists/dirb/common.txt
```
<img width="1920" height="378" alt="Screenshot (716)" src="https://github.com/user-attachments/assets/b3fe017f-851f-4993-80b6-b02e1623a39f" />

Gobuster rapidly requested hundreds of common directory names against the target web server to identify accessible resources.

---

## Scan Results

The scan successfully discovered several interesting resources, including:

```text
/.htaccess
/.htpasswd
/server-status
```

Although access to these resources was restricted, their discovery provided valuable reconnaissance information that could assist an attacker during later stages of an intrusion.

---

## Apache Log Analysis

To observe the generated traffic, Apache access logs were monitored in real time.

```bash
sudo tail -f /var/log/apache2/access.log
```
<img width="1920" height="828" alt="Screenshot (717)" src="https://github.com/user-attachments/assets/d518ad59-fdd2-44cc-82c7-374ca23b1bf4" />

The logs showed numerous requests originating from the Kali Linux attacker IP.

Example requests included:

```text
192.168.56.103 GET /.htaccess
192.168.56.103 GET /.htpasswd
192.168.56.103 GET /server-status
```

During analysis, the following characteristics were observed:

* High request volume
* Rapid sequential HTTP requests
* Multiple HTTP 403 responses
* Multiple HTTP 404 responses
* Automated scanning behaviour

These characteristics are consistent with directory brute-forcing and reconnaissance activity.

---

## Wazuh Investigation

The generated telemetry was investigated within the Wazuh Dashboard.

Navigate to:

* Threat Hunting

Search using:

* Kali Linux source IP
* `server-status`
* `.htaccess`
* `404`
* `403`
<img width="1920" height="825" alt="Screenshot (718)" src="https://github.com/user-attachments/assets/c383e61d-e1af-4c81-be43-4f5829d0f5c2" />


<img width="1920" height="832" alt="Screenshot (719)" src="https://github.com/user-attachments/assets/29c9a4dd-5b1b-4207-8cfc-ba084b3d5b3d" />


This investigation helped identify:

* Source IP address
* Requested resources
* HTTP response codes
* Enumeration timeline
* Automated scanning behaviour

---

## Detection Indicators

The following indicators were identified during the investigation.

| Indicator      | Observation                   |
| -------------- | ----------------------------- |
| Source IP      | Kali Linux VM                 |
| Destination    | Ubuntu DVWA                   |
| Activity       | Directory Enumeration         |
| Tool           | Gobuster                      |
| HTTP Responses | 403 Forbidden / 404 Not Found |
| Behaviour      | Automated Reconnaissance      |
| Severity       | Medium                        |

---

## SOC Analyst Investigation

## Incident Summary

A host performed automated directory enumeration against the DVWA web server using Gobuster.

The activity generated numerous HTTP requests targeting common administrative and sensitive directories. Multiple **403 Forbidden** and **404 Not Found** responses were observed, indicating attempts to discover hidden web resources.

Although no exploitation occurred, the activity represents the reconnaissance phase of the cyber attack lifecycle and may precede exploitation attempts.

---

## MITRE ATT&CK Mapping

| Tactic         | Technique                            |
| -------------- | ------------------------------------ |
| Reconnaissance | T1595 – Active Scanning              |
| Discovery      | T1083 – File and Directory Discovery |

---

## Recommendations

Following the investigation, the following defensive measures are recommended:

* Monitor for repeated HTTP requests from a single source IP.
* Investigate high volumes of HTTP 403 and 404 responses.
* Restrict access to sensitive administrative resources.
* Implement rate limiting to reduce automated scanning.
* Deploy a Web Application Firewall (WAF).
* Correlate Apache access logs with Wazuh alerts to improve detection of reconnaissance activity.

---

## Issues Encountered

During this exercise, the following issues were encountered and resolved.

| Issue                      | Resolution                                                                                |
| -------------------------- | ----------------------------------------------------------------------------------------- |
| Incorrect target address   | Corrected the target IP address before rerunning the scan.                                |
| Wordlist verification      | Confirmed that the default Dirb wordlist existed before starting the scan.                |

---

## Learning Outcomes

Through this exercise, I gained practical experience with:

* Web application reconnaissance
* Directory enumeration using Gobuster
* Apache access log analysis
* Threat hunting within Wazuh
* Identifying reconnaissance indicators
* Investigating automated scanning activity
* Mapping reconnaissance techniques to the MITRE ATT&CK framework

This exercise demonstrated how directory enumeration appears from a defender's perspective and reinforced the importance of monitoring reconnaissance activity as an early indicator of potential compromise.
