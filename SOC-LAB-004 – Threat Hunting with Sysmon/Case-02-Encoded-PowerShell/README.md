# Case Study 2 – Investigation of Base64-Encoded PowerShell Execution

## Scenario

During routine security monitoring, Wazuh generated a high-severity alert indicating that a PowerShell process executed a Base64-encoded command.

Encoded PowerShell commands are commonly used by attackers to obfuscate malicious activity, evade detection mechanisms, and execute payloads without exposing the original command.

The objective of this investigation was to determine whether the detected activity represented a malicious execution attempt or legitimate administrative behavior.

---

## Alert Summary

| Field | Value |
|--------|-------|
| Timestamp | Jul 11, 2026 @ 09:42:15 |
| Host | DESKTOP-MARCIO |
| User | Marcio-Braga\yukem |
| Event ID | 1 (Process Creation) |
| Rule ID | 92057 |
| Rule Description | Powershell.exe spawned a powershell process which executed a base64 encoded command |
| Severity | 12 (High) |
| MITRE ATT&CK | T1059.001 – PowerShell |

---

## Initial Evidence

Wazuh detected the execution of a PowerShell process using the **-EncodedCommand** parameter.

The generated event identified the following process:

**Process**

```text
powershell.exe
```

**Executed Command**

```powershell
powershell.exe -EncodedCommand dwBoAG8AYQBtAGkA
```

The **-EncodedCommand** parameter allows PowerShell commands to be executed using Base64 encoding.

This technique is frequently observed in malicious PowerShell scripts because it hides the original command from casual inspection.

For this reason, Wazuh generated a high-severity alert for investigation.

---

# Evidence

## Figure 1 – Process and Event Details

![Figure 1 - Process and Event Details](case2-figure1-process-details.png)

*Figure 1 presents the technical details of the PowerShell process collected by Sysmon and forwarded to Wazuh, including the encoded command, process image, parent process, user context, integrity level, process identifiers, and cryptographic hashes.*

---

## Figure 2 – Wazuh Alert Summary

![Figure 2 - Wazuh Alert Summary](case2-figure2-alert-summary.png)

*Figure 2 presents the Wazuh detection metadata, including the triggered rule, severity level, MITRE ATT&CK mapping, Event ID, decoder, provider information, and timestamp associated with the generated alert.*

---

## Investigation

The investigation focused on determining whether the encoded PowerShell execution represented malicious activity.

### Process Created

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

### Parent Process

```text
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
```

### Executed Command

```powershell
powershell.exe -EncodedCommand dwBoAG8AYQBtAGkA
```

### User

```text
Marcio-Braga\yukem
```

### Integrity Level

```text
High
```

### Process Purpose

The PowerShell process was executed using the **-EncodedCommand** parameter, which accepts commands encoded in Base64.

Although this feature is commonly used by administrators for automation, it is also widely abused by attackers to obfuscate malicious PowerShell commands.

Because of this dual use, PowerShell encoded commands are considered high-value events during threat hunting and incident investigations.

---

## Command Analysis

The Base64 string used during execution was:

```text
dwBoAG8AYQBtAGkA
```

After decoding, the command corresponds to:

```powershell
whoami
```

The decoded command simply returns the identity of the currently logged-on user.

No malicious payloads, download activity, persistence mechanisms, or code execution attempts were identified.

---

## MITRE ATT&CK Analysis

| Tactic | Technique | ID |
|---------|-----------|----|
| Execution | PowerShell | T1059.001 |

The generated alert was correctly mapped to **MITRE ATT&CK T1059.001 – PowerShell**, since the command was executed through PowerShell using an encoded command line.

**MITRE ATT&CK Navigator**

Execution → PowerShell (T1059.001)

---

## Investigation Timeline

1. Wazuh detected a PowerShell execution using the **-EncodedCommand** parameter.
2. Sysmon recorded the Process Creation event (Event ID 1).
3. The encoded command line was extracted from the event.
4. The Base64 string was decoded.
5. The decoded command was analyzed.
6. No additional malicious activity was identified.
7. The alert was classified as a **Benign True Positive**.

---

## Analyst Assessment

The investigation confirmed that the encoded PowerShell command was intentionally executed by the legitimate user **Marcio-Braga\yukem** during a controlled SOC laboratory exercise.

The parent-child relationship showed that PowerShell launched another PowerShell instance using the **-EncodedCommand** parameter.

Although this execution technique is commonly associated with malware and offensive frameworks, the decoded command revealed only a simple administrative command:

```powershell
whoami
```

No additional suspicious activity, persistence mechanisms, network connections, or indicators of compromise were observed.

---

## Analyst Verdict

| Result | Classification |
|----------|---------------|
| ✅ Event Confirmed | PowerShell execution successfully detected |
| ✅ True Positive | The event actually occurred |
| ✅ Legitimate Administrative Activity | Encoded command executed intentionally |
| ❌ Security Incident | Not confirmed |
| ❌ Escalation Required | No |

---

## 🧠 Analyst's Thought Process

> **Why was this alert generated?**
>
> Wazuh detected a PowerShell process executing another PowerShell instance using the **-EncodedCommand** parameter. Base64-encoded PowerShell commands are frequently associated with attacker tradecraft because they hide the original command from plain text inspection.
>
> **What evidence was collected?**
>
> The investigation analyzed the command line, process hierarchy, user context, integrity level, process identifiers, cryptographic hashes, detection rule, severity level, and MITRE ATT&CK mapping.
>
> **What was investigated?**
>
> The encoded command was decoded to determine its actual functionality. The parent-child process relationship and execution context were also analyzed to identify potential malicious behavior.
>
> **What led to the final decision?**
>
> After decoding the Base64 string, the command was identified as `whoami`, a legitimate Windows command used to display the current user identity. No additional malicious indicators were observed.
>
> **Final Decision**
>
> The alert was classified as a **Benign True Positive**. Wazuh correctly detected a technique frequently used by attackers; however, the decoded command and execution context confirmed that the activity was legitimate.

---

## Lessons Learned

PowerShell encoded commands should always be investigated because attackers frequently use Base64 encoding to conceal malicious scripts.

However, the presence of an encoded command alone is insufficient to classify an event as malicious.

SOC analysts should always:

- Decode the Base64 string.
- Analyze the executed command.
- Review the parent process.
- Validate the execution context.
- Correlate additional Sysmon events.
- Search for follow-up malicious behavior.

Only after completing this analysis can the activity be accurately classified.

---

## 🔍 Threat Hunter Tip

One of the first actions during a PowerShell investigation is to decode any Base64-encoded command.

Attackers frequently use the **-EncodedCommand** parameter to conceal malicious scripts, download payloads, or evade signature-based detection.

Decoding the command immediately reveals the attacker's actual intent and significantly accelerates the investigation process.

---

## Key Takeaway

> **Encoded PowerShell commands indicate a technique—not necessarily malicious intent.**
>
> SOC analysts must decode the command, analyze the execution context, and correlate additional evidence before determining whether the activity represents a legitimate administrative action or a real security incident.
