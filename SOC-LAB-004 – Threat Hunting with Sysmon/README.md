# Case Study 1 – Windows Discovery Activity

## Scenario

During routine security monitoring, Wazuh generated an alert indicating that a Windows Discovery command had been executed on the monitored endpoint.

The objective of this investigation was to determine whether the detected activity represented legitimate administrative behavior or a potential security threat.

---

## Alert Summary

| Field | Value |
|--------|-------|
| Timestamp | Jul 9, 2026 @ 20:07:53 |
| Host | DESKTOP-MARCIO |
| User | Marcio-Braga\yukem |
| Event ID | 1 (Process Creation) |
| Rule ID | 92031 |
| Rule Description | Discovery activity executed |
| Severity | 3 |
| MITRE ATT&CK | T1087 – Account Discovery |

---

## Initial Evidence

The Wazuh SIEM detected the execution of a Windows Discovery command.

The generated event indicates the creation of the following process:

**Process**

```text
net1.exe
```

**Executed Command**

```cmd
net localgroup administrators
```

This Windows command enumerates all members of the local **Administrators** group and is commonly used during system administration.

Because attackers frequently execute this command during the **Discovery** phase after gaining access to a compromised host, Wazuh correctly generated an alert for further investigation.

---

# Evidence

## Figure 1 – Wazuh Alert Overview

![Figure 1 - Wazuh Alert Overview](alert-overview.png)

*Figure 1 shows the Wazuh alert summary, including the detection rule, severity, MITRE ATT&CK mapping, and the affected endpoint.*

---

## Figure 2 – Event Details

![Figure 2 - Event Details](event-details.png)

*Figure 2 presents the Sysmon event details, including the executed command, process information, user context, hashes, and process identifiers used during the investigation.*

---

## Investigation

The investigation focused on validating the origin of the process and determining whether the activity was expected or potentially malicious.

### Process Created

```text
C:\Windows\System32\net1.exe
```

### Parent Process

```text
C:\Windows\System32\net.exe
```

### Executed Command

```cmd
net localgroup administrators
```

### User

```text
Marcio-Braga\yukem
```

### Process Purpose

The command **net localgroup administrators** enumerates all local accounts that belong to the **Administrators** group.

Although this command is frequently executed by system administrators, it is also commonly observed during adversary reconnaissance following initial system compromise.

For this reason, security monitoring solutions frequently classify this activity as **Discovery**.

---

## MITRE ATT&CK Analysis

| Tactic | Technique | ID |
|---------|-----------|----|
| Discovery | Account Discovery | T1087 |

The generated alert was correctly mapped to **MITRE ATT&CK T1087 – Account Discovery**, since the executed command attempts to enumerate privileged local accounts.

---

## Analyst Assessment

The investigation confirmed that the command was intentionally executed by the legitimate user **Marcio-Braga\yukem** during a controlled laboratory exercise.

The parent-child process relationship was consistent with normal Windows behavior:

```text
net.exe
        │
        └── net1.exe
```

No indicators of privilege escalation, persistence mechanisms, lateral movement, or additional suspicious processes were identified during the investigation.

Although this command is commonly associated with attacker reconnaissance, its execution alone is insufficient to classify the activity as malicious.

---

## Analyst Verdict

| Result | Classification |
|----------|---------------|
| ✅ Event Confirmed | Process creation successfully detected |
| ✅ True Positive | The event actually occurred |
| ✅ Legitimate Administrative Activity | Command executed intentionally |
| ❌ Security Incident | Not confirmed |
| ❌ Escalation Required | No |

---

## 🧠 Analyst's Thought Process

> **Why was this alert generated?**
>
> Wazuh detected the execution of the command `net localgroup administrators`, which is commonly associated with the **Discovery** phase of the MITRE ATT&CK framework. Attackers frequently use this command after gaining access to a system to identify users with administrative privileges.
>
> **What evidence was collected?**
>
> The investigation gathered the executed command, process image, parent process, user context, timestamp, detection rule, severity level, and MITRE ATT&CK mapping.
>
> **What was investigated?**
>
> The process hierarchy (`net.exe → net1.exe`), the executed command, the user account, and the execution context were analyzed to determine whether the activity represented normal administrative behavior or potential attacker reconnaissance.
>
> **What led to the final decision?**
>
> The command was intentionally executed by the legitimate user **Marcio-Braga\yukem** during a controlled laboratory exercise. No additional suspicious behavior, persistence mechanisms, privilege escalation, or indicators of compromise were identified.
>
> **Final Decision**
>
> The alert was classified as a **Benign True Positive**. Wazuh correctly detected a behavior commonly associated with attacker reconnaissance; however, the surrounding context confirmed that the activity was legitimate.

---

## Lessons Learned

This investigation demonstrates that not every alert generated by a SIEM represents malicious activity.

Commands such as **net localgroup administrators** are legitimate Windows administrative utilities but are also frequently used during the **Discovery** phase of cyber attacks.

A SOC analyst should never rely solely on the detection rule. Instead, the investigation must consider:

- User context
- Parent process
- Command line
- Process purpose
- MITRE ATT&CK mapping
- Overall execution context

Only after correlating these elements is it possible to determine whether escalation is necessary.

---

## Key Takeaway

> **A SIEM generates alerts based on behavior, not intent.**
>
> The responsibility of a SOC analyst is to investigate the surrounding context, correlate the available evidence, and determine whether the observed behavior represents legitimate administrative activity or a genuine security incident.
