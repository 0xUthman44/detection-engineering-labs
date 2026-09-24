# Windows Endpoint Activity Investigation with Sysmon

## Scenario

As part of routine security monitoring within a simulated enterprise environment, Wazuh generated several Sysmon Process Creation (Event ID 1) alerts from a monitored Windows 10 workstation.

The endpoint began executing multiple command-line utilities, including PowerShell, Windows discovery commands, and local account management utilities. Since these techniques are commonly observed during post-compromise reconnaissance, the activity required investigation to determine whether it represented legitimate administrative behavior or malicious activity.

The objective of this investigation was to reconstruct the sequence of events, analyze process execution, examine parent-child process relationships, correlate Windows Security events, and determine whether the endpoint had been compromised.

---

## Investigation Objectives

* Validate Sysmon logging
* Investigate Sysmon Event ID 1 process creation events
* Analyze command-line activity
* Identify suspicious PowerShell execution
* Correlate Windows Security Events
* Determine whether malicious activity occurred
* Produce an analyst assessment

---

## Lab Environment

| Component     | Role                | IP Address      |
| ------------- | ------------------- | --------------  |
| Windows 10    | Monitored Endpoint  | 192.168.56.101  |
| Wazuh OVA     | SIEM Platform       | 192.168.56.104  |
| Kali Linux    | SOC Lab Workstation | 192.168.56.103  |

---

## Environment Architecture

```text
Windows 10 Endpoint
        │
        ▼
Sysmon
        │
        ▼
Wazuh Agent
        │
        ▼
Wazuh Manager
        │
        ▼
SOC Investigation
```

---

## Detection

Routine monitoring within Wazuh identified multiple Sysmon Process Creation events originating from the monitored Windows endpoint.

Threat hunting focused on:

* Sysmon Event ID 1
* PowerShell execution
* Command-line arguments
* Parent-child process relationships
* Windows Security Events

Searches performed included:

```text
event.provider:Sysmon
```

and

```text
win.system.providerName:Microsoft-Windows-Sysmon
```
<img width="1920" height="580" alt="Screenshot (747)" src="https://github.com/user-attachments/assets/355b3ae0-d0ee-4885-8e67-2be9167b163f" />

The investigation centered on **Sysmon Event ID 1**, which records every process created on the endpoint.

---

## Initial Observations

Initial review of Sysmon telemetry identified several legitimate Windows utilities executed within a short time period.

Observed commands included:

```powershell
whoami
hostname
ipconfig
systeminfo
```
<img width="1920" height="849" alt="Screenshot (748)" src="https://github.com/user-attachments/assets/a9c30ecd-f6b4-4896-a081-b0b4f4c62eee" />

These commands are commonly used by system administrators but are also frequently executed by attackers immediately after gaining access to a system to collect host information.

---

## Endpoint Discovery Activity

Further investigation revealed additional discovery commands executed from the same user session.

Observed commands included:

```powershell
net user
net localgroup administrators
tasklist
whoami /priv
wmic process list brief
```
<img width="1920" height="840" alt="Screenshot (749)" src="https://github.com/user-attachments/assets/262da263-f135-4dad-84fa-2bfdb003f9e1" />

These commands are commonly associated with attacker reconnaissance following initial access.

The investigation focused on determining whether these commands were executed by an authorized user or as part of malicious activity.
<img width="1920" height="234" alt="Screenshot (750)" src="https://github.com/user-attachments/assets/10693932-4413-4cbd-bc2a-02eb43e2b404" />

---

## PowerShell Investigation

Additional PowerShell activity was observed during the investigation.

Executed commands included:

```powershell
Get-Process
Get-Service
Get-ChildItem C:\Users
```
<img width="1920" height="350" alt="Screenshot (751)" src="https://github.com/user-attachments/assets/11108ece-d427-485c-87d7-a7041e1e68cb" />

-----
<img width="1920" height="661" alt="Screenshot (752)" src="https://github.com/user-attachments/assets/c29149ef-9d8b-4b2e-bb17-514c0fe47ac8" />

----
<img width="1920" height="216" alt="Screenshot (753)" src="https://github.com/user-attachments/assets/2bb196ef-1719-4d85-8698-5827b9278735" />


These generated additional Sysmon process creation events that were successfully ingested into Wazuh.

---

## Encoded PowerShell Execution

One process immediately stood out during the investigation.

The following encoded PowerShell command was executed:

```powershell
powershell -enc SQBlAGwAbABvAA==
```
<img width="1920" height="146" alt="Screenshot (754)" src="https://github.com/user-attachments/assets/270cb702-4ad9-481d-b1a0-fdaadc244c93" />

Although the payload itself was harmless, Base64-encoded PowerShell commands are widely used by malware, penetration testing frameworks, and threat actors to conceal malicious commands.

The investigation focused on determining:

* Which user executed the command
* Which parent process launched PowerShell
* Whether additional child processes were spawned
* Whether persistence or network activity followed execution

No additional malicious behavior was identified.
<img width="1920" height="174" alt="Screenshot (756)" src="https://github.com/user-attachments/assets/6d6791d8-a710-4514-9e3c-b920ddec02f8" />

---

## Process Tree Analysis

Sysmon telemetry allowed reconstruction of the execution chain.

```text
explorer.exe
        │
        └── powershell.exe
                ├── whoami.exe
                ├── tasklist.exe
                ├── systeminfo.exe
                ├── net.exe
                └── wmic.exe
```

The process hierarchy showed PowerShell being launched from **explorer.exe**, indicating an interactive user session.

No abnormal parent-child relationships or suspicious execution chains were identified.

---

## Account Management Activity

To generate additional Windows Security telemetry, a temporary local account was created.

```powershell
net user SOCLabUser Password123! /add
```

Group membership was verified.

```powershell
net localgroup Users
```

The account was subsequently removed.

```powershell
net user SOCLabUser /delete
```

This activity generated Windows Security events that were successfully correlated with Sysmon telemetry.

---

## Event Correlation

During the investigation, Sysmon events were correlated with Windows Security logs.

| Event ID | Description             |
| -------- | ----------------------- |
| 1        | Sysmon Process Creation |
| 4624     | Successful Logon        |
| 4625     | Failed Logon            |
| 4688     | Process Creation        |
| 4720     | User Account Created    |
| 4726     | User Account Deleted    |

Correlating multiple log sources provided a more complete picture of endpoint activity and improved investigation accuracy.

---

## Timeline of Activity

| Time  | Activity                    |
| ----- | --------------------------- |
| 18:52 | PowerShell launched         |
| 18:52 | whoami executed             |
| 18:56 | systeminfo executed         |
| 19:06 | tasklist executed           |
| 19:53 | net user executed           |
| 20:38 | Encoded PowerShell executed |
| 20:39 | Temporary account created   |
| 21:05 | Temporary account deleted   |

---

## Indicators Observed

| Indicator               | Observation                        |
| ----------------------- | ---------------------------------- |
| Endpoint                | Windows 10                         |
| Primary Log Source      | Sysmon                             |
| Primary Event           | Event ID 1                         |
| Discovery Commands      | whoami, systeminfo, tasklist, wmic |
| Administrative Commands | net user                           |
| Suspicious Activity     | Encoded PowerShell                 |
| Parent Process          | explorer.exe                       |
| Severity                | Low (Controlled Lab Simulation)    |

---

## MITRE ATT&CK Mapping

| Tactic    | Technique                            |
| --------- | ------------------------------------ |
| Discovery | T1033 – System Owner/User Discovery  |
| Discovery | T1082 – System Information Discovery |
| Discovery | T1057 – Process Discovery            |
| Discovery | T1087 – Account Discovery            |
| Execution | T1059.001 – PowerShell               |

---

## Analyst Assessment

Based on the available evidence:

* All observed commands were executed by an authorized local user.
* Process execution originated from **explorer.exe**, indicating an interactive desktop session.
* Sysmon successfully recorded every process creation event.
* Wazuh successfully ingested and indexed the endpoint telemetry.
* The encoded PowerShell command was logged and available for investigation.
* No persistence mechanisms, privilege escalation, credential theft, lateral movement, or unauthorized access were identified.

The activity was determined to be **simulated administrative behavior performed to validate endpoint monitoring and detection capabilities**.

---

## Recommendations

Following the investigation, the following defensive measures are recommended:

* Continue monitoring Sysmon Event ID 1 for unusual process execution.
* Generate alerts for Base64-encoded PowerShell commands.
* Investigate unexpected parent-child process relationships.
* Correlate Sysmon events with Windows Security logs during endpoint investigations.
* Monitor local account creation and deletion events.
* Review command-line arguments for administrative utilities to distinguish legitimate administration from attacker reconnaissance.

---

## Learning Outcomes

This investigation strengthened practical skills in:

* Windows endpoint investigations
* Sysmon process creation analysis
* Command-line forensic analysis
* Parent-child process investigation
* Windows Security event correlation
* PowerShell threat hunting
* Wazuh endpoint monitoring
* MITRE ATT&CK mapping
* SOC investigation workflows

This project demonstrates how endpoint telemetry collected by Sysmon and Wazuh can be leveraged to reconstruct user activity, identify potentially suspicious behavior, and differentiate legitimate administrative actions from techniques commonly used by attackers during post-compromise operations.
