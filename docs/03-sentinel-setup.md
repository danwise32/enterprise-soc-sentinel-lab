# Microsoft Sentinel Setup

## Workspace Details

- Workspace name: SOCLabWorkspace
- Resource group: SOC-Lab-RG
- Location: UK South
- Subscription: Azure subscription 1

## Data Connectors Connected

| Connector | Status | Log Types |
|---|---|---|
| Microsoft Entra ID | Connected | Sign-In Logs, Audit Logs, Non-Interactive Logs, User Risk Events, Risky Users |
| Microsoft Entra ID Protection | Connected | Risk alerts |

## Analytics Rules Created

| Rule Name | Severity | MITRE Tactic | Technique | Frequency |
|---|---|---|---|---|
| Brute Force Attack — Multiple Failed Logins | High | Credential Access | T1110 | Every 5 minutes |
| Impossible Travel — Login from Multiple IPs | High | Initial Access | T1078 | Every 5 minutes |
| Suspicious Login Behaviour | Medium | Initial Access | T1078 | Every 5 minutes |
| Privilege Escalation — Admin Role Assignment | Critical | Privilege Escalation | T1078.004 | Every 5 minutes |
| MFA Bypass Attempt | High | Credential Access | T1556.006 | Every 5 minutes |

## Incidents Raised

| Incident | Scenario | Severity | Status |
|---|---|---|---|
| SOC-2026-001 | Brute Force | High | Investigated |
| SOC-2026-002 | Impossible Travel | High | Investigated |
| SOC-2026-003 | Suspicious Login | Medium | Pending |
| SOC-2026-004 | Privilege Escalation | Critical | Pending |
| SOC-2026-005 | MFA Bypass | High | Pending |
