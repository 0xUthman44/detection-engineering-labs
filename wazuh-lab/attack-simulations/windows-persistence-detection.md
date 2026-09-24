# Windows Persistence Mechanisms Investigation

## Scenario

During routine endpoint monitoring, Wazuh generated multiple alerts indicating persistence-related activity on a monitored Windows 10 workstation.

The observed activity included Windows Registry modifications, scheduled task creation, Windows service installation, and changes associated with user logon persistence. These techniques are commonly used by threat actors to maintain access to compromised systems following initial access and are frequently observed during endpoint investigations.

Given the nature of the observed activity, an investigation was initiated to determine whether the persistence mechanisms represented authorized administrative activity or potential indicators of compromise. The investigation focused on reconstructing the sequence of events using endpoint telemetry collected by Sysmon and Wazuh, identifying the persistence techniques employed, and assessing the overall security impact to the affected endpoint.

---

## Investigation Objectives

The objectives of this investigation were to:

- Investigate persistence-related activity observed on the Windows endpoint.
- Identify Registry Run Key modifications associated with user logon persistence.
- Detect the creation and modification of scheduled tasks.
- Investigate Windows service creation events.
- Examine Startup Folder persistence mechanisms.
- Correlate Sysmon process creation and registry events to reconstruct the sequence of activity.
- Map observed techniques to the MITRE ATT&CK framework.
- Produce a comprehensive analyst assessment based on the collected evidence.

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

Routine monitoring within Wazuh identified multiple events associated with common Windows persistence techniques originating from the monitored endpoint.

Initial review of the telemetry revealed the execution of administrative utilities capable of modifying the Windows Registry, creating scheduled tasks, and installing Windows services. Because these actions closely resemble persistence mechanisms commonly used by attackers, the endpoint was prioritized for further investigation.

Threat hunting activities focused on identifying:

- Registry modifications
- Scheduled Task creation
- Windows Service creation
- Startup Folder persistence
- Parent-child process relationships
- Process execution history

The initial investigation began by reviewing Sysmon telemetry using the following query:

```text
event.provider:Sysmon
```

Process-specific investigations were then performed using the following searches:

```text
process.name:reg.exe
```

```text
process.name:schtasks.exe
```

```text
process.name:sc.exe
```

Sysmon Process Creation events, registry telemetry, command-line arguments, and parent-child process relationships formed the primary sources of evidence throughout the investigation.

---

## Initial System Verification

Before analyzing the persistence-related events, the health of the monitored endpoint and its telemetry pipeline was verified to ensure that endpoint logs were being collected successfully.

The following commands were executed to confirm the identity of the endpoint, validate normal system operation, and verify that the Wazuh agent service was running.

```powershell
whoami
hostname
Get-Service WazuhSvc
```

The endpoint returned the expected system information, and the Wazuh agent was confirmed to be running. Review of the Wazuh dashboard verified that Sysmon telemetry was being successfully collected and indexed, providing confidence that subsequent investigative findings were based on complete and reliable endpoint data.
<img width="1920" height="144" alt="Screenshot (759)" src="https://github.com/user-attachments/assets/737e5228-f448-4cdd-b8b3-6ba992debadc" />
*Figure : Verification that the Wazuh Agent service is running on the monitored endpoint.*
## Registry Run Key Investigation

Analysis of the persistence-related alerts identified a modification to the Windows **Run** registry key, indicating that an application had been configured to execute automatically during user logon.

Registry Run Keys are a well-known persistence mechanism frequently abused by threat actors to maintain access to compromised systems. By creating or modifying values within this registry location, malware can achieve automatic execution whenever a user authenticates to the operating system.

Review of the associated Sysmon telemetry confirmed that **reg.exe** was used to create a new registry value named **SOCLab**, configured to launch **notepad.exe** at user logon.

The following command was identified during the investigation:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v SOCLab /t REG_SZ /d "notepad.exe"
```

To validate the modification, the contents of the Run registry key were examined using:

```cmd
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Run"
```

The investigation focused on validating the following evidence:

- Registry path that was modified
- Registry value that was created
- Executing process responsible for the modification
- Parent process associated with the activity
- User account that performed the action
- Hostname of the affected endpoint
- Event timestamp
- Command-line arguments recorded by Sysmon

Review of the collected telemetry confirmed that the registry modification originated from an interactive administrative session and was successfully captured by Sysmon Process Creation events. The associated command-line arguments provided clear visibility into the persistence mechanism that had been established, enabling the activity to be reconstructed from the available endpoint evidence.
  <img width="1920" height="146" alt="Screenshot (760)" src="https://github.com/user-attachments/assets/34398953-bcef-407f-b6fe-df7a583091a6" />
  *Figure : Verification of the newly created Registry Run Key used to simulate persistence.*
  <img width="1494" height="275" alt="Screenshot (761)" src="https://github.com/user-attachments/assets/44de35a1-38d0-4fb4-b009-da19fc1f054a" />
*Figure : Sysmon Event ID 13 capturing the modification of the Windows Run registry key, including the registry path, value name, executing process, user account, and timestamp.*

Following confirmation of the registry modification, the associated persistence mechanism was removed to restore the endpoint to its original state.

The following command was executed to delete the Registry Run Key value:

```cmd
reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v SOCLab /f
```

Following the removal, additional threat hunting was performed within Wazuh to verify that the deletion event had been successfully recorded and that no unauthorized Registry Run Key entries remained on the endpoint.

Analysis of the corresponding Sysmon registry events confirmed that the registry value was successfully removed. Review of the surrounding telemetry did not identify any additional registry modifications or persistence mechanisms requiring further investigation, indicating that the endpoint had been returned to its expected configuration.
<img width="1920" height="146" alt="Screenshot (762)" src="https://github.com/user-attachments/assets/14cf98be-86e7-49b3-97ea-f48909268ef8" />
## Scheduled Task Investigation

Analysis of the endpoint telemetry identified the creation of a scheduled task configured to execute automatically during user logon.

Scheduled Tasks are a widely recognized persistence mechanism frequently used by threat actors to maintain access following initial compromise. By configuring a task to execute at logon or on a defined schedule, attackers can ensure malicious payloads are launched without requiring further user interaction.

Review of the associated Sysmon Process Creation events confirmed that **schtasks.exe** was used to create a scheduled task named **SOCLabTask**, configured to launch **notepad.exe** whenever a user authenticated to the system.

The following command was identified during the investigation:

```cmd
schtasks /create /tn "SOCLabTask" /tr "notepad.exe" /sc onlogon
```

To verify the persistence mechanism, the scheduled task configuration was reviewed using:

```cmd
schtasks /query | findstr SOCLabTask
```

The investigation focused on validating the following evidence:

- Task name
- Configured executable
- Executing user account
- Parent process
- Command-line arguments
- Hostname
- Event timestamp
- Associated Sysmon Event ID

Analysis of the collected telemetry confirmed that the scheduled task was successfully created and that the activity was fully captured by Sysmon and ingested into Wazuh. Examination of the process metadata and command-line arguments provided sufficient context to reconstruct the persistence activity and verify the configuration of the scheduled task.
<img width="1920" height="147" alt="Screenshot (764)" src="https://github.com/user-attachments/assets/244fb7f9-478c-44e7-a6f5-71fd117b3ed1" />
<img width="1920" height="144" alt="Screenshot (766)" src="https://github.com/user-attachments/assets/72a9180f-e3a1-4c88-a501-4cf4d7ea91b8" />
*Figure : Wazuh Threat Hunting results showing the Sysmon Process Creation event generated by **schtasks.exe**, including the command line used to create the scheduled task, the executing user account, parent process, and associated event metadata.*

Following confirmation of the scheduled task creation, the persistence mechanism was removed to return the endpoint to its expected configuration.

The following command was executed to delete the scheduled task:

```cmd
schtasks /delete /tn "SOCLabTask" /f
```

Following removal, additional threat hunting was conducted within Wazuh to verify that the deletion activity had been successfully recorded and that the scheduled task no longer existed on the endpoint.

Analysis of the corresponding Sysmon telemetry confirmed that the scheduled task was successfully removed. A review of subsequent process creation events and scheduled task activity did not identify any additional unauthorized tasks or persistence mechanisms requiring further investigation.

Based on the available evidence, the scheduled task persistence mechanism was successfully removed, and no residual scheduled task persistence was identified on the monitored endpoint.
<img width="1920" height="150" alt="Screenshot (767)" src="https://github.com/user-attachments/assets/6c2e9db5-165f-4b2c-bc2b-39e542a6aa0e" />
## Windows Service Investigation

Further analysis of the endpoint telemetry identified the creation of a new Windows service on the monitored endpoint.

Windows services are a common persistence mechanism used by threat actors because they can be configured to start automatically during system boot and often execute with elevated privileges. As a result, unexpected service creation events warrant immediate investigation due to their potential to provide long-term unauthorized access.

Review of the associated Sysmon Process Creation events confirmed that **sc.exe** was used to create a Windows service named **SOCService**, configured to execute **notepad.exe**.

The following command was identified during the investigation:

```cmd
sc create SOCService binPath= "C:\Windows\System32\notepad.exe"
```

To validate the configuration, the service status was reviewed using:

```cmd
sc query SOCService
```

The investigation focused on validating the following evidence:

- Service name
- Executable path
- Executing process
- Parent process
- User account
- Command-line arguments
- Hostname
- Event timestamp
- Associated Sysmon Event ID

Analysis of the collected telemetry confirmed that the Windows service was successfully created and that the activity was fully captured by Sysmon and ingested into Wazuh. Examination of the process metadata and command-line arguments provided sufficient context to reconstruct the service creation activity and determine how the persistence mechanism had been established.

---

### Screenshot  – Windows Service Creation

The figure below shows the Sysmon Process Creation event associated with the creation of the Windows service. The event captures the execution of **sc.exe**, including the full command line, service name, executable path, executing user account, parent process, hostname, timestamp, and associated event metadata.


<img width="1920" height="157" alt="Screenshot (769)" src="https://github.com/user-attachments/assets/44f80097-2607-48b9-a307-e39ebd334b23" />

---------------------------------------------------------------
<img width="1920" height="147" alt="Screenshot (770)" src="https://github.com/user-attachments/assets/f44c9cae-5f56-4cbc-925f-c3da9a0300e8" />
*Figure : Sysmon process creation event showing execution of sc.exe to create a Windows service.*

Windows services are a well-established persistence mechanism frequently abused by threat actors because they can be configured to start automatically during system boot and often execute with elevated privileges. Consequently, the creation of unexpected services should always be treated as a high-value indicator during endpoint investigations.

Following confirmation of the service creation, the persistence mechanism was removed to restore the endpoint to its expected configuration.

The following command was executed to delete the service:

```cmd
sc delete SOCService
```

Following removal, additional analysis was performed to verify that the service had been successfully removed and that no residual persistence remained.

Review of the corresponding Sysmon Process Creation events confirmed execution of the service deletion command. The service status was subsequently verified, and no evidence of the **SOCService** remained on the endpoint.

Further review of endpoint telemetry did not identify any additional unauthorized service creation events or related persistence mechanisms. Based on the available evidence, the service was successfully removed, and no indicators of continued service-based persistence were identified.

---


The figure below shows the Sysmon Process Creation event associated with the removal of the Windows service, including the execution of **sc.exe**, the full command line used during deletion, the executing user account, parent process, hostname, timestamp, and associated event metadata.

> **Figure :** Wazuh Threat Hunting results showing the removal of the **SOCService** Windows service and confirming successful remediation of the persistence mechanism.
<img width="1920" height="162" alt="Screenshot (771)" src="https://github.com/user-attachments/assets/2d236986-0f89-4484-bb4e-87fbcd214110" />

## Startup Folder Persistence Investigation

As part of the investigation, additional analysis was conducted to determine whether the endpoint had been configured to establish persistence through the Windows Startup folder.

The Windows Startup folder is a well-known persistence location that enables applications to execute automatically whenever a user signs in. Because this mechanism requires minimal user interaction and can be implemented without modifying core operating system components, it is frequently abused by malware and other unauthorized software to maintain access to compromised systems.

Review of the endpoint activity identified the creation of a shortcut configured to launch **notepad.exe** from the user's Startup folder.

The investigation focused on validating the following evidence:

- Startup folder location
- Shortcut target
- Executing user account
- Parent process
- File creation activity
- Event timestamp
- Associated Sysmon telemetry

Where supported by the deployed Sysmon configuration, **File Create (Event ID 11)** telemetry was reviewed to determine whether the creation of the Startup folder shortcut had been successfully recorded.

Analysis of the available endpoint telemetry confirmed the creation of the Startup folder entry. No additional unauthorized files or persistence mechanisms were identified within the Startup directory during the course of the investigation.

---

### Screenshot  – Startup Folder Persistence

The figure below shows the Startup folder containing the shortcut configured to execute automatically during user logon. Where available, the corresponding Sysmon File Create (Event ID 11) telemetry was also reviewed to validate the creation of the persistence artifact.

> **Figure :** Startup folder persistence mechanism identified during the investigation, showing the shortcut configured to launch automatically at user logon.

<img width="1920" height="316" alt="Screenshot (772)" src="https://github.com/user-attachments/assets/4d4a8488-086d-47b1-bf2f-fe4322b65ada" />

## Process Tree Analysis

One of the key objectives of the investigation was to reconstruct the process execution chain that resulted in the Registry Run Key modification.

Sysmon Process Creation events provided visibility into the parent-child relationship between the processes involved, allowing the execution sequence to be reconstructed.

The observed process hierarchy was:

```text
explorer.exe
    └── powershell.exe
            └── reg.exe
```

The process tree indicates that **reg.exe** was launched from an interactive **PowerShell** session, which itself was initiated by **explorer.exe**. This parent-child relationship is consistent with activity originating from a user logged into the Windows desktop rather than from a background service, scheduled task, or remote execution framework.

Review of the associated Sysmon telemetry confirmed the complete execution chain, including the parent process, child process, command-line arguments, executing user account, hostname, and timestamp. This context enabled the Registry Run Key modification to be directly associated with the observed persistence activity.

From an investigative perspective, process tree analysis is critical because it helps distinguish legitimate administrative activity from malicious execution chains. Unexpected parent-child relationships—such as **winword.exe** spawning **powershell.exe**, or **powershell.exe** launching **cmd.exe**, **reg.exe**, or **rundll32.exe** without a clear administrative purpose—often warrant additional investigation.

In this case, the reconstructed process hierarchy provided sufficient context to attribute the Registry Run Key modification to an interactive user session while confirming that the persistence mechanism had been successfully captured by Sysmon and correlated within Wazuh.

---
<img width="1920" height="233" alt="Screenshot (775)" src="https://github.com/user-attachments/assets/b23ae099-98cb-4aa5-a42e-63423b28474e" />


## Threat Hunting

Following the identification of persistence-related activity, a targeted threat hunting exercise was conducted within the Wazuh dashboard to determine the process responsible for the observed registry modification and to identify any additional indicators of persistence across the monitored endpoint.

The investigation focused on **reg.exe**, as this Windows utility is commonly used to create, modify, and delete Registry keys and values. Registry modifications are considered high-value investigative artifacts because adversaries frequently abuse Run Keys to establish persistence, enabling malicious code to execute automatically whenever a user logs on.

The following query was executed within the Wazuh Threat Hunting module:

```text
process.name:reg.exe
```

The resulting Sysmon Process Creation events were reviewed to identify the following attributes:

- Process Name
- Parent Process
- Full Command Line
- Executing User Account
- Hostname
- Event Timestamp
- Event ID
- Detection Rule

Analysis of the returned telemetry confirmed that **reg.exe** was responsible for modifying the Windows Run registry key. Review of the associated process metadata showed that the command originated from an interactive **PowerShell** session launched by **explorer.exe**, allowing the complete execution chain to be reconstructed.

The recorded command-line arguments matched the Registry Run Key modification observed during the investigation, providing clear visibility into how the persistence mechanism was established.

To determine whether the activity formed part of a broader compromise, surrounding Sysmon events were also reviewed for additional Registry modifications, scheduled task creation, service creation events, and other common persistence techniques.

No additional indicators of persistence, privilege escalation, lateral movement, or post-compromise activity were identified during the review. Based on the available endpoint telemetry, the observed Registry Run Key modification represented an isolated event, and no evidence of further unauthorized persistence mechanisms was found on the monitored endpoint.

---

### Screenshot – Threat Hunting Results

The figure below shows the results of the **process.name:reg.exe** threat hunting query within Wazuh. The returned Sysmon Process Creation event captures the execution of **reg.exe**, including the full command line, parent process, executing user account, hostname, timestamp, and associated detection rule.

> **Figure:** Wazuh Threat Hunting results identifying **reg.exe** as the process responsible for the Registry Run Key modification and confirming the associated execution chain through Sysmon telemetry.
<img width="1920" height="823" alt="Screenshot (773)" src="https://github.com/user-attachments/assets/6a55b49d-732b-4fb4-aa02-bc5d9ce7314d" />
<img width="1920" height="817" alt="Screenshot (774)" src="https://github.com/user-attachments/assets/ec5e7ebb-3445-4524-9758-897e7a3e8fd6" />


## MITRE ATT&CK Mapping

Analysis of the endpoint telemetry identified multiple persistence techniques consistent with behaviors documented in the MITRE ATT&CK framework. Mapping the observed activity to MITRE ATT&CK provides standardized context for understanding how adversaries establish persistence, maintain access, and survive system reboots or user logoff events.

| MITRE ATT&CK Technique | Description                                                                           |
|------------------------|---------------------------------------------------------------------------------------|
| **T1547.001 – Registry Run Keys / Startup Folder** | Modification of the Windows Run registry key to configure an application for automatic execution during user logon. |
| **T1053.005 – Scheduled Task** | Creation of a scheduled task configured to execute automatically following user authentication. |
| **T1543.003 – Windows Service** | Creation of a Windows service capable of executing during system startup, providing a common mechanism for long-term persistence. |

The identified techniques closely align with persistence behaviors frequently observed during endpoint investigations involving Windows-based environments. Correlating these techniques with Sysmon telemetry and Wazuh detections enabled the activity to be classified accurately while providing valuable context for the overall investigation.

---

## Analyst Assessment

The investigation confirmed the execution of multiple persistence mechanisms on the monitored Windows endpoint, including Registry Run Key modification, scheduled task creation, Windows service creation, and Startup folder persistence.

Review of the collected Sysmon Process Creation events, registry telemetry, command-line arguments, and parent-child process relationships provided sufficient evidence to reconstruct the complete sequence of events. All persistence-related activity was successfully captured by Sysmon and ingested into Wazuh, allowing each event to be correlated throughout the investigation.

Analysis of the execution chain showed that the observed activity originated from an interactive user session initiated through **explorer.exe**, with subsequent execution of **PowerShell**, **reg.exe**, **schtasks.exe**, and **sc.exe**. The corresponding telemetry contained the necessary process metadata, command-line arguments, executing user account, hostname, and timestamps required to support the investigation.

A comprehensive review of surrounding endpoint telemetry was conducted to identify indicators commonly associated with post-compromise activity, including privilege escalation, credential access, lateral movement, additional persistence mechanisms, and unauthorized service creation. No evidence was identified to suggest that the observed persistence activity formed part of a broader compromise.

Based on the available evidence, the endpoint telemetry provided complete visibility into the observed persistence techniques, enabling the activity to be accurately reconstructed, investigated, and assessed without identifying additional indicators of compromise.

---

## Recommendations

Based on the findings of this investigation, the following defensive measures are recommended to improve endpoint visibility and strengthen the organization's ability to detect persistence-related activity:

- Continuously monitor Windows Registry Run Keys for unauthorized modifications.
- Generate high-severity alerts for newly created scheduled tasks, particularly those configured to execute during user logon or system startup.
- Monitor the creation of new Windows services and investigate unexpected service installations.
- Alert on execution of administrative utilities such as **reg.exe**, **schtasks.exe**, and **sc.exe** when used to modify persistence-related system components.
- Review Sysmon Process Creation events for suspicious command-line arguments and anomalous parent-child process relationships.
- Correlate Sysmon telemetry with Windows Security events to improve investigative context and support incident response activities.
- Conduct proactive threat hunting focused on persistence techniques mapped to the MITRE ATT&CK framework.
- Regularly validate Sysmon configuration to ensure process creation, registry modification, and file creation events continue to be collected and retained for forensic analysis.

---

## Learning Outcomes

This investigation provided practical experience in detecting, investigating, and analyzing Windows persistence techniques using endpoint telemetry collected by Sysmon and centralized within Wazuh.

Core competencies demonstrated throughout the investigation included:

- Investigating Windows Registry persistence mechanisms.
- Detecting and analyzing scheduled task creation.
- Investigating Windows service installation events.
- Examining Startup folder persistence techniques.
- Performing parent-child process analysis using Sysmon Process Creation events.
- Correlating multiple sources of endpoint telemetry to reconstruct event sequences.
- Conducting targeted threat hunting within Wazuh.
- Mapping observed techniques to the MITRE ATT&CK framework.
- Producing evidence-based incident documentation suitable for SOC and incident response workflows.

This investigation demonstrates how endpoint telemetry collected through Sysmon and centralized within Wazuh can be used to identify persistence mechanisms, reconstruct process execution, correlate related events, and support evidence-based security investigations. The methodology applied throughout this investigation reflects investigative workflows commonly performed by Security Operations Center (SOC) analysts when assessing persistence activity on Windows endpoints.
