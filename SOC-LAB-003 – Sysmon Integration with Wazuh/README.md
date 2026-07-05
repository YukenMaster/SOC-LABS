# LAB-003 – Sysmon Integration with Wazuh

## Overview

This lab demonstrates how to integrate Microsoft Sysmon with the Wazuh SIEM platform to improve Windows endpoint visibility and threat detection.

Sysmon provides detailed telemetry about process creation, network connections, file creation, registry modifications, and other system activities. Wazuh collects these events, decodes them, applies detection rules, and maps them to the MITRE ATT&CK framework.

---

## Objectives

- Install Microsoft Sysmon
- Configure Sysmon with a community security configuration
- Configure the Wazuh Agent to collect Sysmon logs
- Validate event collection
- Trigger a Process Create event
- Analyze the generated alert in Wazuh
- Identify the corresponding MITRE ATT&CK technique

---

## Lab Environment

| Component | Version |
|-----------|---------|
| Windows 11 | Client |
| Ubuntu Server | VirtualBox |
| Docker | Latest |
| Wazuh | 4.14.5 |
| Sysmon | 15.21 |

---

## Architecture

```
Windows Endpoint
        │
        │
    Sysmon
        │
        │
Windows Event Log
        │
        │
Wazuh Agent
        │
        │
Wazuh Manager
        │
        │
Alert Decoder
        │
        │
Detection Rule
        │
        │
MITRE ATT&CK Mapping
```

---

# Step 1 – Verify Sysmon Status

Initially, Sysmon was not installed on the endpoint.

![Sysmon Not Installed](01-sysmon-not-installed.png)

---

# Step 2 – Install Sysmon

Sysmon was installed using the SwiftOnSecurity configuration file.

The installation completed successfully and Windows immediately started generating Sysmon Event ID 1 (Process Creation) events.

![Sysmon Installation](images/02-sysmon-installation-validation.png)

---

# Step 3 – Configure Wazuh Agent

The Wazuh Agent was configured to collect the Sysmon Operational log.

```xml
<localfile>
    <location>Microsoft-Windows-Sysmon/Operational</location>
    <log_format>eventchannel</log_format>
</localfile>
```

After editing the configuration, the Wazuh Agent service was restarted.

![Wazuh Configuration](images/03-ossec-conf-sysmon-configuration.png)

---

# Step 4 – Detection Evidence

After generating a Process Create event, Wazuh successfully received and decoded the Sysmon event.

The JSON output contains:

- Agent information
- Process name
- Parent process
- Command line
- User
- SHA256 hash
- Process GUID
- Timestamp

![Sysmon JSON Evidence](images/06-sysmon-event-evidence-part1.png)

---

# Step 5 – Detection Analysis

Wazuh analyzed the Sysmon event and triggered Detection Rule **92052**.

Detection Details:

- Rule ID: **92052**
- Severity: **4**
- Description:
  - Windows command prompt started by an abnormal process

MITRE ATT&CK Mapping:

| Tactic | Technique | ID |
|---------|-----------|----|
| Execution | Windows Command Shell | T1059.003 |

![Detection Rule](images/07-sysmon-event-evidence-part2.png)

---

# Detection Summary

| Field | Value |
|--------|-------|
| Event Source | Sysmon |
| Event ID | 1 |
| Process | cmd.exe |
| Parent Process | chrome.exe |
| User | Marcio-Braga\\yukem |
| Detection Rule | 92052 |
| Severity | 4 |
| MITRE ATT&CK | T1059.003 |
| Decoder | windows_eventchannel |

---

# Skills Demonstrated

- Windows Event Monitoring
- Microsoft Sysmon
- Endpoint Telemetry
- Wazuh Agent Configuration
- Wazuh Rule Analysis
- Event Decoding
- Threat Detection
- MITRE ATT&CK Mapping
- Security Monitoring
- SIEM Investigation

---

# Key Takeaways

This lab demonstrates the complete integration between Sysmon and Wazuh.

The endpoint generated detailed Windows telemetry, the Wazuh Agent collected the events, the Wazuh Manager decoded the logs, applied detection rules, and mapped the activity to the MITRE ATT&CK framework.

This setup significantly improves endpoint visibility and enables analysts to investigate suspicious Windows activity with rich contextual information.

---

## Author

**Marcio Braga**

Cybersecurity Student

SOC Analyst | Blue Team | Wazuh | SIEM | Windows Security | AWS Cloud Security (Learning)
