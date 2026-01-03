# Cloud Security Engineering Lab: Azure Honeypot, Centralized Logging, and Threat Detection

## Objective
This project demonstrates the design and implementation of a cloud-native security telemetry and detection pipeline in Microsoft Azure. The environment simulates real-world attack activity by deploying an intentionally exposed Windows virtual machine (honeypot) and routing its security events through a centralized logging and SIEM architecture.

### Skills Learned

- Deploying and instrumenting cloud infrastructure for security monitoring.
- Centralizing and analyzing Windows security events using Log Analytics.
- Writing KQL queries to detect authentication failures and suspicious activity.
- Building security visualizations to support threat hunting and situational awareness.
- Understanding SIEM architecture and data flow in a production-like cloud environment.

### Tools Used

- Microsoft Azure – Cloud platform used to provision and manage infrastructure resources
- Azure Virtual Machines (Windows 10) – Honeypot host generating real-world authentication telemetry
- Azure Network Security Groups (NSG) – Network-level exposure configuration for attack simulation
- Azure Monitor Agent (AMA) – Secure log collection and forwarding from the VM to Log Analytics
- Azure Log Analytics Workspace (LAW) – Centralized repository for security telemetry and analytics
- Microsoft Sentinel – Cloud-native SIEM used for log ingestion, detection, enrichment, and visualization
- Kusto Query Language (KQL) – Query language used to analyze Windows Security Events and build detections
- Sentinel Watchlists – GeoIP enrichment source used to add geographic context to attacker IP addresses
- Sentinel Workbooks – Custom dashboards used to visualize attack patterns and global threat activity
- Windows Event Viewer – Local log inspection and validation of security events (Event ID 4625)

## Steps
Part 1. Setup Azure Subscription
- Create Free Azure Subscription: https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account
- After your subscription is created, you can login at:
https://portal.azure.com

Part 2. Create your Resource group and core resources
In Azure Portal, go to Resource groups → open your lab RG (ex: RG-SOC-LAB).
Confirm you have these resources:
- Windows VM
- Public IP
- NSG
- NIC
- Disk
- VNet
<img width="1815" height="857" alt="soc1" src="https://github.com/user-attachments/assets/e83c6e99-2279-494e-bb2c-9824c49b7482" />


Part 3. Open inbound traffic on the NSG

In the RG, click your Network Security Group (NSG).
Go to Inbound security rules → Add.
Create a rule that allows inbound traffic broadly (for the lab):
- Source: Any
- Source port ranges: 
- Destination: Any
- Destination port ranges: 
- Protocol: Any
- Action: Allow
- Priority: something like 100 (must be higher priority than deny rules)

Part 4. Disable Windows Firewall on the VM (LAB ONLY)
- RDP into the VM.
- Open Windows Defender Firewall with Advanced Security (wf.msc).
- Click Windows Defender Firewall Properties.
- Set Domain / Private / Public to Off.
<img width="2522" height="1372" alt="soc2" src="https://github.com/user-attachments/assets/8a10b71d-8ab0-4e6f-9b0b-be9947a2d3d3" />

Part 5. Create Log Analytics Workspace (LAW)

In Azure Portal, search Log Analytics workspaces → Create.
Select:
- Subscription
- Your lab Resource Group
- Region (same as VM if possible)
- Create it.
<img width="2493" height="1138" alt="soc3" src="https://github.com/user-attachments/assets/38111980-2b4e-45fd-af1c-0d314cde366a" />

Part 6. Connect logs to LAW / Sentinel (Windows Security Events via AMA)
- In Azure Portal, search Microsoft Sentinel → Create.
- Select your Log Analytics Workspace (LAW) and enable Sentinel on it.
- In Sentinel:
- Go to Content management / Data connectors
- Find Windows Security Events via AMA
- Open connector page → create/select a Data Collection Rule (DCR)
- Scope it to your VM (or the resource group)
- Wait a few minutes for ingestion.

Part 7. Query failed logons (Event ID 4625)
- Filter all failed logons (4625)
<img width="1772" height="1000" alt="soc5" src="https://github.com/user-attachments/assets/f20f587e-a90f-4057-b634-cf8149b482e7" />

Part 8. Add GeoIP enrichment (Sentinel Watchlist)

- In Microsoft Sentinel → go to Watchlists → Create new.
- Set:
- Name/Alias: geoip
- Source type: Local file
- Upload: geoip-summarized.csv
- Number of lines before row: 0
- Search Key: network
- Create and wait for import completion.

Part 9. Enrich logs using ipv4_lookup

<img width="1732" height="927" alt="soc6" src="https://github.com/user-attachments/assets/db3eb287-2b05-4c35-bc88-d4be9a6597c4" />


Part 10. Build the Attack Map Workbook in Sentinel
- In Microsoft Sentinel → Workbooks → New.
- Remove default widgets.
- Add a Query widget.
- Use the workbook JSON (your map.json) via the Advanced editor tab .
- Run/Save the workbook and confirm pins on the world map.
<img width="1322" height="888" alt="soc8" src="https://github.com/user-attachments/assets/6c7e6fd4-ef2b-413c-a53c-2043145b9100" />



