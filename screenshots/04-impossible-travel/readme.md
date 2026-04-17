# 📸 Screenshots — 04: Impossible Travel

These screenshots document the full end-to-end investigation of a simulated 
impossible travel attack — where two successful logins from geographically 
distant locations occurred just 6 minutes apart, making physical travel 
between them impossible.

**MITRE ATT&CK:** T1078 — Valid Accounts (Initial Access)  
**Severity:** 🔴 High  
**Verdict:** True Positive  

---

## Screenshots in This Folder

| File | Description |
|------|-------------|
| `impossible-travelanalytics-rule.png` | Custom Sentinel analytics rule configured to detect geographically impossible login patterns |
| `impossible-travelincident-details.png` | Sentinel incident details panel showing alert metadata, assigned severity, and timeline |
| `impossible-travelinvestigation-graph.png` | Sentinel investigation graph mapping the two login events and their geographic relationship |
| `impossible-travelkql-detection-results.png` | KQL detection query results identifying the two sign-in locations and timestamp gap |
| `impossible-travelkql-evidence-results.png` | KQL evidence query output used during investigation to confirm the anomaly |
| `impossible-travellogin-1-uk-laptop.png` | First login — UK-based laptop sign-in recorded in SigninLogs |
| `impossible-travellogin-2-mobile.jpeg` | Second login — mobile device sign-in from a different geographic location 6 minutes later |
| `impossible-travelsentinel-both-incidents.png` | Sentinel incident queue showing both login events raised as a combined incident |

---

## Attack Summary

Two successful logins were simulated from geographically distant locations 
within a 6-minute window — physically impossible travel time. The first login 
originated from a UK-based laptop and the second from a mobile device in a 
separate location. The custom KQL analytics rule detected the geographic 
anomaly and raised a High severity incident in Sentinel.

## What This Demonstrates

- Custom KQL detection logic analysing sign-in geography and timestamps
- Sentinel incident creation from correlated multi-event detection
- End-to-end SOC investigation using SigninLogs and the investigation graph
- Identifying credential compromise through behavioural anomaly detection

## Key Finding

The two logins used the same valid credentials but from locations that made 
physical travel impossible. This indicated either credential compromise or 
unauthorised VPN/proxy usage to mask the true origin of access.

## Remediation Applied

- Conditional Access policy implemented to restrict sign-ins by Named Locations
- Legacy authentication protocols blocked
- Affected account credentials reset and session tokens revoked
- Sign-in risk policy enabled in Entra ID Identity Protection

---

## Related Files
- KQL detection query: [`/kql/impossible-travel.kql`](../../kql/impossible-travel.kql)
- SOC incident report: [`/reports`](../../reports)
