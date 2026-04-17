# 🛡️ Enterprise SOC Monitoring & Identity Protection Lab

> A hands-on cloud-based Security Operations Centre built on Microsoft Azure, simulating real-world enterprise threat detection and identity security using Microsoft Sentinel and Microsoft Entra ID.

**Built by:** Daniel Owoeye-Wise — Cybersecurity Analyst | MSc Cyber Security | CompTIA Security+  
**GitHub:** [danwise32](https://github.com/danwise32) · **LinkedIn:** [daniel-owoeye-wise](https://www.linkedin.com/in/daniel-owoeye-wise-7157451b4)

---

## 📌 Project Overview

This project simulates a real-world enterprise SOC environment from the ground up. Using Microsoft Azure (free tier), I configured Microsoft Entra ID with three user personas, connected identity logs to Microsoft Sentinel, and built custom KQL detection rules for 5 attack scenarios — each fully investigated and documented as a structured SOC incident report.

**The goal:** Demonstrate the full SOC analyst workflow — from environment setup and detection engineering, through to incident investigation, verdict, and remediation recommendations.

---

## 🗂️ How to Navigate This Repo

| Folder | Contents |
|--------|----------|
| [`/docs`](./docs) | Lab setup guide — step-by-step environment build walkthrough |
| [`/kql`](./kql) | Custom KQL detection queries for all 5 attack scenarios |
| [`/reports`](./reports) | 5 structured SOC incident reports with verdicts and remediation |
| [`/screenshots`](./screenshots) | Evidence screenshots from Sentinel investigations |

**Suggested reading order:**
1. Start with `/docs` to understand how the environment was built
2. Review `/kql` to see the detection logic for each scenario
3. Read `/reports` for the full SOC investigation findings
4. Check `/screenshots` for visual evidence from each incident

---

## 🏗️ Environment Setup

### Microsoft Entra ID — Identity Configuration

Three user personas were created to simulate a realistic enterprise identity environment:

| Persona | Role | MFA | Purpose |
|---------|------|-----|---------|
| Lab Admin | Global Administrator | ✅ Enforced | Target for privilege escalation and MFA bypass scenarios |
| Standard User | Standard Member | ✅ Enforced | Used in impossible travel and suspicious login scenarios |
| Compromised User | Standard Member | ❌ None | Intentionally vulnerable — target for brute force simulation |

**MFA Configuration**

![MFA Configuration](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/01-entra-setup/setupmfa-configuration.png)

**Conditional Access — Require MFA Policy**

![Conditional Access MFA Policy](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/01-entra-setup/setupca-policy-require-mfa.png)

**Conditional Access — Block Legacy Authentication**

![Block Legacy Auth Policy](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/01-entra-setup/setupca-policy-block-legacy-auth.png)

**Conditional Access — Block High Risk Sign-ins**

![Block High Risk Policy](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/01-entra-setup/setupca-policy-block-high-risk.png)

---

### Microsoft Sentinel — SIEM Setup

**Entra ID Data Connector — Log Ingestion Configured**

![Entra ID Connector](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/02-sentinel-setup/entra-id-connector-config.png)

---

## ⚔️ Attack Scenarios Simulated & Investigated

All 5 scenarios were simulated end-to-end: attack executed → KQL rule triggered → Sentinel alert fired → full investigation conducted → incident report written.

| # | Scenario | MITRE ATT&CK Tactic | Severity | Verdict |
|---|----------|-------------------|----------|---------|
| 1 | [Brute Force Attack](#1-brute-force-attack) | Credential Access (T1110) | 🔴 High | True Positive |
| 2 | [Impossible Travel](#2-impossible-travel) | Initial Access (T1078) | 🔴 High | True Positive |
| 3 | [Suspicious Login Behaviour](#3-suspicious-login-behaviour) | Initial Access (T1078) | 🟡 Medium | True Positive |
| 4 | [Privilege Escalation](#4-privilege-escalation) | Privilege Escalation (T1078.004) | 🔴 Critical | True Positive |
| 5 | [MFA Bypass via Prompt Bombing](#5-mfa-bypass-via-prompt-bombing) | Credential Access (T1621) | 🟠 Medium–High | True Positive |

---

### 1. Brute Force Attack

**What happened:** 32 failed login attempts against a user account with no MFA. Account was compromised in 64 seconds.  
**Detection:** KQL analytics rule triggered on failed login threshold breach.  
**Key finding:** Absence of MFA enabled rapid compromise once credentials were guessed.  
**Remediation:** MFA enforced organisation-wide; account lockout after 5 failed attempts.

**Analytics Rule**

![Brute Force Analytics Rule](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/03-brute-force/brute-forceanalytics-rule-brute-force.png)

**Failed Login Attempts in SigninLogs**

![Failed Login Attempts](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/03-brute-force/brute-forcefailed-login-attempt.png)

**KQL Detection Results**

![KQL Brute Force Results](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/03-brute-force/brute-forcekql-brute-force-results.png)

**Sentinel Incident Raised**

![Sentinel Incident](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/03-brute-force/brute-forcesentinel-incident-raised.png)

**Investigation Graph**

![Investigation Graph](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/03-brute-force/brute-forceinvestigation-graph.png)

**Successful Login After Compromise**

![Successful Login After Brute Force](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/03-brute-force/brute-forcesuccessful-login-after-brute-force.png)

---

### 2. Impossible Travel

**What happened:** Two successful logins from geographically distant locations just 6 minutes apart — physically impossible travel time.  
**Detection:** KQL query analysed sign-in locations and timestamps to flag geographic anomaly.  
**Key finding:** Credential reuse across regions indicated account compromise.  
**Remediation:** Conditional Access policies restricting sign-in by Named Locations implemented.

**Analytics Rule**

![Impossible Travel Analytics Rule](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/04-impossible-travel/impossible-travelanalytics-rule.png)

**Login 1 — UK Laptop**

![Login 1 UK](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/04-impossible-travel/impossible-travellogin-1-uk-laptop.png)

**Login 2 — Mobile Device**

![Login 2 Mobile](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/04-impossible-travel/impossible-travellogin-2-mobile.jpeg)

**KQL Detection Results**

![KQL Detection](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/04-impossible-travel/impossible-travelkql-detection-results.png)

**Investigation Graph**

![Investigation Graph](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/04-impossible-travel/impossible-travelinvestigation-graph.png)

**Sentinel Both Incidents**

![Sentinel Incidents](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/04-impossible-travel/impossible-travelsentinel-both-incidents..png)

---

### 3. Suspicious Login Behaviour

**What happened:** Login activity from 3 distinct IPs including a ProtonVPN Netherlands exit node, combined with off-hours timing.  
**Detection:** KQL rule flagged multiple distinct IPs and anomalous login timing.  
**Key finding:** VPN usage to mask origin combined with unusual hours indicated malicious intent.  
**Remediation:** Named Locations enforced in Conditional Access; VPN exit nodes flagged for monitoring.

**KQL Distinct IPs Results**

![KQL Distinct IPs](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/05-suspicious-login/suspicious-loginkql-distinct-ips.png)

**Incident Details**

![Incident Details](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/05-suspicious-login/suspicious-loginincident-details.png)

**Investigation Graph**

![Investigation Graph](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/05-suspicious-login/suspicious-logininvestigation-graph.png)

**Sentinel Incidents Panel**

![Sentinel Incidents](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/05-suspicious-login/suspicious-loginsentinel-incidents-panel.png)

---

### 4. Privilege Escalation

**What happened:** Global Administrator role silently assigned to a standard user with no approval or change request.  
**Detection:** KQL query on AuditLogs detected unauthorised role assignment event.  
**Key finding:** No PIM controls in place meant privileged roles could be assigned silently.  
**Remediation:** PIM enabled; just-in-time access enforced for all admin roles.

**Analytics Rule**

![Privilege Escalation Analytics Rule](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/06-privilege-escalation/privilege-escalationanalytics-rule-saved.png)

**KQL AuditLog Results — Role Assignment Detected**

![KQL AuditLog](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/06-privilege-escalation/privilege-escalationkql-auditlog.png)

**Role Assignment Evidence**

![Role Assignment](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/06-privilege-escalation/privilege-escalationrole-assignment.png)

**Investigation Graph**

![Investigation Graph](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/06-privilege-escalation/privilege-escalationinvestigation-graph.png)

**Role Removed — Remediation Verified**

![Role Removed](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/06-privilege-escalation/privilege-escalationrole-removed.png)

**Sentinel Incident List**

![Sentinel Incident List](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/06-privilege-escalation/privilege-escalationsentinel-incident-list.png)

---

### 5. MFA Bypass via Prompt Bombing

**What happened:** 17 MFA push notification denial events in 17 minutes against the highest-privilege account.  
**Detection:** KQL analytics rule triggered on high-frequency MFA denial pattern.  
**Key finding:** Standard push MFA is vulnerable to fatigue attacks — no number-matching was in place.  
**Remediation:** Switched to number-matching MFA; MFA fraud alerting enabled.

**Analytics Rule**

![MFA Bypass Analytics Rule](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/07-mfa-bypass/mfa-bypassanalytics-rule-mfa-bypass.png)

**KQL Evidence — 17 Denial Events**

![KQL Evidence](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/07-mfa-bypass/mfa-bypasskql-evidence-results.png)

**Incident Detail**

![Incident Detail](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/07-mfa-bypass/mfa-bypassincident-detail.png)

**Investigation Graph**

![Investigation Graph](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/07-mfa-bypass/mfa-bypassinvestigation-graph.png)

**Sentinel Incident List**

![Sentinel Incident List](https://raw.githubusercontent.com/danwise32/enterprise-soc-sentinel-lab/main/screenshots/07-mfa-bypass/mfa-bypasssentinel-incident-list.png)

---

## 🔍 Detection Engineering — KQL Highlights

All custom KQL queries are in the [`/kql`](./kql) folder. Each query includes inline comments explaining the detection logic. Every scenario has three queries: Detection Rule, Investigation Query, and Remediation Verification.

Example — Privilege Escalation detection logic:
```kql
// Detects privileged admin role assignments to any user account
// MITRE ATT&CK: T1078.004 — Privilege Escalation
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

## 🏗️ Environment Architecture

```
Microsoft Azure (Free Tier)
│
├── Microsoft Entra ID
│   ├── User Personas: Admin · Standard · Compromised
│   ├── RBAC role assignments
│   ├── MFA enforcement (Microsoft Authenticator)
│   └── Conditional Access policies (Zero Trust aligned)
│
├── Log Analytics Workspace
│   ├── Entra ID Sign-in Logs
│   └── Entra ID Audit Logs
│
└── Microsoft Sentinel
    ├── Data connectors (Entra ID → Sentinel)
    ├── Custom KQL analytics rules (5 scenarios)
    ├── Incident queue
    └── Investigation graph
```

---

## 🛠️ Technologies Used

![Microsoft Azure](https://img.shields.io/badge/Microsoft_Azure-0089D6?style=flat&logo=microsoft-azure&logoColor=white)
![Microsoft Sentinel](https://img.shields.io/badge/Microsoft_Sentinel-0078D4?style=flat&logo=microsoft&logoColor=white)
![Entra ID](https://img.shields.io/badge/Microsoft_Entra_ID-0078D4?style=flat&logo=microsoft&logoColor=white)
![KQL](https://img.shields.io/badge/KQL-Kusto_Query_Language-blue?style=flat)

| Tool | Purpose |
|------|---------|
| Microsoft Azure | Cloud infrastructure (free tier) |
| Microsoft Sentinel | SIEM — log ingestion, analytics rules, incident management |
| Microsoft Entra ID | Identity provider — users, RBAC, MFA, Conditional Access |
| KQL (Kusto Query Language) | Custom detection queries and investigation evidence |
| Microsoft Authenticator | MFA enforcement and prompt bombing simulation |
| MITRE ATT&CK Framework | Mapping detection rules to real-world threat tactics |

---

## 📋 Key Skills Demonstrated

- ✅ Cloud SOC environment build and configuration
- ✅ Identity & Access Management (IAM) — RBAC, MFA, Conditional Access, Zero Trust
- ✅ SIEM configuration and log ingestion pipeline setup
- ✅ Detection engineering — custom KQL analytics rules
- ✅ MITRE ATT&CK mapping for 5 attack scenarios
- ✅ End-to-end SOC incident investigation workflow
- ✅ Structured incident reporting with impact assessment and remediation
- ✅ Privileged Identity Management (PIM) principles

---

## 📄 SOC Incident Reports

All 5 reports are in the [`/reports`](./reports) folder. Each report follows a structured SOC format:

- **Incident Summary** — what triggered the alert
- **Evidence** — KQL query output and AuditLog findings
- **Investigation** — Sentinel investigation graph analysis
- **Verdict** — True Positive / False Positive with reasoning
- **Impact Assessment** — potential business impact if undetected
- **Remediation Recommendations** — specific mitigations and controls

---

## 🚀 Project Status

✅ **Completed** — All 5 scenarios simulated, detected, investigated, and documented.

---

## 📬 Contact

If you're a recruiter, hiring manager, or fellow cybersecurity professional and want to discuss this project:

- **LinkedIn:** [Daniel Owoeye-Wise](https://www.linkedin.com/in/daniel-owoeye-wise-7157451b4)
- **Email:** danielwise997@gmail.com
- **GitHub:** [danwise32](https://github.com/danwise32)

---

*This project was built independently as a portfolio piece to demonstrate practical SOC analyst capabilities. All simulations were conducted in a controlled, isolated Azure environment.*
