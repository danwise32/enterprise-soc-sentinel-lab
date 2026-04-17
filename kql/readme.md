# 🔍 KQL Detection Queries

This folder contains all custom KQL (Kusto Query Language) detection 
queries written for the Enterprise SOC Monitoring & Identity Protection 
Lab. Each file covers one attack scenario and contains three queries — 
a Detection Rule, an Investigation Query, and a Remediation Verification 
Query.

---

## Queries in This Folder

| File | Scenario | MITRE ATT&CK | Severity |
|------|----------|--------------|----------|
| [`brute-force-detection.kql`](./brute-force-detection.kql) | Brute Force Attack | T1110 — Credential Access | 🔴 High |
| [`impossible-travel.kql`](./impossible-travel.kql) | Impossible Travel | T1078 — Initial Access | 🔴 High |
| [`suspicious-login.kql`](./suspicious-login.kql) | Suspicious Login Behaviour | T1078 — Initial Access | 🟡 Medium |
| [`privilege-escalation.kql`](./privilege-escalation.kql) | Privilege Escalation | T1078.004 — Privilege Escalation | 🔴 Critical |
| [`mfa-bypass.kql`](./mfa-bypass.kql) | MFA Bypass via Prompt Bombing | T1621 — Credential Access | 🟠 Medium–High |

---

## Query Structure

Every `.kql` file follows the same three-part structure:

**Part 1 — Detection Rule Query**
Runs automatically in Microsoft Sentinel as a scheduled analytics rule.
Triggers an incident alert when the attack pattern is detected.
Includes: table source, time window, detection logic, and projected fields.

**Part 2 — Investigation Query**
Run manually during incident response to gather evidence.
Extends the detection window and returns additional context fields
to support the SOC investigation and verdict decision.

**Part 3 — Remediation Verification Query**
Run after remediation is applied to confirm the fix was successful.
For example — confirming a role was removed, a session was revoked,
or that MFA denials have stopped following policy enforcement.

---

## Detection Logic Summary

### Brute Force (T1110)
Detects accounts with 10+ failed sign-ins within a 10-minute window
using SigninLogs. Triggers on failed login threshold breach and 
projects the affected user, failure count, and timestamps.

### Impossible Travel (T1078)
Detects two successful sign-ins from geographically distant locations
within a time window too short for physical travel. Analyses SigninLogs
for location data and timestamp gaps between consecutive successful logins.

### Suspicious Login Behaviour (T1078)
Detects sign-in activity from 3 or more distinct IP addresses within
a short window, combined with off-hours login timing anomalies.
Flags VPN exit node usage as an additional indicator of compromise.

### Privilege Escalation (T1078.004)
Detects unauthorised privileged role assignments by querying AuditLogs
for "Add member to role" operations. Extracts the assigned role, target
user, and the account that performed the assignment for full attribution.

### MFA Bypass via Prompt Bombing (T1621)
Detects high-frequency MFA denial events against a single account
within a short time window. Triggers when denial count exceeds a 
threshold consistent with a prompt bombing attack pattern.

---

## How These Queries Were Used

Each query was deployed as a **Sentinel Analytics Rule** — running 
automatically on a scheduled frequency (every 5–15 minutes depending 
on scenario). When triggered, Sentinel created an incident in the 
incident queue which was then investigated using the corresponding 
Investigation Query.

After each investigation, the Remediation Verification Query was run 
to confirm the fix was applied and the threat was neutralised.

---

## Example Query — Privilege Escalation Detection

```kql
// Detects privileged admin role assignments to any user account
// MITRE ATT&CK: T1078.004 — Privilege Escalation
// Table: AuditLogs | Frequency: Every 5 minutes
AuditLogs
| where TimeGenerated > ago(1h)
| where OperationName contains "Add member to role"
| extend
    AssignedRole = tostring(TargetResources[0].displayName),
    TargetUser = tostring(TargetResources[1].userPrincipalName),
    PerformedBy = tostring(InitiatedBy.user.userPrincipalName)
| where isnotempty(AssignedRole)
| project TimeGenerated, AssignedRole, TargetUser, PerformedBy, Result
| order by TimeGenerated desc
```

---

## Related Files
- SOC incident reports: [`/reports`](../reports)
- Screenshots evidence: [`/screenshots`](../screenshots)
- Lab setup guide: [`/docs`](../docs)
- Main README: [`/README.md`](../README.md)
