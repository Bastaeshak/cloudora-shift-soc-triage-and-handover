# Cloudora Morning Shift: SOC Alert Triage, Investigation, and Handover

## Overview

This repository documents a simulated Tier 1 SOC morning shift involving the investigation and triage of 11 security alerts across identity, email, cloud, endpoint, and network security.

Each alert was investigated using a structured workflow that included reviewing the alert, developing a hypothesis, analyzing the available evidence, establishing a baseline, reassessing severity, documenting findings, and determining the appropriate response.

The goal of this project was to simulate a real SOC shift by producing clear, evidence-based verdicts and creating analyst handover documentation for the next shift.

---

## Skills Demonstrated

- Alert triage
- Incident investigation
- Email header analysis
- Sign-in analysis
- User baseline development
- KQL investigations
- Audit log analysis
- Severity reassessment
- MITRE ATT&CK mapping
- Incident escalation
- Duplicate alert identification
- Shift handover documentation
- Detection tuning

---

## Tools Used

- Azure Data Explorer
- Kusto Query Language (KQL)
- Microsoft Entra ID sign-in logs
- Cloudora audit logs
- Email header analysis
- AbuseIPDB
- MITRE ATT&CK

---

## Alert Queue Summary

| Alert | Investigation Type | Verdict |
| --- | --- | --- |
| CLD-0101 | Identity | False Positive |
| CLD-0102 | Endpoint | Escalated |
| CLD-0103 | Network | False Positive |
| CLD-0104 | Identity | Escalated |
| CLD-0105 | Email | False Positive |
| CLD-0106 | Endpoint | Duplicate |
| CLD-0107 | Cloud Activity | False Positive |
| CLD-0108 | Identity | Escalated |
| CLD-0109 | Network | Escalated |
| CLD-0110 | Identity | True Positive (Blocked) |
| CLD-0111 | Email | Insufficient Data |

---

## Repository Structure

Each alert is organized into its own folder and includes:

- The final verdict
- Evidence reviewed
- Investigation notes
- Severity reassessment
- Recommended actions
- MITRE ATT&CK mapping (when applicable)
- Supporting screenshots

```text
CLD-XXXX/
├── verdict.md
└── screenshots/
