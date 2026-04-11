# Incident Report — SOC-2026-005
## MFA Bypass Attempt — Repeated MFA Failures (Prompt Bombing)

| Field | Detail |
|---|---|
| Incident ID | SOC-2026-005 |
| Date and Time | 10 April 2026, 18:15 UTC |
| Classification | HIGH SEVERITY |
| Status | Confirmed True Positive |
| Analyst | Daniel Owoeye-Wise |
| Tenant | Cyberbulwork.onmicrosoft.com |
| Affected Account | labadmin@Cyberbulwork.onmicrosoft.com |
| MITRE ATT&CK Tactic | Credential Access |
| MITRE ATT&CK Technique | T1556.006 Multi-Factor Authentication |
| MFA Error Code | 500121 MFA denied by user |
| Detection Method | Microsoft Sentinel Scheduled Analytics Rule |
| Incident Numbers | 49, 50, 51 (Sentinel) |
| Escalated To | Tier 2 SOC Analyst |

---

## 1. Incident Summary

MFA fatigue, also known as prompt bombing, occurs when an attacker already has a valid username and password but cannot bypass the second authentication factor. Instead of attempting to hack or intercept the MFA token, the attacker repeatedly triggers MFA push notifications hoping the legitimate user will eventually approve one out of frustration or confusion. The attack exploits human behaviour rather than a technical vulnerability. The MFA system itself is working correctly but the user becomes the weak point.

This scenario was simulated against the Lab Admin account which holds the Global Administrator role, the highest privilege account in the tenant and therefore the highest value target for this type of attack. The simulation was carried out by repeatedly attempting to sign into the Azure Portal as Lab Admin. Each time the Microsoft Authenticator number matching screen appeared I tapped No it was not me on my phone or closed the browser tab without completing authentication. This was repeated across two sessions to simulate the sustained and aggressive nature of a real prompt bombing attack.

A scheduled analytics rule was created in Sentinel to detect 3 or more MFA failures with ResultType 500121 within a 30 minute window running every 5 minutes at High severity. The rule fired and raised three High severity incidents numbered 49, 50, and 51.

---

## 2. Observations

When I ran the KQL summary query the results immediately stood out.

| Field | Value |
|---|---|
| UserPrincipalName | labadmin@cyberbulwork.onmicrosoft.com |
| MFAFailureCount | 17 |
| FirstFailure | 10 April 2026, 18:15:16 UTC |
| LastFailure | 10 April 2026, 18:33:03 UTC |
| Attack Duration | 17 minutes 47 seconds |
| ResultType | 500121 MFA denied by user |
| IPAddresses | 82.5.115.116 (single source) |
| Account Role | Global Administrator |

17 MFA failures in under 18 minutes is not something that happens by accident. It is unusual for a legitimate user to fail to complete MFA 17 times in less than 20 minutes. This looked like someone trying to pummel their way into the account through sheer repetition, which is exactly what a prompt bombing attack looks like in the logs.

The fact that all 17 attempts came from a single IP address confirms this was a targeted attack from one source rather than a distributed attempt. The account targeted was Lab Admin with Global Administrator role. Attackers prioritise privileged accounts because compromising one gives them the keys to the entire tenant. The attacker already had the correct password for this account which tells me the credentials were previously obtained through phishing, credential stuffing, or the brute force attack documented in SOC-2026-001.

An important follow-up step is to check whether any attempt resulted in a successful login by running:

```kql
SigninLogs
| where TimeGenerated between (datetime(2026-04-10T18:15:00Z) .. datetime(2026-04-10T18:34:00Z))
| where UserPrincipalName == "labadmin@cyberbulwork.onmicrosoft.com"
| where ResultType == 0
| project TimeGenerated, UserPrincipalName, IPAddress, ResultType
```

If this returns rows the attack succeeded. If no rows are returned the MFA held.

---

## 3. KQL Detection Query

```kql
SigninLogs
| where TimeGenerated > ago(1h)
| where ResultType in ("500121", "50076", "50074", "500133")
| summarize
    MFAFailureCount = count(),
    FirstFailure = min(TimeGenerated),
    LastFailure = max(TimeGenerated),
    IPAddresses = make_set(IPAddress),
    Locations = make_set(Location)
    by UserPrincipalName, bin(TimeGenerated, 30m)
| where MFAFailureCount >= 3
| project
    UserPrincipalName,
    MFAFailureCount,
    FirstFailure,
    LastFailure,
    IPAddresses,
    Locations
| order by MFAFailureCount desc
```

---

## 4. Impact Assessment

| Category | Assessment |
|---|---|
| Account targeted | Lab Admin Global Administrator highest privilege account |
| Attack volume | 17 MFA denial events in 17 minutes 47 seconds from single IP |
| Attack method | Prompt bombing using number matching MFA with No it was not me |
| MFA system | MFA held, no confirmed successful login during attack window |
| Risk level | Critical, Global Admin targeted, full tenant compromise if attack succeeds |
| Attacker knowledge | Attacker had correct password confirming prior credential compromise |
| Business impact | High, sustained attack on highest privilege account with known credentials |

---

## 5. Verdict

**Confirmed True Positive**

17 MFA denial events with ResultType 500121 on a single privileged account within 18 minutes is not something that happens by accident. No legitimate user fails to complete MFA 17 times in under 20 minutes. The volume, speed, and consistency of failures from a single IP confirms this was an automated or manually sustained prompt bombing attack.

The account targeted was the Global Administrator account making this significantly more serious than if it had been a standard user. The attacker already had the correct password meaning MFA was the only barrier between the attacker and complete tenant control. The fact that the attacker generated 17 denial events rather than giving up indicates a determined and targeted attack.

---

## 6. Recommended Actions

**Immediate**
1. Temporarily block sign-ins for the Lab Admin account while investigation is ongoing
2. Check for ResultType 0 in the attack window to determine if any prompt was approved
3. Revoke all active sessions for the Lab Admin account
4. Reset the Lab Admin password since attacker had correct credentials
5. Escalate to Tier 2 SOC analyst with all KQL evidence

**Short term**
1. Investigate how the attacker obtained the Lab Admin password
2. Review all Lab Admin activity in the 24 hours preceding the attack
3. Enable MFA fraud alerts so No it was not me responses automatically flag the account
4. Configure additional context in number matching to make prompt bombing harder

**Long term**
1. Switch Lab Admin to FIDO2 hardware key or passkey which is fully prompt bombing resistant
2. Implement Privileged Identity Management so Global Administrator requires just in time activation
3. Enable Identity Protection risk based Conditional Access for privileged accounts
4. Restrict Lab Admin logins to trusted named locations only

---

## 7. Sign-off

| Field | Detail |
|---|---|
| Analyst | Daniel Owoeye-Wise |
| Role | SOC Analyst |
| Date | 10 April 2026 |
| Status | Escalated to Tier 2 SOC Analyst |
| Next Steps | Await Tier 2 confirmation of whether attack succeeded and remediation completed |

*This report was produced as part of the Enterprise Security Monitoring and Identity Protection Lab. All simulations were conducted in a controlled lab environment for educational purposes only.*
