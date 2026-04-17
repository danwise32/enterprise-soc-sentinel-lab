# 📸 Screenshots — 05: Suspicious Login Behaviour

These screenshots document the full end-to-end investigation of a suspicious 
login behaviour incident — where login activity was detected from 3 distinct 
IP addresses within a short window, including a ProtonVPN Netherlands exit node, 
combined with off-hours login timing.

**MITRE ATT&CK:** T1078 — Valid Accounts (Initial Access)  
**Severity:** 🟡 Medium  
**Verdict:** True Positive  

---

## Screenshots in This Folder

| File | Description |
|------|-------------|
| `suspicious-loginincident-details.png` | Sentinel incident details panel showing alert metadata, severity, and timeline |
| `suspicious-logininvestigation-graph.png` | Sentinel investigation graph mapping the 3 login sources and their relationships |
| `suspicious-loginkql-distinct-ips.png` | KQL query results identifying 3 distinct IP addresses including a ProtonVPN exit node |
| `suspicious-loginsentinel-incidents-panel.png` | Sentinel incident queue showing the suspicious login alert raised for investigation |

---

## Attack Summary

Login activity was detected from 3 distinct IP addresses within a short 
time window. One of the IPs was identified as a ProtonVPN Netherlands exit 
node, indicating deliberate masking of the true login origin. The logins 
also occurred outside of normal business hours, adding further suspicion. 
The custom KQL analytics rule flagged the combination of multiple IPs and 
anomalous timing as a Medium severity incident.

## What This Demonstrates

- KQL detection logic identifying multiple distinct source IPs per user session
- Flagging VPN exit node usage as an indicator of suspicious behaviour
- Off-hours login anomaly detection
- Sentinel incident investigation using the investigation graph and SigninLogs

## Key Finding

The use of a ProtonVPN Netherlands exit node to mask the true origin of 
access, combined with logins from 3 separate IPs in a short window and 
outside business hours, indicated deliberate evasion behaviour consistent 
with an account takeover attempt.

## Remediation Applied

- Named Locations configured in Conditional Access to restrict unexpected 
  geographic sign-ins
- VPN exit node IP ranges flagged for enhanced monitoring
- Sign-in risk policy enabled in Entra ID Identity Protection
- User alerted and credentials reset as a precaution

---

## Related Files
- KQL detection query: [`/kql/suspicious-login.kql`](../../kql/suspicious-login.kql)
- SOC incident report: [`/reports`](../../reports)
