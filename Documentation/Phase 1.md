# Phase 1 — Environment Setup

## Objective
Create an Azure account, activate a Microsoft 365 Business Premium 
trial, and confirm access to the Microsoft Intune admin center to 
establish the cloud environment for this project.

---

## Step 1: Create Microsoft Azure Account

Created a new Microsoft Azure free account at portal.azure.com 
using a dedicated Microsoft account (aaronvandhana@outlook.com) 
created specifically for this project.

Azure free account includes:
- $200 in credits for 30 days
- Access to Microsoft Entra ID (visible in left sidebar)
- Foundation for Microsoft 365 tenant creation

Screenshot: P1_01_MicrosoftAzure_Dashboard.png

---

## Step 2: Activate Microsoft 365 Business Premium Trial

### Challenges Encountered

Initial attempts to activate the trial encountered several issues:

**Issue 1 — Personal email not accepted:**
The Microsoft 365 Admin Center requires a work or school account 
to sign in. A personal Gmail or Outlook account cannot be used 
directly to access admin.microsoft.com.

**Issue 2 — Outdated trial links:**
Several trial URLs returned errors stating the subscription was 
no longer available or had already been used.

**Issue 3 — Wrong plan selected:**
Microsoft 365 Business Standard was initially presented instead 
of Business Premium. Standard does not include Microsoft Intune, 
making it unsuitable for this project.

### Resolution

Navigated directly to the Microsoft 365 Business Premium product 
page and selected the free trial option. Created a new tenant 
during the signup flow with the following details:

- **Tenant name:** AaronLab IT
- **Admin account:** AaronVandhana@AaronLabIT.onmicrosoft.com
- **Domain:** AaronLabIT.onmicrosoft.com
- **License:** Microsoft 365 Business Premium Trial (25 seats)
- **Trial end date:** August 12, 2026 — cancel before this date 
  to avoid charges

Microsoft 365 Business Premium includes:
- Microsoft Intune (manage endpoints)
- Azure IP Information Protection
- Exchange, Teams, SharePoint, OneDrive
- Microsoft Defender for Business

Screenshot: P1_03_M365_Admin_Dashboard.png

---

## Step 3: Confirm Intune Admin Center Access

Navigated to intune.microsoft.com and signed in with the admin 
account. Confirmed the following:

- **Tenant:** AaronLab IT
- **Account status:** Active
- **Service health:** Healthy
- **Devices not in compliance:** 0 (baseline — no devices enrolled yet)

Intune admin center is fully accessible and ready for device 
enrollment and policy configuration in Phase 2.

Screenshot: P1_04_M365_Intune_Admin_Dashboard.png
