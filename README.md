# Enterprise Security Monitoring and Identity Protection Lab

A hands-on cybersecurity portfolio project simulating a real-world SOC environment
using Microsoft Sentinel as the SIEM and Microsoft Entra ID for identity and access management.
Built to demonstrate practical SOC analyst skills including threat detection, KQL query writing,
incident investigation, and security reporting.

---

## Project Overview

This lab simulates an enterprise security environment in Microsoft Azure with three user accounts
configured at varying privilege and security levels to represent realistic identity risk profiles.
Microsoft Sentinel is deployed and connected to Entra ID to ingest sign-in and audit logs in real time.
Five attack scenarios were simulated and investigated, each producing a full SOC incident report,
KQL detection query, and investigation graph analysis.

**Tenant:** Cyberbulwork.onmicrosoft.com
**Workspace:** SOCLabWorkspace
**Region:** UK South
**Analyst:** Daniel Owoeye-Wise

---

## Attack Scenarios Completed

| Incident ID | Scenario | Severity | MITRE Tactic | MITRE Technique | Status |
|---|---|---|---|---|---|
| SOC-2026-001 | Brute Force Attack | High | Credential Access | T1110 | Investigated |
| SOC-2026-002 | Impossible Travel | High | Initial Access | T1078 | Investigated |
| SOC-2026-003 | Suspicious Login Behaviour | Medium | Initial Access | T1078 | Investigated |
| SOC-2026-004 | Privilege Escalation | High | Privilege Escalation | T1078.004 | Investigated |
| SOC-2026-005 | MFA Bypass Attempt | High | Credential Access | T1556.006 | Investigated |

---

## Lab Environment

### Identity Configuration

| Account | Role | MFA | Purpose |
|---|---|---|---|
| labadmin@Cyberbulwork.onmicrosoft.com | Global Administrator | Enforced | High value admin target |
| StandardUser@Cyberbulwork.onmicrosoft.com | Global Reader | Enforced | Regular employee simulation |
| CompromisedUser@Cyberbulwork.onmicrosoft.com | None | Disabled | Deliberately vulnerable account |

### Conditional Access Policies

| Policy | Scope | Control |
|---|---|---|
| Require MFA Lab Users | Lab Admin and Standard User | Require MFA |
| Block Legacy Authentication | All users | Block access |
| Block High Risk Sign-ins | All users | Block access |

### Microsoft Sentinel

- Log Analytics Workspace: SOCLabWorkspace
- Data connectors: Microsoft Entra ID, Microsoft Entra ID Protection
- Log types ingested: Sign-In Logs, Audit Logs, Non-Interactive Logs, User Risk Events, Risky Users
- Custom analytics rules: 5 scheduled rules mapped to MITRE ATT&CK

---

## Repository Structure
enterprise-soc-sentinel-lab/
├── docs/
│   ├── 01-lab-setup.md
│   ├── 02-entra-id-config.md
│   └── 03-sentinel-setup.md
├── kql/
│   ├── brute-force-detection.kql
│   ├── impossible-travel.kql
│   ├── suspicious-login.kql
│   ├── privilege-escalation.kql
│   └── mfa-bypass.kql
├── reports/
│   ├── SOC-2026-001-BruteForce.md
│   ├── SOC-2026-002-ImpossibleTravel.md
│   ├── SOC-2026-003-SuspiciousLogin.md
│   ├── SOC-2026-004-PrivEscalation.md
│   └── SOC-2026-005-MFABypass.md
└── screenshots/
├── 03-brute-force/
├── 04-impossible-travel/
├── 05-suspicious-login/
├── 06-privilege-escalation/
└── 07-mfa-bypass/

---

## KQL Detection Queries

Each scenario uses a custom KQL analytics rule running on a schedule in Microsoft Sentinel.
All queries are stored in the kql folder with comments explaining each line.

| Scenario | Key KQL Concepts Used |
|---|---|
| Brute Force | where, summarize, countif, bin, make_set |
| Impossible Travel | serialize, next(), extend, project |
| Suspicious Login | dcount, make_set, hourofday, summarize |
| Privilege Escalation | AuditLogs, extend, tostring, TargetResources |
| MFA Bypass | ResultType filtering, bin, summarize, count |

---

## Incident Reports

Each scenario produced a full SOC incident report covering:

- Incident metadata and classification
- Incident summary and simulation methodology
- Analyst observations in plain English
- KQL evidence with query results
- Impact assessment
- Severity justification
- True Positive or False Positive verdict with reasoning
- Recommended immediate, short term, and long term actions
- Analyst sign-off

All reports are available in the reports folder as markdown files.

---

## Key Findings Across Scenarios

**SOC-2026-001 Brute Force:** 32 failed login attempts followed by successful compromise in 64 seconds
confirming an extremely weak password on the Compromised User account with no MFA protection.

**SOC-2026-002 Impossible Travel:** Same account logged in from home broadband IPv4 and mobile 4G IPv6
within 5 minutes. Initial country-based detection rule was refined to IP-based detection after
identifying both connections geolocated to the UK.

**SOC-2026-003 Suspicious Login:** Three distinct IP addresses recorded across 30 successful logins
in 2 days including a ProtonVPN Netherlands exit node, confirming deliberate location masking.

**SOC-2026-004 Privilege Escalation:** Global Administrator role assigned to Standard User account
confirmed via AuditLogs with PerformedBy and TargetUser fields. Role removed and verified
via remediation query.

**SOC-2026-005 MFA Bypass:** 17 MFA denial events with ResultType 500121 in 17 minutes 47 seconds
against the highest privilege account in the tenant. Single source IP confirmed targeted attack.

---

## Technologies Used

- Microsoft Azure (Pay as you go)
- Microsoft Sentinel (SIEM and SOAR)
- Microsoft Entra ID (Identity Provider)
- Microsoft Entra ID Protection
- KQL (Kusto Query Language)
- Microsoft Authenticator (MFA — number matching)
- ProtonVPN (used in impossible travel and suspicious login simulations)
- GitHub (documentation and version control)

---

## Skills Demonstrated

- Cloud identity and access management in Microsoft Entra ID
- SIEM configuration, data connector setup, and log ingestion
- Custom KQL analytics rule development and iteration
- SOC incident investigation using investigation graphs and log analysis
- Threat detection mapped to MITRE ATT&CK framework
- Security control implementation including MFA, Conditional Access, and RBAC
- Professional SOC incident report writing
- Detection rule refinement based on real environment data

---

## Project Status

**Complete** — All five attack scenarios simulated, detected, investigated, and documented.

---

## By Daniel Owoeye-Wise
