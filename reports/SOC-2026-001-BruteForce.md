# Incident Report — SOC-2026-001
## Brute Force Attack — Credential Compromise

**Incident ID:** SOC-2026-001
**Date:** 28 March 2026
**Severity:** High
**Status:** Confirmed True Positive
**Analyst:** Daniel Owoeye-Wise
**Tenant:** Cyberbulwork.onmicrosoft.com
**MITRE ATT&CK:** T1110 — Brute Force (Credential Access)

---

## Incident Summary

On 28 March 2026 at 16:56 UTC, Microsoft Sentinel detected a brute force attack against compromiseduser@cyberbulwork.onmicrosoft.com. A total of 32 failed login attempts were recorded from IP 82.5.115.116 over 90 minutes, followed by a successful login at 16:57 UTC. The account had no MFA and no Conditional Access policy applied.

---

## Observations

- 32 failed login attempts with ResultType 50126
- All attempts from single IP: 82.5.115.116 (GB)
- Successful login at 16:57 UTC from same IP
- No MFA configured on account
- Sentinel analytics rule automatically raised High severity incident

---

## Impact Assessment

- Account confirmed compromised
- Standard user: no elevated privileges
- Access to Microsoft 365 services during compromise window
- No evidence of lateral movement or data exfiltration

---

## Verdict

Confirmed True Positive: 32 automated failures from single IP followed by successful login is consistent with automated brute force tooling.

---

## Recommended Actions

**Immediate:**
1. Reset compromised user password
2. Revoke all active sessions
3. Block IP 82.5.115.116 via Conditional Access Named Locations

**Short-term:**
1. Enable and enforce MFA on this account
2. Remove account from Conditional Access policy exclusion list

**Long-term:**
1. Implement account lockout after 5 failed attempts
2. Enable Identity Protection risk-based Conditional Access
3. Audit all accounts missing MFA enforcement

