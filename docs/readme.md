# 📚 Documentation — Lab Setup Guides

This folder contains the full step-by-step documentation for building 
the Enterprise SOC Monitoring & Identity Protection Lab from scratch. 
The guides cover everything from initial Azure subscription setup through 
to Microsoft Sentinel configuration and log ingestion.

---

## Documents in This Folder

| File | Description |
|------|-------------|
| [`01-lab-setup.md`](./01-lab-setup.md) | Initial Azure environment setup — subscription, resource groups, and Log Analytics Workspace creation |
| [`02-entra-id-config.md`](./02-entra-id-config.md) | Full Microsoft Entra ID configuration — user personas, RBAC role assignments, MFA enforcement, and Conditional Access policies |
| [`03-sentinel-setup.md`](./03-sentinel-setup.md) | Microsoft Sentinel workspace setup — data connectors, log ingestion from Entra ID, and analytics rule configuration |

---

## Suggested Reading Order

Follow the guides in numbered order — each builds on the previous:

**Step 1 — [`01-lab-setup.md`](./01-lab-setup.md)**
Start here. Sets up the Azure foundation — subscription activation, 
resource group creation, and Log Analytics Workspace deployment. 
Everything else depends on this being in place first.

**Step 2 — [`02-entra-id-config.md`](./02-entra-id-config.md)**
Configure Microsoft Entra ID with three user personas (Admin, Standard, 
Compromised), RBAC role assignments, MFA enforcement via Microsoft 
Authenticator, and Conditional Access policies aligned to Zero Trust 
principles.

**Step 3 — [`03-sentinel-setup.md`](./03-sentinel-setup.md)**
Deploy Microsoft Sentinel, connect the Entra ID data connector to begin 
ingesting sign-in and audit logs, and configure the custom KQL analytics 
rules for all 5 attack scenarios.

---

## Prerequisites

Before following these guides you will need:

- A Microsoft Azure account (free tier is sufficient)
- A Microsoft 365 or Entra ID tenant
- Microsoft Entra ID P2 licence (available on free trial)
- Basic familiarity with the Azure portal

---

## Cost

This entire lab was built using the **Microsoft Azure free tier**. 
No paid resources are required. The only cost consideration is 
Entra ID P2 — available as a free 30-day trial which covers the 
full duration of this lab.

---

## Time to Build

| Phase | Estimated Time |
|-------|---------------|
| Lab setup (01) | 30–45 minutes |
| Entra ID config (02) | 45–60 minutes |
| Sentinel setup (03) | 45–60 minutes |
| **Total** | **~2.5–3 hours** |

---

## Related Files
- KQL detection queries: [`/kql`](../kql)
- SOC incident reports: [`/reports`](../reports)
- Screenshots evidence: [`/screenshots`](../screenshots)
- Main README: [`/README.md`](../README.md)
