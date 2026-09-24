# SSH Brute Force Detection

## Overview

This exercise simulates an SSH brute-force attack against the Ubuntu server using Hydra from the Kali Linux attacker workstation. The objective was to generate authentication logs, validate Wazuh's ability to detect repeated failed login attempts, and perform a SOC analyst investigation of the activity.

This exercise provided practical experience with:

* SSH authentication monitoring
* Linux log analysis
* Credential attack detection
* Threat hunting in Wazuh
* Incident investigation
* MITRE ATT&CK mapping

---

## Objective

The primary objectives of this exercise were to:

* Verify the SSH service was running
* Launch a brute-force attack using Hydra
* Generate authentication telemetry
* Monitor Linux authentication logs
* Investigate the attack using Wazuh
* Determine whether authentication was successful
* Produce an incident report

---

## Lab Environment

| Component     | Role                 | IP Address     |
| ------------- | -------------------- | -------------- |
| Kali Linux    | Attacker Workstation | 192.168.56.103 |
| Ubuntu Server | Target Server        | 192.168.56.102 |
| Wazuh OVA     | SIEM Platform        | 192.168.56.104 |

---

## Attack Flow

```text
Kali Linux
      │
      ▼
Hydra SSH Brute Force
      │
      ▼
Ubuntu SSH Server
      │
      ├────────► auth.log
      │
      ▼
Wazuh SIEM
```

---

## Phase 1 – Verify SSH Service

The SSH service was verified on the Ubuntu server.

```bash
sudo systemctl status ssh
```

A successful result showed:

```text
Active: active (running)
```

If the service was not running, it was started using:

```bash
sudo systemctl start ssh
```
<img width="1920" height="423" alt="Screenshot (723)" src="https://github.com/user-attachments/assets/6cf6ef5e-a379-45aa-b983-53508573fca7" />

To ensure SSH started automatically after reboot:

```bash
sudo systemctl enable ssh
```


---

## Verify SSH is Listening

The SSH service was confirmed to be listening on port **22**.

```bash
sudo ss -tulpn | grep :22
```

Expected output:

```text
LISTEN ... :22
```
<img width="1920" height="150" alt="Screenshot (724)" src="https://github.com/user-attachments/assets/d8b38182-c702-4ca3-90e0-d4a2e4602764" />

---

## Test SSH Connectivity

Connectivity from the Kali Linux VM was verified.

```bash
ssh username@192.168.56.102
```
<img width="1920" height="150" alt="Screenshot (725)" src="https://github.com/user-attachments/assets/bb166d31-aff9-4c54-a2fd-5ff95b4a26f9" />


Receiving a password prompt confirmed successful connectivity.

---

## Phase 2 – Verify Authentication Logging

Ubuntu stores SSH authentication events in:

```text
/var/log/auth.log
```

A failed login was generated using:

```bash
ssh fakeuser@192.168.56.102
```
<img width="1920" height="157" alt="Screenshot (726)" src="https://github.com/user-attachments/assets/d4fe9148-2483-426d-b2e6-245bd4f9a754" />

The authentication logs were reviewed using:

```bash
sudo tail -20 /var/log/auth.log
```
<img width="1920" height="144" alt="Screenshot (727)" src="https://github.com/user-attachments/assets/9af5e789-e482-4199-a06d-1c105e9a196a" />

Example log entry:

```text
Failed password for invalid user fakeuser
```

These logs confirm that Ubuntu was generating telemetry for Wazuh to ingest.

---

## Phase 3 – Verify Hydra Wordlist

The available wordlists were verified.

```bash
ls /usr/share/wordlists/
```

The RockYou wordlist was compressed, it was extracted using:

```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

Verification:

```bash
ls /usr/share/wordlists/rockyou.txt
```

---

## Phase 4 – Launch the Brute Force Attack

A small custom password list was created to speed up testing.

<img width="1920" height="150" alt="Screenshot (728)" src="https://github.com/user-attachments/assets/044c7373-f5b2-4d8d-b71c-bf673ba58bcb" />


Hydra was then executed from the Kali Linux VM.

```bash
hydra -l username -P passwords.txt ssh://192.168.56.102
```
<img width="1920" height="153" alt="Screenshot (729)" src="https://github.com/user-attachments/assets/1fb13023-46bc-4932-8a62-d8ac756fd864" />

This generated multiple authentication attempts against the Ubuntu SSH service.

---

## Phase 5 – Monitor the Attack

Authentication logs were monitored in real time.

```bash
sudo tail -f /var/log/auth.log
```
<img width="1920" height="212" alt="Screenshot (730)" src="https://github.com/user-attachments/assets/d767363b-86bc-472a-afa1-9a9d444cc649" />

Example events:

```text
Failed password for <username>
Connection closed
```

These entries confirmed that repeated authentication attempts were reaching the target server.

---

## Phase 6 – Investigate in Wazuh

The generated telemetry was investigated using the Wazuh Dashboard.

Navigate to:

* Threat Hunting

Search using:

* `sshd`
* `authentication failure`
* `failed password`
* `192.168.56.103`
<img width="1920" height="824" alt="Screenshot (732)" src="https://github.com/user-attachments/assets/1df40d8b-f457-4337-adc6-4397cf44a1ff" />
<img width="1920" height="828" alt="Screenshot (733)" src="https://github.com/user-attachments/assets/9676b2f8-0610-4652-873c-192aa1ba555b" />
<img width="1920" height="840" alt="Screenshot (735)" src="https://github.com/user-attachments/assets/71f30501-5dd4-498d-98ab-0a66250775cd" />

This investigation helped identify:

* Source IP address
* Target username
* Authentication failures
* Event timeline
* Attack frequency

---

## Detection Indicators

The following indicators were observed.

| Indicator   | Observation                    |
| ----------- | ------------------------------ |
| Source IP   | 192.168.56.103                 |
| Destination | Ubuntu Server                  |
| Service     | SSH                            |
| Port        | 22                             |
| Attack Tool | Hydra                          |
| Activity    | SSH Brute Force                |
| Result      | Failed Authentication Attempts |
| Severity    | Medium                         |

---

## SOC Analyst Investigation

## Incident Summary

A brute-force attack was launched from the Kali Linux virtual machine against the Ubuntu server's SSH service using Hydra.

Multiple failed authentication attempts targeting a valid user account were observed within a short period. The activity generated authentication logs that were successfully ingested into Wazuh for analysis.

No successful authentication events were identified during the investigation.

---

## Investigation Findings

| Question                  | Finding                |
| ------------------------- | ---------------------- |
| Who initiated the attack? | Kali Linux VM          |
| Who was targeted?         | Ubuntu Server          |
| Service Targeted          | SSH                    |
| Port                      | 22                     |
| Username Targeted         | xxxxxxx                |
| Successful Login?         | No                     |
| Attack Type               | Credential Brute Force |

---

## Timeline

| Time  | Activity                              |
| ----- | ------------------------------------- |
| 16:33 | SSH connection initiated              |
| 16:43 | Multiple failed password attempts     |
| 16:44 | Hydra completed attack                |
| 16:44 | No successful authentication observed |

---

## Indicators of Compromise (IOCs)

* Source IP Address
* Destination IP Address
* Target Username
* Port 22
* Multiple failed authentication attempts
* Authentication log entries
* Hydra-generated SSH traffic

---

## MITRE ATT&CK Mapping

| Tactic                           | Technique              |
| -------------------------------- | ---------------------- |
| Credential Access                | T1110 – Brute Force    |
| Initial Access *(if successful)* | T1078 – Valid Accounts |

---

## Impact Assessment

No unauthorized access was obtained during this exercise.

However, repeated authentication failures indicate an active credential attack and should be investigated immediately within a production environment.

---

## Recommendations

Following the investigation, the following defensive measures are recommended:

* Enable account lockout policies.
* Restrict SSH access using firewall rules.
* Disable password authentication where possible.
* Implement SSH key-based authentication.
* Monitor repeated authentication failures.
* Configure alerting for brute-force behaviour.
* Block malicious source IP addresses after repeated failures.

---

## Issues Encountered

| Issue                   | Resolution                                                               |
| ----------------------- | ------------------------------------------------------------------------ |
| SSH service not running | Started the SSH service using `systemctl`.                               |
| SSH connection failed   | Verified port 22 was listening and connectivity between Kali and Ubuntu. |
| Wordlist unavailable    | Extracted the RockYou wordlist before testing.                           |
| wazuh agent misconfig   | configured wazuh agent to monitor auth.log                               |
---

## Learning Outcomes

Through this exercise, I gained practical experience with:

* Linux authentication monitoring
* SSH brute-force attacks
* Hydra usage
* Authentication log analysis
* Threat hunting in Wazuh
* Credential attack investigation
* MITRE ATT&CK mapping
* SOC incident investigation workflows

This exercise demonstrated how repeated failed SSH authentication attempts appear from a defender's perspective and reinforced the importance of monitoring authentication logs as an early indicator of credential-based attacks.
