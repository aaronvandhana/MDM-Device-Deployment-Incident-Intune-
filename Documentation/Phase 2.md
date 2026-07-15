# Phase 2 — Device Enrollment & Intune Configuration

## Objective
Create a test user in Entra ID, assign a Microsoft 365 Business 
Premium license, enable Windows enrollment in Intune, and enroll 
a Windows 11 device as the new hire's endpoint.

---

## Step 1: Create New Hire User in Entra ID

Created user Alie Layus in Microsoft Entra ID with the following 
attributes:

- **UPN:** alayus@AaronLabIT.onmicrosoft.com
- **Job Title:** Sales Representative
- **Department:** Sales
- **Company:** AaronLab
- **Account status:** Enabled

Screenshot: P2_01_Create_New_User_alayus.png

---

## Step 2: Assign Microsoft 365 Business Premium License

Assigned Microsoft 365 Business Premium license to Alie Layus 
via the M365 Admin Center. License assignment through Entra ID 
was unavailable — the portal redirected to M365 Admin Center for 
all licensing operations. Both Aaron Vandhana (admin) and Alie 
Layus (new hire) now show as licensed users with 2/25 seats 
assigned.

Screenshot: P2_02_M365_BusinessPremium_alayus.png

---

## Step 3: Enable Windows Automatic Enrollment in Intune

Configured automatic MDM enrollment in Intune:

- **MDM user scope:** All
- **WIP user scope:** None

This setting ensures any Windows device that connects a work 
account from the AaronLabIT.onmicrosoft.com tenant will 
automatically enroll into Intune MDM management.

Screenshot: P2_03_Enable_Windows_Enrollment_Intune.png

---

## Step 4: Device Enrollment — Issues and Adjustments

### Initial Attempt: Windows 11 Client VM (VirtualBox)

First attempted to enroll the existing Windows 11 client VM 
used in Project 2. Two issues were encountered:

**Issue 1 — Insufficient privileges:**
The VM is domain-joined to aaronlab.local. Standard domain 
users cannot add work accounts or modify device management 
settings on domain-joined machines without local admin rights. 
The local admin password for the VM was not documented and 
could not be recovered.

**Issue 2 — No internet access:**
The Windows 11 client VM is configured on an internal-only 
VirtualBox network to simulate an enterprise LAN. It has no 
internet connectivity, which is required for Intune enrollment 
since Intune is a cloud-based MDM platform.

**Discovery:** Enrolling a domain-joined VM into Intune without 
Hybrid Azure AD Join configuration (which requires Microsoft 
Entra Connect) is not straightforward in a single-server home 
lab. This is a real enterprise architecture consideration — 
in production, Hybrid Azure AD Join is used to bridge on-prem 
AD and cloud Intune management.

### Adjustment: Host Windows 11 Machine

Pivoted to enrolling the host Windows 11 physical machine 
instead. The host machine has:
- Full local admin rights
- Internet connectivity
- No domain join conflict

Connected Alie's work account

Screenshot: P2_05_MicrosoftIntune_Devices.png
