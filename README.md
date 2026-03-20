# enterprise-soc-sentinel-lab
Hands-on SOC lab simulating enterprise threat detection and identity security using Microsoft Sentinel and Entra ID.

# Enterprise Security Monitoring & Identity Protection Lab
A hands-on cybersecurity project simulating a real-world SOC environment using
Microsoft Sentinel (SIEM) and Microsoft Entra ID (IAM).
## What This Project Covers
- Identity management with RBAC, MFA, and Conditional Access
- Log ingestion from Entra ID into Microsoft Sentinel
- Detection of 5 real-world attack patterns using KQL
- Full SOC investigation and incident reporting workflow
## Attack Scenarios Simulated
| # | Scenario | Severity | Detection Method |
|---|---|---|---|
| 1 | Brute Force Attack | High | KQL — failed login threshold |
| 2 | Impossible Travel | High | KQL — geographic analysis |
| 3 | Suspicious Login Behaviour | Medium | KQL — IP + hour anomaly |
| 4 | Privilege Escalation | Critical | KQL — AuditLogs role assignment |
| 5 | MFA Bypass Attempt | Medium–High | KQL — legacy auth + MFA fatigue |
## Technologies Used
- Microsoft Azure (Free Tier)
- Microsoft Sentinel (SIEM/SOAR)
- Microsoft Entra ID (Identity Provider)
- KQL (Kusto Query Language)
- Microsoft Authenticator (MFA)
## Key Skills Demonstrated
- Cloud identity and access management (IAM)
- SIEM configuration and log ingestion
- Threat detection with custom KQL analytics rules
- SOC incident investigation and reporting
- Security control implementation (MFA, Conditional Access, RBAC)
## Screenshots
[Add screenshots of your Sentinel incidents, KQL results, and Entra ID config]
## Project Status
Completed — all 5 scenarios simulated, detected, investigated, and documented
Daniel Owoeye-wise
