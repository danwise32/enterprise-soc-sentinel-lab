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

**What happened:** 32 failed login attempts against a user account. Account was compromised in 64 seconds.  
**Detection:** KQL analytics rule triggered on failed login threshold breach.  
**Key finding:** No MFA on target account enabled rapid compromise post-credential guess.  
**Remediation:** Enforce MFA organisation-wide; implement account lockout policy after 5 failed attempts.

---

### 2. Impossible Travel

**What happened:** Two successful logins from geographically distant locations just 6 minutes apart — physically impossible travel time.  
**Detection:** KQL query analysed sign-in locations and timestamps to flag geographic anomaly.  
**Key finding:** Credential reuse across regions indicated account compromise or VPN misuse.  
**Remediation:** Block legacy authentication; implement Conditional Access policies restricting sign-in by location.

---

### 3. Suspicious Login Behaviour

**What happened:** Login activity detected from 3 distinct IP addresses within a short window, including a ProtonVPN Netherlands exit node.  
**Detection:** KQL rule flagged multiple distinct IPs and off-hours login timing anomaly.  
**Key finding:** VPN usage to mask origin combined with unusual login hours indicated malicious intent.  
**Remediation:** Enforce Named Locations in Conditional Access; flag VPN exit node sign-ins for review.

---

### 4. Privilege Escalation

**What happened:** Global Administrator role silently assigned to a standard user account with no change request or approval.  
**Detection:** KQL query on AuditLogs detected unauthorised role assignment event.  
**Key finding:** No Privileged Identity Management (PIM) controls were in place to require approval for privileged role assignment.  
**Remediation:** Enable Privileged Identity Management (PIM); enforce just-in-time access for all admin roles.

---

### 5. MFA Bypass via Prompt Bombing

**What happened:** 17 MFA push notification denial events in 17 minutes against the highest-privilege account in the environment.  
**Detection:** KQL analytics rule triggered on high-frequency MFA denial pattern.  
**Key finding:** Attacker relied on MFA fatigue — repeated prompts hoping the user would accidentally approve.  
**Remediation:** Switch from push notifications to number-matching MFA; implement MFA fraud alerting.

---

## 🔍 Detection Engineering — KQL Highlights

All custom KQL queries are in the [`/kql`](./kql) folder. Each query includes inline comments explaining the detection logic.

Example — Brute Force detection logic:
```kql
// Detect accounts with 10+ failed sign-ins in a 10-minute window
SigninLogs
| where ResultType != "0"
| summarize FailedAttempts = count() by UserPrincipalName, bin(TimeGenerated, 10m)
| where FailedAttempts >= 10
| project TimeGenerated, UserPrincipalName, FailedAttempts
| order by FailedAttempts desc
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
