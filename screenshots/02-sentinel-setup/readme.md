# 📸 Screenshots — 02: Microsoft Sentinel Setup

These screenshots document the configuration of Microsoft Sentinel as the 
SIEM for this SOC lab, including data connector setup and log ingestion 
from Microsoft Entra ID.

## Screenshots in This Folder

| File | Description |
|------|-------------|
| `entra-id-connector-config.png` | Entra ID data connector configured in Microsoft Sentinel, enabling sign-in and audit log ingestion |

## What This Shows

- Microsoft Sentinel workspace connected to Microsoft Entra ID via the data connector
- Sign-in logs and audit logs flowing into the Log Analytics Workspace
- Foundation layer that enables all KQL detection queries and incident alerts

## Why It Matters

Without this connector, no identity logs reach Sentinel — meaning no detections 
fire. This step is the bridge between Entra ID activity and SOC visibility.

## Related Files
- KQL queries: [`/kql`](../../kql)
- Lab setup guide: [`/docs`](../../docs)
