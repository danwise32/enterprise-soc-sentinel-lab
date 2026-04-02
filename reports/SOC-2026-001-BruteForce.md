# Incident Report — SOC-2026-001
## Brute Force Attack — Credential Compromise

| Field | Detail |
|---|---|
| Incident ID | SOC-2026-001 |
| Date and Time | 28 March 2026, 16:56 UTC |
| Classification | HIGH SEVERITY |
| Status | Confirmed True Positive |
| Analyst | Daniel Owoeye-Wise |
| Tenant | Cyberbulwork.onmicrosoft.com |
| Affected Account | compromiseduser@cyberbulwork.onmicrosoft.com |
| MITRE ATT&CK Tactic | Credential Access |
| MITRE ATT&CK Technique | T1110 Brute Force |
| Detection Method | Microsoft Sentinel Scheduled Analytics Rule |
| Escalated To | Tier 2 SOC Analyst |

---

## 1. Incident Summary

For this project I created three user accounts in Microsoft Entra ID to simulate real-world attack scenarios against an organisation's cloud infrastructure, acting in the role of a SOC analyst. Two accounts were secured with MFA and Conditional Access policies. The third account, Compromised User, was deliberately left without MFA and excluded from Conditional Access to simulate a common misconfiguration seen in real organisations.

I deployed Microsoft Sentinel connected to a Log Analytics Workspace and configured the Entra ID data connector to ingest sign-in and audit logs in real time. I built a scheduled analytics rule mapped to MITRE ATT&CK T1110 Brute Force under the Credential Access tactic, set to High severity, configured to detect five or more failed login attempts against the same account within a defined time window running every five minutes.

During the brute force simulation against the Compromised User account the analytics rule fired and automatically raised a High severity incident in Sentinel. I opened the investigation graph, reviewed the timeline and alert details, ran KQL queries against the SigninLogs table to gather evidence, confirmed a successful attack and escalated to Tier 2.

---

## 2. Observations

When I ran the KQL queries I noticed two separate activity timelines for the Compromised User account.

### Timeline 1 — 24 March 2026 (Administrative Activity — Ruled Out)

Events between 18:27 and 19:24 UTC showed ResultType 50072 appearing multiple times. ResultType 50072 means the event was caused by an administrator making configuration changes, not by an external attacker. This was activity from when I was setting up the lab. I ruled this timeline out as administrative noise.

### Timeline 2 — 28 March 2026 (Confirmed Brute Force Attack)

| Time (UTC) | ResultType | Description | Significance |
|---|---|---|---|
| 28 Mar 16:56:41 | 50126 | Invalid credentials | First failed attempt — attack begins |
| 28 Mar 16:57:45 | 0 | Successful authentication | Account compromised |
| 28 Mar 16:58 to 16:59 | 0 | Successful logins | Attacker active inside account |
| 28 Mar 17:48 to 17:49 | 50126 | Invalid credentials | Second wave of failed attempts |

The most significant finding was the speed of compromise. The first failed login was at 16:56:41 and the attacker successfully authenticated at 16:57:45, just 64 seconds later, across 32 total failed attempts. This tells me the account had an extremely weak password appearing very early in a common password wordlist.

After gaining access the attacker remained active between 16:58 and 16:59 UTC. The second wave at 17:48 suggests the attacker's automated tool continued running, the attacker lost their session and re-authenticated, or the password was reset during the compromise window.

All 96 events across both timelines originated from a single IP address 82.5.115.116, confirming a targeted single-source attack.

---

## 3. KQL Detection Query
```kql
SigninLogs
| where TimeGenerated > ago(1h)
| where ResultType != 0
| where ResultType in ("50126", "50053", "50055")
| summarize
    FailureCount = count(),
    StartTime = min(TimeGenerated),
    EndTime = max(TimeGenerated),
    IPAddresses = make_set(IPAddress),
    Locations = make_set(Location)
    by UserPrincipalName, bin(TimeGenerated, 10m)
| where FailureCount >= 5
| project StartTime, EndTime, UserPrincipalName, FailureCount, IPAddresses, Locations
```

### Query Results

| Field | Value |
|---|---|
| UserPrincipalName | compromiseduser@cyberbulwork.onmicrosoft.com |
| FailureCount | 32 |
| FirstAttempt | 28 March 2026, 16:56:41 UTC |
| LastAttempt | 28 March 2026, 18:16:49 UTC |
| IP Address | 82.5.115.116 |
| Geolocation | GB (United Kingdom) |

---

## 4. Impact Assessment

| Category | Assessment |
|---|---|
| Account status | Confirmed compromised |
| MFA protection | None configured on this account |
| Conditional Access | Not applied, account was excluded |
| Privilege level | Standard user, no elevated permissions |
| Post-compromise access | Attacker active for approximately 2 hours |
| Data exposure | Access to Microsoft 365 services during window |
| Lateral movement | Not confirmed at time of report |

---

## 5. Verdict

**Confirmed True Positive**

Classified as True Positive based on three specific pieces of evidence. First, 32 failed attempts from a single IP in under 90 minutes is consistent with automated brute force tooling, not a user forgetting their password. Second, all attempts from the same IP 82.5.115.116, a targeted attack not an accidental lockout. Third and most critically a successful login followed the failures confirming the account was actually compromised.

If MFA had been enforced the attacker would have needed the physical token or authenticated device belonging to the user. The correct password alone would not have been enough.

---

## 6. Recommended Actions

**Immediate**
1. Revoke all active sessions for the Compromised User account in Entra ID
2. Reset the account password
3. Disable the account pending investigation
4. Block IP 82.5.115.116 via Conditional Access Named Locations

**Short term**
1. Enable and enforce MFA on this account
2. Remove the account from the Conditional Access policy exclusion list
3. Review all activity performed during the compromise window

**Long term**
1. Implement account lockout after 5 failed attempts
2. Enable Identity Protection risk-based Conditional Access
3. Audit all accounts missing MFA enforcement

---

## 7. Sign-off

| Field | Detail |
|---|---|
| Analyst | Daniel Owoeye-Wise |
| Role | SOC Analyst |
| Date | 28 March 2026 |
| Status | Escalated to Tier 2 SOC Analyst |

*This report was produced as part of the Enterprise Security Monitoring and Identity Protection Lab. All simulations were conducted in a controlled lab environment for educational purposes only.*
