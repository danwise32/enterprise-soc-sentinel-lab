# 📸 Screenshots — 03: Brute Force Attack

These screenshots document the full end-to-end investigation of a simulated 
brute force attack — from the Sentinel analytics rule that detected it, through 
to the investigation and confirmed account compromise.

**MITRE ATT&CK:** T1110 — Brute Force (Credential Access)  
**Severity:** 🔴 High  
**Verdict:** True Positive  

---

## Screenshots in This Folder

| File | Description |
|------|-------------|
| `brute-forceanalytics-rule-brute-force.png` | Custom Sentinel analytics rule configured to detect brute force login attempts |
| `brute-forcefailed-login-attempt.png` | Failed login attempts visible in SigninLogs — 32 failures recorded against the target account |
| `brute-forceinvestigation-graph.png` | Sentinel investigation graph showing the attack chain and entity relationships |
| `brute-forcekql-brute-force-results.png` | KQL query results showing failed login threshold breach that triggered the alert |
| `brute-forcesentinel-incident-raised.png` | Sentinel incident queue showing the brute force alert raised and assigned for investigation |
| `brute-forcesuccessful-login-after-brute-force.png` | Successful login following the brute force attempts — confirms account compromise in 64 seconds |

---

## Attack Summary

A brute force attack was simulated against a user account with no MFA enabled. 
32 failed login attempts were made in quick succession, triggering the custom 
KQL analytics rule. The account was successfully compromised 64 seconds after 
the attack began.

## What This Demonstrates

- Custom KQL detection rule triggering on failed login thresholds
- Sentinel incident creation and assignment workflow
- End-to-end investigation using SigninLogs and the investigation graph
- Identifying the exact moment of compromise via successful post-attack login

## Key Finding

The absence of MFA on the target account was the critical vulnerability. 
Once the correct credentials were guessed, there was no secondary control 
to prevent access.

## Remediation Applied

- MFA enforced organisation-wide via Conditional Access policy
- Account lockout policy implemented after 5 failed login attempts
- Affected account credentials reset

---

## Related Files
- KQL detection query: [`/kql/brute-force.kql`](../../kql/brute-force.kql)
- SOC incident report: [`/reports`](../../reports)
