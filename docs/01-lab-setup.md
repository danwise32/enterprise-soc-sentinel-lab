# Lab Setup — Microsoft Azure and Sentinel

## Environment

- Platform: Microsoft Azure (Pay-as-you-go)
- Tenant: Cyberbulwork.onmicrosoft.com
- Region: UK South
- SIEM: Microsoft Sentinel
- Log Analytics Workspace: SOCLabWorkspace
- Resource Group: SOC-Lab-RG

## Setup Steps

### Step 1 — Azure Tenant

Created Microsoft Azure account and tenant. Tenant domain is
Cyberbulwork.onmicrosoft.com. Global admin account:
DanielOwoeye-Wise@Cyberbulwork.onmicrosoft.com

### Step 2 — Log Analytics Workspace

Created SOCLabWorkspace in UK South region under resource
group SOC-Lab-RG. This is the central log store for all
Sentinel data.

### Step 3 — Microsoft Sentinel

Enabled Microsoft Sentinel on SOCLabWorkspace. All analytics
rules, incidents, and log queries run within this workspace.

### Step 4 — Entra ID P2 Licence

Activated Microsoft Entra ID P2 trial and assigned to all
lab users. P2 is required for Conditional Access policies
and Identity Protection features.

### Step 5 — Data Connector

Connected Microsoft Entra ID data connector in Sentinel
with the following log types enabled:
- Sign-In Logs
- Audit Logs
- Non-Interactive User Sign-In Log
- User Risk Events
- Risky Users
