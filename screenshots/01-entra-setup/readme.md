# 📸 Screenshots — 01: Microsoft Entra ID Setup

These screenshots document the full configuration of Microsoft Entra ID 
as the identity provider for this SOC lab, including user persona creation, 
role assignments, MFA enforcement, and Conditional Access policies aligned 
to Zero Trust principles.

**Purpose:** Foundation layer of the SOC lab — all attack scenarios and 
detections are built on top of this identity configuration.

---

## Screenshots in This Folder

| File | Description |
|------|-------------|
| `setupca-policy-block-high-risk.png` | Conditional Access policy configured to automatically block sign-ins flagged as high risk by Entra ID Identity Protection |
| `setupca-policy-block-legacy-auth.png` | Conditional Access policy blocking all legacy authentication protocols — prevents attackers bypassing MFA via older auth methods |
| `setupca-policy-require-mfa.png` | Conditional Access policy enforcing MFA for all users across the environment |
| `setupcompromised-user-signin-no-mfa.png` | Compromised user persona configured without MFA — intentionally vulnerable account used as the target for brute force and other attack simulations |
| `setupcompromiseduser-role.png` | Role assignment for the compromised user persona — standard user permissions with no admin privileges |
| `setuplabadmin-role.png` | Role assignment for the lab admin persona — Global Administrator role used to simulate privilege escalation detection |
| `setupmfa-configuration.png` | MFA configuration in Entra ID using Microsoft Authenticator — enforced across admin and standard user personas |
| `setupstandarduser-role.png` | Role assignment for the standard user persona — baseline employee account used across multiple attack scenarios |

---

## Environment Design

Three user personas were created to simulate a realistic enterprise 
identity environment:

| Persona | Role | MFA | Purpose |
|---------|------|-----|---------|
| Lab Admin | Global Administrator | ✅ Enforced | Target for privilege escalation and MFA bypass scenarios |
| Standard User | Standard Member | ✅ Enforced | Used in impossible travel and suspicious login scenarios |
| Compromised User | Standard Member | ❌ None | Intentionally vulnerable — target for brute force simulation |

## What This Demonstrates

- Real-world enterprise identity configuration using Microsoft Entra ID
- Zero Trust aligned Conditional Access policies covering MFA, risk-based 
  access, and legacy auth blocking
- Deliberate security gap design — compromised user account created without 
  MFA to enable realistic attack simulation
- RBAC role assignments matching real enterprise privilege tiers

## Why It Matters

Every attack scenario in this lab depends on this Entra ID configuration. 
The Conditional Access policies, role assignments, and MFA settings 
determine what gets detected, what gets blocked, and what remains 
vulnerable — mirroring the decisions a real security team makes when 
hardening an enterprise identity environment.

---

## Related Files
- Sentinel setup: [`/screenshots/02-sentinel-setup`](../02-sentinel-setup)
- Lab setup guide: [`/docs`](../../docs)
