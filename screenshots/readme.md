# 📸 Screenshots

This folder contains all visual evidence from the Enterprise SOC Monitoring 
& Identity Protection Lab. Screenshots are organised by scenario and cover 
the full SOC workflow — environment setup, attack simulation, detection, 
investigation, and remediation.

---

## Folder Structure

| Folder | Scenario | MITRE ATT&CK | Severity |
|--------|----------|--------------|----------|
| [`01-entra-setup`](./01-entra-setup) | Microsoft Entra ID Configuration | N/A — Setup | N/A |
| [`02-sentinel-setup`](./02-sentinel-setup) | Microsoft Sentinel Setup & Log Ingestion | N/A — Setup | N/A |
| [`03-brute-force`](./03-brute-force) | Brute Force Attack | T1110 — Credential Access | 🔴 High |
| [`04-impossible-travel`](./04-impossible-travel) | Impossible Travel | T1078 — Initial Access | 🔴 High |
| [`05-suspicious-login`](./05-suspicious-login) | Suspicious Login Behaviour | T1078 — Initial Access | 🟡 Medium |
| [`06-privilege-escalation`](./06-privilege-escalation) | Privilege Escalation | T1078.004 — Privilege Escalation | 🔴 Critical |
| [`07-mfa-bypass`](./07-mfa-bypass) | MFA Bypass via Prompt Bombing | T1621 — Credential Access | 🟠 Medium–High |

---

## What Each Folder Contains

Every scenario folder follows the same structure and includes screenshots of:

- **Analytics Rule** — the custom Sentinel detection rule configured for the scenario
- **KQL Results** — query output showing the evidence that triggered the alert
- **Sentinel Incident** — the incident raised in the Sentinel queue
- **Investigation Graph** — the Sentinel investigation graph mapping the attack chain
- **Incident Details** — full incident metadata including severity and timeline
- **Remediation Evidence** — confirmation that the remediation was applied

---

## How Screenshots Are Used

All screenshots in this folder are embedded directly in the main 
[`README.md`](../README.md) under their relevant scenario sections. 
Each subfolder also has its own `readme.md` explaining what each 
individual screenshot shows and why it matters.

---

## Setup Folders

### 01-entra-setup
Documents the full Microsoft Entra ID configuration including user 
persona creation, RBAC role assignments, MFA enforcement, and 
Conditional Access policies. This is the identity foundation that 
all attack scenarios are built on top of.

### 02-sentinel-setup
Documents the Microsoft Sentinel workspace configuration and Entra ID 
data connector setup — the log ingestion pipeline that feeds sign-in 
and audit logs into Sentinel for detection and investigation.

---

## Related Files
- Main README: [`/README.md`](../README.md)
- KQL detection queries: [`/kql`](../kql)
- SOC incident reports: [`/reports`](../reports)
- Lab setup guide: [`/docs`](../docs)
