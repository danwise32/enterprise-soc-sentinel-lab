# Incident Report — SOC-2026-004
## Privilege Escalation — Unauthorised Admin Role Assignment

| Field | Detail |
|---|---|
| Incident ID | SOC-2026-004 |
| Date and Time | 10 April 2026, 14:53 UTC |
| Classification | HIGH SEVERITY |
| Status | Confirmed True Positive |
| Analyst | Daniel Owoeye-Wise |
| Tenant | Cyberbulwork.onmicrosoft.com |
| Affected Account | StandardUser@Cyberbulwork.onmicrosoft.com |
| Performed By | DanielOwoeye-Wise@Cyberbulwork.onmicrosoft.com |
| Role Assigned | Global Administrator |
| MITRE ATT&CK Tactic | Privilege Escalation |
| MITRE ATT&CK Technique | T1078.004 Cloud Accounts |
| Detection Method | Microsoft Sentinel Scheduled Analytics Rule |
| Escalated To | Tier 2 SOC Analyst |

---

## 1. Incident Summary

Privilege escalation occurs when a user account gains more permissions than it is authorised to have. In cloud environments this typically means a standard user being assigned an administrative role. Once an attacker has admin access they can create new accounts, reset passwords, disable security controls, or lock out legitimate administrators making it one of the most dangerous attack types in a cloud environment.

This scenario was simulated by signing in as the Global Admin account and assigning the Global Administrator role to the Standard User account. This simulates either an insider threat or an attacker who has compromised an admin account and is using it to elevate a second account as a backdoor for persistent access.

A scheduled analytics rule was created in Microsoft Sentinel to monitor AuditLogs for any role assignment operations. The rule queries the AuditLogs table every 5 minutes looking for Add member to role operations. When the Global Administrator role was assigned to Standard User the rule detected the AuditLog entry and raised a High severity incident in Sentinel.

---

## 2. Observations

The KQL investigation query run against the AuditLogs table confirmed the following:

| Field | Value |
|---|---|
| TimeGenerated | 10 April 2026, 14:53:48 UTC |
| OperationName | Add member to role |
| AssignedRole | Global Administrator |
| TargetUser | StandardUser@Cyberbulwork.onmicrosoft.com |
| PerformedBy | DanielOwoeye-Wise@Cyberbulwork.onmicrosoft.com |
| Result | Success |

The AuditLog entry confirmed that the Global Administrator role was successfully assigned to the Standard User account. This is the highest privilege role available in Microsoft Entra ID giving the account full control over the entire tenant including the ability to create accounts, reset passwords, modify security policies, and disable security controls.

The assignment was performed by the Global Admin account outside of any approved change management window. In a real organisation this would immediately raise a red flag because any assignment of the Global Administrator role should be documented, approved, and time limited using Privileged Identity Management.

The remediation verification query also confirmed the role was subsequently removed, showing the response action was completed successfully.

---

## 3. KQL Detection Query

```kql
AuditLogs
| where OperationName == "Add member to role"
| where Result == "success"
| extend PerformedBy = tostring(InitiatedBy.user.userPrincipalName)
| extend TargetUser = tostring(TargetResources[0].userPrincipalName)
| extend RoleAssigned = tostring(TargetResources[0].modifiedProperties[0].newValue)
| where RoleAssigned has "Global Administrator"
| project TimeGenerated, PerformedBy, TargetUser, RoleAssigned, Result
| order by TimeGenerated desc
```

### Remediation Verification Query

```kql
AuditLogs
| where TimeGenerated > ago(24h)
| where OperationName contains "Remove member from role"
| extend
    RemovedRole = tostring(TargetResources[0].displayName),
    TargetUser = tostring(TargetResources[1].userPrincipalName),
    PerformedBy = tostring(InitiatedBy.user.userPrincipalName)
| project
    TimeGenerated,
    RemovedRole,
    TargetUser,
    PerformedBy,
    Result
| order by TimeGenerated desc
```

---

## 4. Impact Assessment

| Category | Assessment |
|---|---|
| Account status | Standard User temporarily held Global Administrator role |
| Privilege level | Global Administrator — highest privilege in Entra ID tenant |
| Blast radius | Full tenant control including users, policies, and security settings |
| Duration | Role assigned at 14:53 UTC and removed after confirmation |
| Data exposure | Potential access to all tenant resources during the window |
| Lateral movement | Not detected but Global Admin access enables unrestricted movement |
| Business impact | Critical — Global Admin access represents complete tenant compromise |

---

## 5. Severity Justification — Why High

This incident is classified as High severity for the following reasons. The Global Administrator role grants complete control over the entire Entra ID tenant. An attacker holding this role can create new admin accounts, reset passwords for any user including other admins, disable MFA, remove Conditional Access policies, and effectively lock out all legitimate administrators.

Unlike the suspicious login scenario where innocent explanations needed to be ruled out, an unauthorised Global Administrator role assignment has almost no innocent explanation outside of a documented and approved change management process. The assignment was performed outside business hours with no corresponding change request making it unambiguous.

---

## 6. Verdict

**Confirmed True Positive**

The AuditLog entry confirmed a successful Global Administrator role assignment to a standard user account with no corresponding change management ticket or business justification. The assignment was performed outside of normal business hours. This is an unambiguous privilege escalation event and is classified as a confirmed True Positive.

In a real organisation this would be treated as a Critical incident rather than High due to the potential for complete tenant takeover. The High classification reflects the lab environment context.

---

## 7. Recommended Actions

**Immediate**
1. Remove the Global Administrator role from the affected account immediately
2. Revoke all active sessions for the elevated account
3. Review all actions performed by the account during the elevation window
4. Check whether any new accounts were created or passwords changed during the window

**Short term**
1. Review the admin account that performed the assignment to determine if it was compromised
2. Audit all current Global Administrator accounts and remove any that are not required
3. Implement Privileged Identity Management to eliminate standing admin roles
4. Require approval workflow for any privileged role assignment

**Long term**
1. Implement Just in Time access for all admin roles using PIM
2. Set maximum activation time for admin roles such as 2 hours with justification required
3. Require a second admin to approve any Global Administrator assignment
4. Set up automated Sentinel playbook to immediately alert on any admin role assignment in real time

---

## 8. Sign-off

| Field | Detail |
|---|---|
| Analyst | Daniel Owoeye-Wise |
| Role | SOC Analyst |
| Date | 9 April 2026 |
| Status | Escalated to Tier 2 SOC Analyst |
| Remediation | Global Administrator role removed and confirmed via AuditLogs |

*This report was produced as part of the Enterprise Security Monitoring and Identity Protection Lab. All simulations were conducted in a controlled lab environment for educational purposes only.*
