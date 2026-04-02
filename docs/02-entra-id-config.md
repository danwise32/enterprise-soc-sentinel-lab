# Entra ID Configuration

## Lab Users

| Display Name | Username | Role | MFA | Notes |
|---|---|---|---|---|
| Lab Admin | labadmin@Cyberbulwork.onmicrosoft.com | Global Administrator | Enforced | High value target |
| Standard User | StandardUser@Cyberbulwork.onmicrosoft.com | Global Reader | Enforced | Regular employee simulation |
| Compromised User | CompromisedUser@Cyberbulwork.onmicrosoft.com | None | Disabled | Deliberately vulnerable account |

## Conditional Access Policies

### Policy 1 — Require MFA Lab Users
- Users: Lab Admin and Standard User included, Compromised User excluded
- Target resources: All cloud apps
- Grant: Require MFA
- Status: On

### Policy 2 — Block Legacy Authentication
- Users: All users
- Conditions: Legacy authentication clients
- Grant: Block access
- Status: On

### Policy 3 — Block High Risk Sign-ins
- Users: All users (admin excluded)
- Conditions: Sign-in risk — High
- Grant: Block access
- Status: On

## MFA Configuration

| Account | MFA Status | Reason |
|---|---|---|
| Lab Admin | Enforced | Admin accounts must have MFA |
| Standard User | Enforced | Simulates secured employee |
| Compromised User | Disabled | Simulates security gap |
