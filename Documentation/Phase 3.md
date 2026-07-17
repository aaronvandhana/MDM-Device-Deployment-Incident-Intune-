# Phase 3 — Compliance Policy, Engineered Failure, and Incident Ticket
## Objective
Create a device compliance policy in Microsoft Intune, engineer a 
real compliance failure on the enrolled test device, configure 
Conditional Access in Entra ID to enforce the policy, verify the 
block, and create the resulting incident ticket in osTicket.
---
## Step 1: Create Compliance Policy in Intune
Created a new Windows compliance policy in the Intune admin center 
to define the security baseline a device must meet to access 
company resources.
- **Policy name:** AaronLab Windows Compliance Policy
- **Platform:** Windows 10 and later
- **Compliance settings configured:**
  - Device Health — BitLocker: Require
  - System Security — Firewall: Require
  - System Security — Microsoft Defender Antimalware: Require
- **Action for noncompliance:** Mark device noncompliant — Immediately
- **Assigned to:** All users

Screenshot: P3_01_Compliance_Policy.png
---
## Step 2: Confirm Policy Deployment and Device Compliance Status
Confirmed the AaronLab Windows Compliance Policy was created and 
active under Devices > Compliance > Policies, assigned by default 
scope tag with a last modified timestamp matching creation.

After the policy synced, DESKTOP-1QBQANA (enrolled under 
alayus@AaronLabIT.onmicrosoft.com) changed status from Compliant 
to Not Compliant.

### Investigation
Checked the per-setting compliance breakdown rather than relying 
on the overall status:
- Firewall: Compliant
- Microsoft Defender Antimalware: Compliant
- **BitLocker: Not compliant**

### Issue Encountered
A Norton 360 forced resubscription and reinstall triggered a 
restart on the host machine during testing. This was initially 
assumed to be the cause of a firewall related failure, since 
Windows had handed firewall management off to Norton and the 
native Windows Defender Firewall toggle was greyed out.

### Resolution
The per setting detail view showed firewall and Defender were 
both still compliant; the actual failure was BitLocker, which 
was not enabled on the host machine. This is a realistic 
compliance gap for a personally-owned device enrolled as BYOD, 
and was used as the root cause going forward instead of the 
originally assumed firewall issue.

Screenshot: P3_02_Compliant_Devices.png
---
## Step 3: Configure Conditional Access and Verify the Block
Created a Conditional Access policy to enforce the compliance 
requirement at sign-in.
- **Policy name:** AaronLab-Block Non-Compliant Devices
- **Users:** alayus (test user)
- **Target resources:** All resources (All cloud apps)
- **Grant control:** Require device to be marked as compliant

### Issue Encountered
Policy creation was initially blocked by a warning that security 
defaults must be disabled before a Conditional Access policy can 
be enabled.

### Resolution
Disabled security defaults under Entra ID > Overview > Properties, 
since this is a single-tenant lab with no other dependent policies. 
Noted for documentation that this tradeoff would need more 
consideration in a production tenant.

Tested the policy in Report-only mode first. Alayus signed in and 
the sign-in log's Report-only tab showed a result of Report-only: 
Failure, confirming the policy correctly identified the 
non-compliant device and would have blocked access. Switched the 
policy from Report-only to On.

With the policy enforced, alayus attempted to sign in again from 
DESKTOP-1QBQANA. The sign-in was blocked, returning "You can't get 
there from here," stating the device did not meet AaronLab IT's 
management compliance policy. This confirmed the Conditional 
Access policy was enforcing correctly end-to-end, from compliance 
evaluation in Intune through to access enforcement in Entra ID.

Screenshot: P3_03_alayus_cannot_access_login.png
---
## Step 4: Create Incident Ticket in osTicket
Submitted the resulting incident as a ticket in osTicket to begin 
the formal support workflow.
- **Ticket:** #617768
- **Title:** Can't log into anything on my new laptop
- **Submitted by:** Alie Layus (alayus@aaronlab.local)
- **Source:** Web (end user portal, http://localhost:8080/osticket/)

The ticket description reflects what a real end user would 
report; the symptom only, with no technical detail about 
compliance policies, plus one detail she noticed on her own 
(a recent Norton antivirus reinstall and restart).

Screenshot: P3_04_Ticket617768.png
---
## Outcome
By the end of Phase 3, the environment has a working, tested 
enforcement chain: compliance policy (Intune) > non-compliant 
device > Conditional Access (Entra ID) > enforced block > real 
user impact > incident ticket opened. Phase 4 will work this 
incident inside osTicket using the CompTIA 6-step troubleshooting 
methodology.
