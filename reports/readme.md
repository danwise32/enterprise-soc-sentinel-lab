# 📄 SOC Incident Reports

This folder contains 5 structured SOC incident reports,one for each 
attack scenario simulated and investigated in this lab. Each report 
follows a professional SOC format covering the full investigation 
lifecycle from alert trigger through to remediation verification.

---

## Reports in This Folder

| File | Scenario | Severity | Verdict |
|------|----------|----------|---------|
| [`SOC-2026-001-BruteForce.md`](./SOC-2026-001-BruteForce.md) | Brute Force Attack | 🔴 High | True Positive |
| [`SOC-2026-002-ImpossibleTravel.md`](./SOC-2026-002-ImpossibleTravel.md) | Impossible Travel | 🔴 High | True Positive |
| [`SOC-2026-003-SuspiciousLogin.md`](./SOC-2026-003-SuspiciousLogin.md) | Suspicious Login Behaviour | 🟡 Medium | True Positive |
| [`SOC-2026-004-PrivEscalation.md`](./SOC-2026-004-PrivEscalation.md) | Privilege Escalation | 🔴 Critical | True Positive |
| [`SOC-2026-005-MFABypass.md`](./SOC-2026-005-MFABypass.md) | MFA Bypass via Prompt Bombing | 🟠 Medium–High | True Positive |

---

## Report Structure

Every report follows the same structured SOC format:

**1. Incident Summary**
High-level overview of what triggered the alert, the affected account, 
and the timeline of events.

**2. Alert Details**
Sentinel incident metadata including incident ID, severity, status, 
assigned analytics rule, and detection timestamp.

**3. Evidence**
KQL query output, AuditLog findings, and SigninLog data used to 
build the case during investigation.

**4. Investigation**
Step-by-step walkthrough of the Sentinel investigation graph analysis, 
entity mapping, and the reasoning used to reach a verdict.

**5. Verdict**
True Positive or False Positive classification with documented 
justification for the decision.

**6. Impact Assessment**
Analysis of the potential business impact if the incident had gone 
undetected — covering data exposure risk, privilege abuse potential, 
and operational disruption.

**7. Remediation Recommendations**
Specific, actionable security controls recommended to prevent 
recurrence, with reference to industry best practices and 
Microsoft security baselines.

**8. Remediation Verification**
Confirmation that remediation steps were applied, including KQL 
verification queries where applicable.

---

## Naming Convention

Reports follow the format: `SOC-YYYY-NNN-ScenarioName.md`

- `SOC` — Security Operations Centre report
- `YYYY` — Year the incident was investigated
- `NNN` — Sequential incident number
- `ScenarioName` — Short name identifying the attack scenario

---

## Related Files
- Screenshots evidence: [`/screenshots`](../screenshots)
- KQL detection queries: [`/kql`](../kql)
- Lab setup guide: [`/docs`](../docs)
- Main README: [`/README.md`](../README.md)
