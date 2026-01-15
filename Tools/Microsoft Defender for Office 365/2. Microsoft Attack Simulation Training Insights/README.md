# Microsoft Defender for Office 365 Attack Simulation Training Insights

## Overview

This Power BI template provides insights into your organization's Attack Simulation Training data from Microsoft Defender for Office 365. It helps tenant administrators identify security knowledge gaps and track user training progress.

- **What you get:** Visualizations for simulation coverage, training coverage, and repeat offender analysis.
- **Data source:** Microsoft Graph Security Reports API.
- **How it works:** Power BI calls the Graph Security API using Web.Contents with application permissions. The app secret is stored and retrieved from Azure Key Vault via a custom connector.

### What is Attack Simulation Training?

Attack simulation training is part of [Microsoft Defender for Office 365](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/defender-for-office-365) which sets up benign cyberattack simulations to train users in the tenant to increase their awareness and help identify vulnerable users.

For more information, see: [Reports API overview for attack simulation training](https://learn.microsoft.com/en-us/graph/api/resources/report-m365defender-reports-overview?view=graph-rest-1.0)

### Data Available

| Report Type | Description |
|-------------|-------------|
| **Repeat Offenders** | Users who have repeatedly fallen for simulated attacks |
| **Simulation User Coverage** | Simulation data and results for each user in the tenant |
| **Training User Coverage** | Training completion status for each user in the tenant |

### Quick Architecture Summary

**For manual Desktop refresh:** 📱 Power BI Desktop → 🔌 Custom Connector → 🔑 Azure Key Vault → 🔐 App Secret → 📊 Graph API → 📈 Attack Simulation Data

**For scheduled refresh:** ☁️ Power BI Service → 🌐 On-premises Gateway → (same flow)

## 🔒 Security

- **No Hardcoded Secrets**: Client Secrets stay in Key Vault.
- **Least Privilege**: The account used to configure/refresh the report only needs `Get` permission on the specific Key Vault Secret. The application only needs `AttackSimulation.Read.All` permission on the Graph API.

## 📁 Repository Contents

| File | Description |
|------|-------------|
| `Microsoft Defender for Office 365 Attack Simulation Training Insights.pbit` | Power BI template report |
| `README.md` | This file |

## 📦 Prerequisites & Setup

> **This template uses the same custom connector as the [Microsoft Defender for Office 365 Detections and Insights](<../1. Microsoft Defender for Office 365 Detections and Insights/README.md>) template.**
>
> Follow the complete setup guide in that folder for:
> - Custom connector installation (`KeyVaultConnector.mez`)
> - Azure Key Vault configuration
> - Power BI Desktop configuration

### ⚠️ Key Difference: Application Permission

When registering your Entra ID application, use the following permission instead of `ThreatHunting.Read.All`:

| Permission | Type | Description |
|------------|------|-------------|
| `AttackSimulation.Read.All` | Application | Read attack simulation data |

**Steps:**
1. In your app registration, go to **API Permissions** → **Add a permission** → **Microsoft Graph** → **Application permissions**
2. Search for and select `AttackSimulation.Read.All`
3. Click **Grant admin consent**

> **Note:** You can use the same app registration and Key Vault secret as the Detections and Insights template if you add both permissions (`ThreatHunting.Read.All` and `AttackSimulation.Read.All`) to the same application.

## 📊 Setup (Power BI Desktop)

1. Ensure you have completed the prerequisites from the [Detections and Insights README](<../1. Microsoft Defender for Office 365 Detections and Insights/README.md>).
2. Open the `Microsoft Defender for Office 365 Attack Simulation Training Insights.pbit` template.
3. Enter the required parameters:
   - **Directory (tenant) ID**
   - **Application (client) ID**
   - **Key Vault URL** (e.g., `https://myvault.vault.azure.net`)
   - **Secret name** (the Key Vault secret containing the app secret)
4. When prompted by the Azure Key Vault connector, sign in with a user that has access to the secret.
5. If prompted for `login.microsoftonline.com` or `graph.microsoft.com`, select **Anonymous** connection.

## 🌐 Publish and Scheduled Refresh

To enable scheduled refresh in Power BI Service, an **On-premises Data Gateway** is required because this template uses a custom connector.

📘 **See the complete gateway guide:** [Gateway Deployment Guide](<../1. Microsoft Defender for Office 365 Detections and Insights/GatewayDeployment.md>)

## 🔧 Troubleshooting

| Issue | Solution |
|-------|----------|
| **403/401 on refresh** | Confirm `AttackSimulation.Read.All` has admin consent and the app/secret are correct |
| **Key Vault access denied** | Verify firewall/network settings and that your user has `Key Vault Secrets User` role |
| **Connector not found** | Re-check custom connector setup in `Documents\Power BI Desktop\Custom Connectors` |
| **No data returned** | Ensure you have active attack simulations configured in your tenant |

## 📚 Additional Resources

- [Attack simulation training in Microsoft Defender for Office 365](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/attack-simulation-training-get-started)
- [Microsoft Graph Attack Simulation Reports API](https://learn.microsoft.com/en-us/graph/api/resources/report-m365defender-reports-overview?view=graph-rest-1.0)
- [Microsoft 365 Defender Portal](https://security.microsoft.com/)

---
**Last Updated:** January 2026  
**Tested with:** Power BI Desktop (January 2026), On-premises Data Gateway (Standard Mode)  
**Author:** [Iustin Irimia/Security CSA] [Daniel Mozes/CxE MDO PM]
