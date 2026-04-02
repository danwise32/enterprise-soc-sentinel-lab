# Incident Report — SOC-2026-002
## Impossible Travel Attack — Suspicious Login from Multiple IPs

| Field | Detail |
|---|---|
| Incident ID | SOC-2026-002 |
| Date and Time | 2 April 2026, 12:03 UTC |
| Classification | HIGH SEVERITY |
| Status | Confirmed True Positive |
| Analyst | Daniel Owoeye-Wise |
| Tenant | Cyberbulwork.onmicrosoft.com |
| Affected Account | standarduser@cyberbulwork.onmicrosoft.com |
| MITRE ATT&CK Tactic | Initial Access |
| MITRE ATT&CK Technique | T1078 Valid Accounts |
| Detection Method | Microsoft Sentinel Scheduled Analytics Rule |
| Incident Number | 3 (Sentinel) |
| Escalated To | Tier 2 SOC Analyst |

---

## 1. Incident Summary

This incident covers the simulation and detection of an impossible travel attack. An impossible travel attack involves an attacker gaining access to a user account and signing in from a different location to the legitimate user, or signing in from multiple locations simultaneously in a timeframe that would not be physically possible.

I first created an analytics rule in Microsoft Sentinel to detect this behaviour, configured the KQL query, set severity to High, mapped it to MITRE ATT&CK T1078 under Initial Access, and set it to run every 5 minutes.

The simulation used two end devices. I signed in as Standard User on my laptop using home broadband, authenticated with MFA and logged out. I then immediately repeated the sign-in on my phone using a different browser and a completely different ISP on 4G mobile data to simulate an attacker who has obtained the user credentials accessing the account from a different device and network. The analytics rule detected the discrepancy and raised a High severity incident in Sentinel.

---

## 2. Detection Rule Development and Iteration

### First Rule Version (Country Based Detection)

The first version detected logins from two different countries within a 2 hour window. When the simulation ran this rule did not fire because both logins came from the United Kingdom. My laptop broadband and my phone 4G both geolocated to GB so the country condition was never met.

### Updated Rule Version (IP Address Based Detection)

I edited the KQL query to detect logins from two different IP addresses within a 5 hour window. This fixed the issue because the two logins had completely different IP addresses from different network providers.

This taught me that detection rules must be adapted to the specific environment. A rule that works in one organisation may not work in another depending on network topology and ISP configuration. A good SOC analyst understands the data their environment produces and adapts detection logic accordingly.

---

## 3. Observations

The KQL results showed two distinct IP addresses for the same account within 5 minutes.

| Field | Login 1 (Legitimate) | Login 2 (Suspicious) |
|---|---|---|
| Time (UTC) | 12:03:03, 2 April 2026 | 12:07:45, 2 April 2026 |
| IP Address | 82.5.115.116 (IPv4) | 2a04:4a43:4def:... (IPv6) |
| Network type | Home broadband Virgin Media | Mobile 4G carrier |
| Location | Allestree, GB | Lower Earley, GB |
| Device | Laptop | Mobile phone |
| Result type | 0 Success | 0 Success |
| Time between logins | 4 minutes 42 seconds | |

The difference between IPv4 and IPv6 is significant. IPv4 82.5.115.116 is the home broadband format used by fixed providers. IPv6 2a04:4a43:4def... is the format used by mobile carriers. This means the two logins came from two fundamentally different types of network infrastructure. One from a fixed home broadband connection and one from a mobile 4G network, telling me two completely different devices on two different network types accessed the same account almost simultaneously.

---

## 4. KQL Detection Query
```kql
SigninLogs
| where TimeGenerated > ago(5h)
| where ResultType == 0
| project TimeGenerated, UserPrincipalName, IPAddress,
    CountryOrRegion = tostring(LocationDetails.countryOrRegion),
    City = tostring(LocationDetails.city)
| order by UserPrincipalName asc, TimeGenerated asc
| serialize
| extend
    NextLoginTime = next(TimeGenerated),
    NextIP = next(IPAddress),
    NextUser = next(UserPrincipalName)
| where UserPrincipalName == NextUser
| where IPAddress != NextIP
| where IPAddress != "" and NextIP != ""
| project UserPrincipalName, FirstLoginTime = TimeGenerated,
    FirstIP = IPAddress, SecondLoginTime = NextLoginTime, SecondIP = NextIP
```

---

## 5. Impact Assessment

| Category | Assessment |
|---|---|
| Account status | Suspicious concurrent access from two different network types |
| MFA protection | MFA completed on both logins confirming attacker had MFA access |
| Privilege level | Standard user with Global Reader role |
| Data exposure | Microsoft 365 access from both sessions |
| Lateral movement | Not confirmed, requires further investigation |

---

## 6. Verdict

**Confirmed True Positive**

This is a confirmed True Positive. The same account logged in from two different devices, two different IP addresses, one IPv4 broadband and one IPv6 mobile, and two different cities, all within under 5 minutes. No legitimate user can physically be in two different locations using two completely different network types within 5 minutes. The fact that MFA was completed on both logins means the attacker either had access to the MFA device or the user unknowingly approved a prompt they did not initiate.

---

## 7. Recommended Actions

**Immediate**
1. Block and revoke all active sessions for Standard User in Entra ID
2. Reset the Standard User account password
3. Block the suspicious IPv6 address via Conditional Access Named Locations
4. Notify supervisor and escalate to Tier 2 SOC analyst

**Short term**
1. Contact Standard User to confirm which session was legitimate
2. Review all activity during both sessions for data access or emails sent
3. Review MFA configuration to understand how attacker completed MFA
4. Switch from push notification MFA to number matching in Authenticator

**Long term**
1. Implement Identity Protection risk-based Conditional Access
2. Configure Named Locations to flag logins from unknown IP ranges
3. Enable Entra Identity Protection for impossible travel detections
4. Regularly review and refine analytics rules for the environment

---

## 8. Sign-off

| Field | Detail |
|---|---|
| Analyst | Daniel Owoeye-Wise |
| Role | SOC Analyst |
| Date | 2 April 2026 |
| Status | Escalated to Tier 2 SOC Analyst |

*This report was produced as part of the Enterprise Security Monitoring and Identity Protection Lab. All simulations were conducted in a controlled lab environment for educational purposes only.*
