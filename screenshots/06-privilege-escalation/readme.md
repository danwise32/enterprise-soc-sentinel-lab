# 📸 Screenshots — 06: Privilege Escalation

These screenshots document the full end-to-end investigation of a simulated 
privilege escalation attack — where a Global Administrator role was silently 
assigned to a standard user account with no change request, approval, or 
legitimate business justification.

**MITRE ATT&CK:** T1078.004 — Cloud Accounts (Privilege Escalation)  
**Severity:** 🔴 Critical  
**Verdict:** True Positive  

---

## Screenshots in This Folder

| File | Description |
|------|-------------|
| `privilege-escalationanalytics-rule-saved.png` | Custom Sentinel analytics rule configured to detect unauthorised privileged role assignments via AuditLogs |
| `privilege-escalationincident-detail.png` | Sentinel incident details panel showing alert metadata, Critical severity, and timeline |
| `privilege-escalationinvestigation-graph.png` | Sentinel investigation graph mapping the role assignment event and involved entities |
| `privilege-escalationkql-auditlog.png` | KQL query results from AuditLogs showing the exact role assignment event with timestamp, target user, and performer |
| `privilege-escalationrole-assignment.png` | Entra ID AuditLogs confirming Global Administrator role was assigned to the standard user account |
| `privilege-escalationrole-removed.png` | Confirmation screenshot showing the Global Administrator role was removed as part of remediation |
| `privilege-escalationsentinel-incident-list.png` | Sentinel incident queue showing the privilege escalation alert raised and listed for investigation |

---

## Attack Summary

A Global Administrator role was silently assigned to a standard user account 
with no approval, change request, or business justification. No Privileged 
Identity Management (PIM) controls were in place to require approval for 
high-privilege role assignments. The custom KQL analytics rule querying 
AuditLogs detected the unauthorised role assignment and raised a Critical 
severity incident in Sentinel within minutes.

## What This Demonstrates

- KQL detection logic querying AuditLogs for unauthorised role assignment events
- Sentinel Critical severity incident creation and investigation workflow
- End-to-end investigation identifying the performer, target user, and exact 
  timestamp of the role assignment
- Remediation verification — confirming the role was removed post-investigation

## Key Finding

The absence of Privileged Identity Management (PIM) controls meant that 
any user with sufficient permissions could silently assign the Global 
Administrator role with no approval workflow or alerting — a critical 
gap in the identity security posture.

## Remediation Applied

- Global Administrator role immediately removed from the affected account
- Privileged Identity Management (PIM) enabled for all privileged roles
- Just-in-time access enforced — admin roles now require approval and 
  have time-limited activation
- Alert rule refined to notify the security team immediately on any 
  privileged role assignment

---

## Related Files
- KQL detection query: [`/kql/privilege-escalation.kql`](../../kql/privilege-escalation.kql)
- SOC incident report: [`/reports`](../../reports)
