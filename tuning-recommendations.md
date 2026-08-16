# Detection Tuning Recommendations

## Overview

During the Cloudora SOC morning shift, several alerts were determined to be false positives, duplicates, or recurring sources of unnecessary analyst workload.

The following recommendations were documented to reduce alert fatigue while preserving visibility into legitimate malicious activity.

The goal is not to disable detections, but to make each rule more precise by incorporating known infrastructure, approved processes, scheduled activity, and additional context.

---

## 1. Service Account Authentication Failures

### Related Alert

CLD-0101 – Service Account Authentication Failures

### Problem

The authentication rule generated a High-severity alert after `svc-backup@cloudora.io` produced multiple authentication failures during the approved credential rotation documented under `CHG-2088`.

The activity was expected, but the detection had no awareness of the approved maintenance window.

### Recommended Tuning

Suppress service-account authentication failure alerts when:

- The authentication originates from the service account's documented host.
- The activity occurs during an approved change window.
- A valid change record exists for the activity.

The authentication rule itself should remain enabled outside these conditions.

### Risk

An attacker operating from the approved host during an active maintenance window could potentially avoid this detection.

Because of this, the exception should be narrowly scoped to the specific service account, source host, and approved time window.

---

## 2. Authorized Vulnerability Scanner Activity

### Related Alert

CLD-0103 – Internal Port Scan

### Problem

The IDS generated an alert when `LDN-SCAN-01 (203.0.113.44)` performed its scheduled vulnerability scan across the internal environment.

The traffic matched the scanner's expected behavior and approved schedule.

### Recommended Tuning

Create a scoped IDS exception for:

`LDN-SCAN-01 – 203.0.113.44`

Only suppress the port-sweep detection during the scanner's approved maintenance window:

`Monday 22:00 UTC – Tuesday 03:00 UTC`

The port-scan rule should remain active for all other hosts and for activity from the scanner outside its approved schedule.

### Risk

If the vulnerability scanner itself were compromised during its approved window, malicious scanning from that host could be suppressed.

The exception should therefore remain limited by both source identity and time window.

---

## 3. MFA Re-Enrollment Context

### Related Alert

CLD-0104 – MFA Failures Followed by Success

### Problem

Multiple MFA failures followed by a successful authentication and new authenticator registration initially resembled an MFA fatigue attack.

The activity was eventually explained by helpdesk ticket `HD-4471`, which documented that the user was replacing her phone and re-enrolling MFA.

### Recommended Tuning

Automatically enrich MFA registration and MFA failure alerts with the user's active helpdesk tickets.

Relevant context could include:

- New phone requests
- MFA resets
- Device replacements
- Account lockouts

The alert should still fire, but the additional context would allow the analyst to determine the cause much faster.

### Risk

Minimal.

This recommendation enriches the alert rather than suppressing it, so detection coverage remains unchanged.

---

## 4. Defender Daily Digest Duplicate Tickets

### Related Alerts

CLD-0102 – Active Malware Infection  
CLD-0106 – Defender Daily Digest

### Problem

CLD-0106 represented the same malware incident already being investigated under CLD-0102.

The same underlying event entered the SOC queue through two different alerting paths, creating unnecessary duplicate work.

### Recommended Tuning

Correlate Defender daily digest notifications with existing active incidents before generating a new ServiceNow ticket.

If the host, malware family, and unresolved detection already belong to an open incident, the digest should:

- Update the existing incident, or
- Reference the existing incident rather than create a new ticket.

### Risk

Before disabling automatic digest ticket creation, verify that detections appearing only in the digest are generated through another real-time alerting mechanism.

Otherwise, a digest-only detection could be missed.

---

## 5. Approved HR Retention Workflow

### Related Alert

CLD-0107 – SharePoint Mass Deletion

### Problem

The mass-deletion rule fired after `HR-RetentionFlow` deleted more than 1,200 expired records from the `Leavers-2019` archive.

The activity was expected under Cloudora's seven-year retention policy, but the detection treated the automated process the same way it would treat an interactive user deleting large amounts of data.

### Recommended Tuning

Exclude the `HR-RetentionFlow` initiating process from the mass-deletion rule when it executes during its approved retention schedule.

Do not increase the deletion threshold.

Interactive mass deletion should continue generating alerts at the existing threshold.

Consider creating a separate detection for:

- `HR-RetentionFlow` running outside its approved schedule.
- The process modifying unexpected folders.
- The automation targeting live rather than archived records.

### Risk

If an attacker compromised the retention workflow itself, activity using the approved process identity could potentially be suppressed.

Monitoring the workflow for abnormal execution times and unexpected target locations should compensate for this risk.

---

## 6. Coordinated Multi-Host Beaconing Detection

### Related Alert

CLD-0109 – Repeated Outbound Connections

### Problem

Three endpoints in three separate offices independently connected to the same newly registered, uncategorized destination approximately every 60 seconds.

The pattern strongly resembled automated command-and-control beaconing, but identifying the relationship required manual correlation.

### Recommended Tuning

Create a correlation detection that increases severity when:

- Multiple internal endpoints contact the same uncommon external destination.
- Connections occur at consistent or periodic intervals.
- The destination has little or no organizational history.
- The destination is newly registered or uncategorized.
- The affected hosts span multiple offices or network segments.

This would allow coordinated activity to be surfaced as one higher-confidence investigation instead of several isolated network events.

### Risk

Some legitimate enterprise applications or newly deployed services may produce similar periodic communication patterns.

The detection should therefore incorporate software deployment records, destination reputation, domain age, and host count before increasing severity.

---

# Summary

| Related Alert | Detection / Process | Recommended Change |
|---|---|---|
| CLD-0101 | Service account failures | Suppress known service-account failures from approved hosts during documented change windows |
| CLD-0103 | IDS port sweep | Add a time-scoped exception for the authorized vulnerability scanner |
| CLD-0104 | MFA failures / enrollment | Enrich identity alerts with active helpdesk context |
| CLD-0106 | Defender digest | Correlate digest alerts with existing real-time incidents |
| CLD-0107 | SharePoint mass deletion | Exclude the approved retention-flow process rather than increasing the deletion threshold |
| CLD-0109 | Network beaconing | Correlate periodic communication to the same rare destination across multiple endpoints |

---

## Key Principle

Detection tuning should reduce known false positives and duplicate work without creating blind spots.

Whenever possible, exceptions should be scoped using specific process identities, hosts, time windows, or contextual data rather than disabling rules or broadly increasing thresholds.
