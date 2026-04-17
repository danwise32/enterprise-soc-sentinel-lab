# 📸 Screenshots — 07: MFA Bypass via Prompt Bombing

These screenshots document the full end-to-end investigation of a simulated 
MFA bypass attack using prompt bombing — where 17 MFA push notification 
denial events were generated in 17 minutes against the highest-privilege 
account in the environment, in an attempt to fatigue the user into 
accidentally approving access.

**MITRE ATT&CK:** T1621 — Multi-Factor Authentication Request Generation (Credential Access)  
**Severity:** 🟠 Medium–High  
**Verdict:** True Positive  

---

## Screenshots in This Folder

| File | Description |
|------|-------------|
| `mfa-bypassanalytics-rule-mfa-bypass.png` | Custom Sentinel analytics rule configured to detect high-frequency MFA denial patterns |
| `mfa-bypassincident-detail.png` | Sentinel incident details panel showing alert metadata, severity, and full timeline of MFA denial events |
| `mfa-bypassinvestigation-graph.png` | Sentinel investigation graph mapping the 17 MFA denial events and the targeted account |
| `mfa-bypasskql-evidence-results.png` | KQL evidence query results showing all 17 MFA denial events within the 17-minute attack window |
| `mfa-bypasssentinel-incident-list.png` | Sentinel incident queue showing the MFA bypass alert raised and listed for investigation |

---

## Attack Summary

A prompt bombing attack was simulated against the Global Administrator 
account — the highest-privilege account in the environment. 17 MFA push 
notification requests were sent in 17 minutes, relying on MFA fatigue to 
trick the user into approving access. The custom KQL analytics rule detected 
the high-frequency MFA denial pattern and raised a Medium-High severity 
incident in Sentinel. All 17 events were confirmed as denial events — the 
attack did not succeed in gaining access.

## What This Demonstrates

- KQL detection logic identifying high-frequency MFA denial patterns
- Targeting of the highest-privilege account as an attacker priority
- Sentinel incident creation from repeated authentication failure events
- End-to-end SOC investigation confirming attack intent and failure
- Understanding of MFA fatigue as a real-world attack technique

## Key Finding

Standard push notification MFA is vulnerable to prompt bombing attacks. 
The attacker specifically targeted the Global Administrator account, 
understanding that a single approval from that account would grant 
complete control over the environment. The absence of number-matching 
MFA meant the user could approve a request without verifying the 
login attempt was legitimate.

## Remediation Applied

- MFA method switched from push notifications to number-matching MFA 
  — users must now enter a displayed number to approve, preventing 
  accidental approvals
- MFA fraud alerting enabled — any MFA denial triggers an alert to 
  the security team
- Conditional Access policy configured to block sign-ins after 
  repeated MFA failures
- Identity Protection risk policy enabled to automatically flag 
  accounts with anomalous MFA activity

---

## Related Files
- KQL detection query: [`/kql/mfa-bypass.kql`](../../kql/mfa-bypass.kql)
- SOC incident report: [`/reports`](../../reports)
