# Incident Report — SOC-2026-003
## Suspicious Login Behaviour — Multiple IPs Same User

| Field | Detail |
|---|---|
| Incident ID | SOC-2026-003 |
| Date and Time | 3 April 2026, 17:34 UTC |
| Classification | MEDIUM SEVERITY |
| Status | Confirmed True Positive |
| Analyst | Daniel Owoeye-Wise |
| Tenant | Cyberbulwork.onmicrosoft.com |
| Affected Account | standarduser@cyberbulwork.onmicrosoft.com |
| MITRE ATT&CK Tactic | Initial Access |
| MITRE ATT&CK Technique | T1078 Valid Accounts |
| Detection Method | Microsoft Sentinel Scheduled Analytics Rule |
| Incident Number | 34 (Sentinel) |
| Escalated To | Tier 2 SOC Analyst |

---

## 1. Incident Summary

Suspicious login behaviour refers to a pattern where the same user account records multiple logins from different IP addresses within a short window such as under 24 hours, and may also include logins at unusual hours or access to applications not previously used. It differs from impossible travel in terms of timeframe. Suspicious login behaviour analyses logs over a longer period while impossible travel focuses on a very short window where geographic movement would be physically impossible.

This scenario was generated organically across two days as a byproduct of the Scenario 2 impossible travel simulation. Three different IP addresses were recorded for the Standard User account. The first from the user's home broadband on Virgin Media. The second from a mobile phone on 4G during the impossible travel simulation. The third from a ProtonVPN Netherlands server connected during an earlier attempt. Even though the VPN login did not complete successfully, Entra ID still recorded the authentication attempt and captured the IP address in the sign-in logs.

An analytics rule was created in Sentinel to detect accounts with 3 or more distinct IP addresses within a 2 day window, running every hour at Medium severity. The rule fired and raised Incident 34.

---

## 2. Observations

The KQL summary query returned the following for the Standard User account:

| Field | Value |
|---|---|
| DistinctIPs | 3 |
| LoginCount | 30 |
| FirstSeen | 2026-04-01T17:38:40 UTC |
| LastSeen | 2026-04-02T12:33:23 UTC |
| IP 1 | 82.5.115.116 (IPv4, home broadband, Virgin Media, Allestree GB) |
| IP 2 | 2a04:4a43:4def:f1ecb11e:1192:a931:75aa (IPv6, mobile 4G, Lower Earley GB) |
| IP 3 | 185.184.195.126 (ProtonVPN, Netherlands) |

30 logins in 2 days is not normal for a standard employee and raises suspicion of an attack. The presence of the ProtonVPN Netherlands IP is the most significant finding because it indicates someone deliberately used an anonymising service to mask their real location when accessing the account.

I considered whether this could be innocent. If only the broadband IP and mobile IP were present I could have argued the user logged in from their laptop at home and phone while out. However the VPN IP from the Netherlands removes that innocent explanation. A standard user account has no business reason to be accessed through a commercial VPN exit node in another country.

It is also notable that even though the VPN login attempt did not fully complete, Entra ID still captured the IP address. This shows that even partial authentication attempts leave traces in the logs that a SOC analyst can detect.

---

## 3. KQL Detection Query
```kql
SigninLogs
| where TimeGenerated > ago(2d)
| where ResultType == 0
| summarize
    DistinctIPs = dcount(IPAddress),
    LoginCount = count(),
    IPList = make_set(IPAddress),
    FirstSeen = min(TimeGenerated),
    LastSeen = max(TimeGenerated),
    HoursList = make_set(hourofday(TimeGenerated))
    by UserPrincipalName
| where DistinctIPs >= 3
| project
    UserPrincipalName,
    DistinctIPs,
    LoginCount,
    IPList,
    FirstSeen,
    LastSeen,
    HoursList
| order by DistinctIPs desc
```

---

## 4. Severity Justification — Why Medium and Not High

The brute force attack in SOC-2026-001 showed 32 failed login attempts followed by a confirmed successful login. That pattern is unambiguous with almost no innocent explanation.

In this scenario 30 logins from 3 IPs over 2 days is suspicious but not immediately conclusive. The timeframe is longer and innocent explanations must be considered and ruled out before escalating. Medium severity means investigate before escalating. High severity means the evidence is clear enough to act immediately.

---

## 5. Impact Assessment

| Category | Assessment |
|---|---|
| Account status | Suspicious access pattern across 2 days from 3 different network types |
| VPN activity | ProtonVPN Netherlands IP recorded indicating deliberate location masking |
| Login volume | 30 successful logins in 2 days is above normal for a standard user |
| MFA protection | MFA enforced and completed on all successful logins |
| Privilege level | Standard user with Global Reader role |
| Data exposure | Microsoft 365 access during the 2 day window |
| Lateral movement | Not confirmed, requires further investigation |

---

## 6. Verdict

**Confirmed True Positive**

I first considered the innocent explanation. If only the home broadband IP and mobile IP were present I would have investigated further before confirming. However the ProtonVPN Netherlands IP removes any innocent explanation. A standard user account has no business reason to be accessed through a commercial VPN exit node in another country. The combination of 30 logins, three different network types, and a VPN IP confirms this as a True Positive.

---

## 7. Recommended Actions

**Immediate**
1. Revoke all active sessions for Standard User in Entra ID
2. Reset the Standard User account password
3. Block ProtonVPN IP 185.184.195.126 via Conditional Access Named Locations
4. Escalate to Tier 2 SOC analyst with all evidence

**Short term**
1. Contact Standard User to confirm whether they used a VPN and recognise all 30 logins
2. Review all activity during the 2 day window for data access or emails sent
3. Check whether the account accessed any new applications
4. Review logs for other accounts accessed from the same VPN IP

**Long term**
1. Configure Named Locations to flag logins from known VPN exit nodes
2. Implement Identity Protection to elevate risk scores for VPN logins
3. Set login frequency policy requiring re-authentication after set hours
4. Review and tune the analytics rule threshold based on normal user patterns

---

## 8. Sign-off

| Field | Detail |
|---|---|
| Analyst | Daniel Owoeye-Wise |
| Role | SOC Analyst |
| Date | 3 April 2026 |
| Status | Escalated to Tier 2 SOC Analyst |

*This report was produced as part of the Enterprise Security Monitoring and Identity Protection Lab. All simulations were conducted in a controlled lab environment for educational purposes only.*
