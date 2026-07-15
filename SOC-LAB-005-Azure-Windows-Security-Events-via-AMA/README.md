# LAB-005 – Collecting Windows Security Events using Azure Monitor Agent (AMA)

## Overview

This lab demonstrates how to configure Azure Monitor Agent (AMA) together with a Data Collection Rule (DCR) to collect Windows Security Events from an Azure Virtual Machine and send them to an Azure Log Analytics Workspace.

After configuring the monitoring pipeline, Kusto Query Language (KQL) queries were executed to validate that the security events were successfully ingested and available for analysis. This process represents a common task performed by Cloud Security Engineers and SOC Analysts when onboarding Windows servers into Microsoft Sentinel.

---

## Objectives

- Deploy Azure Monitor Agent (AMA)
- Configure a Data Collection Rule (DCR)
- Associate the DCR with an Azure Virtual Machine
- Collect Windows Security Events
- Validate log ingestion in Azure Log Analytics
- Execute KQL queries to verify collected events
- Confirm agent connectivity using Heartbeat telemetry

---

## Technologies Used

- Microsoft Azure
- Azure Virtual Machine
- Azure Monitor Agent (AMA)
- Azure Monitor
- Azure Log Analytics Workspace
- Data Collection Rules (DCR)
- Microsoft Sentinel
- Windows Server 2022
- Kusto Query Language (KQL)

---

## Environment

| Component | Value |
|------------|-------|
| Virtual Machine | SOC-LAB005-VM |
| Operating System | Windows Server 2022 |
| Azure Region | Sweden Central |
| Resource Group | RG-SOC-LABS |
| Log Analytics Workspace | LAW-SOC-LABS |
| Monitoring Agent | Azure Monitor Agent (AMA) |

---

# Architecture

```
Windows Server VM
        │
        ▼
Azure Monitor Agent (AMA)
        │
        ▼
Data Collection Rule (DCR)
        │
        ▼
Azure Log Analytics Workspace
        │
        ▼
Kusto Query Language (KQL)
        │
        ▼
Microsoft Sentinel
```

---

# Implementation

## Step 1 – Create the Azure Virtual Machine

A Windows Server 2022 virtual machine was deployed in Azure and prepared for monitoring.

---

## Step 2 – Install Azure Monitor Agent

The Azure Monitor Agent (AMA) extension was installed on the virtual machine to enable telemetry collection.

---

## Step 3 – Create the Data Collection Rule

A Data Collection Rule (DCR) was created to define which Windows Security Event Logs would be collected.

The rule was configured to collect:

- Security Event Log

The DCR was associated with the virtual machine to begin collecting telemetry.

---

## Step 4 – Validate Data Collection

After the configuration was completed, the SecurityEvent table began receiving Windows Security events.

Several KQL queries were executed to validate the ingestion process.

---

# Validation

## Windows Security Events

### Query

```kql
SecurityEvent
| take 10
```

### Result

The query returned Windows Security Events collected from the monitored virtual machine, confirming that the Azure Monitor Agent was successfully forwarding security logs to Azure Log Analytics.

**Evidence**

`images/securityevent-take10.png`

---

## Event ID Summary

### Query

```kql
SecurityEvent
| summarize count() by EventID
| order by EventID asc
```

### Result

The returned Event IDs confirmed that Windows Security auditing events were successfully collected and indexed within Azure Log Analytics.

Examples observed:

- Event ID 1100
- Event ID 4616
- Event ID 4634
- Event ID 4673
- Event ID 4688

**Evidence**

`images/securityevent-eventid-summary.png`

---

## Azure Monitor Heartbeat

### Query

```kql
Heartbeat
| summarize count() by Computer
```

### Result

Heartbeat telemetry confirmed that the Azure Monitor Agent was actively communicating with the Log Analytics Workspace.

**Evidence**

`images/heartbeat-summary-by-computer.png`

---

## Data Collection Rule

The Data Collection Rule (DCR) was successfully created and associated with the monitored Azure Virtual Machine.

The configuration defines which Windows Security Event Logs are collected and sent to Azure Log Analytics.

**Evidence**

- `images/dcr-overview.png`
- `images/dcr-associated-resource.png`
- `images/windows-event-collection.png`

---

# KQL Queries Used

### Display Security Events

```kql
SecurityEvent
| take 10
```

---

### Count Events by Event ID

```kql
SecurityEvent
| summarize count() by EventID
| order by EventID asc
```

---

### Verify Azure Monitor Agent Connectivity

```kql
Heartbeat
| summarize count() by Computer
```

---

# Skills Demonstrated

- Azure Administration
- Azure Monitor
- Azure Monitor Agent (AMA)
- Data Collection Rules (DCR)
- Azure Log Analytics
- Microsoft Sentinel
- Windows Event Monitoring
- Windows Security Logging
- Kusto Query Language (KQL)
- Cloud Monitoring
- Cloud Security
- SOC Operations
- Security Event Analysis

---

# Key Learning Outcomes

Throughout this lab it was possible to understand the complete monitoring pipeline used by Microsoft Sentinel to ingest Windows Security Events from Azure Virtual Machines.

The lab covered the installation of Azure Monitor Agent, the creation of Data Collection Rules, resource association, validation of telemetry, and querying collected events using Kusto Query Language.

These activities closely represent operational tasks performed by Cloud Security Engineers and SOC Analysts responsible for onboarding Windows assets into enterprise SIEM environments.

---

# Conclusion

This lab successfully demonstrated how to deploy Azure Monitor Agent (AMA), configure Data Collection Rules (DCR), and collect Windows Security Events into Azure Log Analytics.

The collected data was validated using KQL queries, confirming successful event ingestion and agent connectivity through Heartbeat telemetry.

This implementation provides the foundation required for security monitoring, threat detection, and incident investigation using Microsoft Sentinel.
