# MDM Device Deployment Incident — Intune Compliance & Conditional Access
A new hire's device was enrolled in Intune but is being blocked from accessing company resources because it failed a compliance policy check — and nobody can figure out why

## Problem Statement

A new hire was issued a personally owned Windows 11 laptop and 
enrolled into the company's Microsoft Intune environment as part 
of onboarding. On first attempting to sign in to company 
resources, the user was unexpectedly blocked with a message 
stating their device did not meet the organization's security 
requirements. The user had not knowingly changed anything on 
their device and had no visibility into why access was being 
denied, creating a first day productivity blocker that needed to 
be triaged and resolved quickly.

The core challenge: authentication itself was succeeding, the 
user's credentials were correct, but access was still being 
denied downstream. This meant the root cause wasn't a login 
problem at all, but a device compliance and access control 
problem, requiring investigation across Intune, Entra ID 
Conditional Access, and the device itself.

## Solution Overview

Built a cloud-based device management environment using a 
Microsoft 365 Business Premium trial tenant, with Microsoft 
Intune managing device compliance and Microsoft Entra ID 
enforcing access control through Conditional Access. Created a 
Windows compliance policy requiring BitLocker, Firewall, and 
Microsoft Defender Antimalware, then enrolled a test user's 
device into the tenant.

Engineered a real compliance failure by allowing a personally 
owned device to enroll without disk encryption enabled, then 
built a Conditional Access policy requiring device compliance 
for access to any company resource. Verified the policy in 
Report-only mode before enforcing it live, confirming the device 
was correctly and fully blocked at sign-in.

Opened an incident ticket in osTicket and worked it end-to-end 
using the CompTIA A+ 6-step troubleshooting methodology, 
documenting every step as internal notes and user replies the 
way a real help desk technician would; including an initial 
false lead before the true root cause was confirmed.

Root cause identified: BitLocker was not enabled on the enrolled 
device, failing the compliance policy and triggering a 
Conditional Access block. Resolution: user enabled BitLocker, 
device re-synced as compliant, and access was automatically 
restored with no changes required to either policy.

## Video Walkthrough

Coming soon.

## Tools Used

* Microsoft Intune
* Microsoft Entra ID (Conditional Access)
* Microsoft 365 Business Premium (trial tenant)
* Windows 11
* VirtualBox
* osTicket (self-hosted via XAMPP)
* GitHub

## Project Timeline

* **Day 1:** Created Azure account and activated Microsoft 365 
  Business Premium trial. Confirmed access to the Intune admin 
  center and established the cloud environment.
* **Day 2:** Created new hire user (Alie Layus) in Entra ID, 
  assigned Microsoft 365 Business Premium license, enabled 
  Windows automatic enrollment in Intune, and enrolled a Windows 
  11 device.
* **Day 3:** Created a Windows compliance policy requiring 
  BitLocker, Firewall, and Microsoft Defender Antimalware. 
  Engineered a real compliance failure (BitLocker not enabled), 
  built and tested a Conditional Access policy blocking 
  non-compliant devices, and opened the resulting incident 
  ticket in osTicket.
* **Day 4:** Worked the full incident using the CompTIA A+ 
  6-step troubleshooting methodology, documenting each step 
  directly in the ticket. Resolved the issue by enabling 
  BitLocker, verified access was restored, and formally confirmed 
  and closed out the resolution.
* **Day 5:** GitHub documentation and README writeup. Video 
  walkthrough to follow.

## Key Accomplishments

* Built a working Intune compliance policy and Conditional Access 
  enforcement chain from scratch in a Microsoft 365 tenant
* Demonstrated the full lifecycle of a device compliance 
  incident, from policy creation through real user impact to 
  resolution
* Applied the CompTIA A+ 6-step troubleshooting methodology to a 
  real incident scenario, including an initial false lead that 
  was correctly ruled out through evidence rather than assumption
* Validated a Conditional Access policy safely using Report-only 
  mode before enforcing it live
* Demonstrated the ability to adapt an incident scenario in real 
  time when the lab environment (a personal host machine rather 
  than a disposable VM) didn't behave the way originally planned

## Engineering Notes

### Original Design Goal
Reproduce a company issued device failing compliance due to a 
missing security setting, to demonstrate how Intune and 
Conditional Access work together to protect company resources.

### Discovery
While investigating the compliance failure, a coincidental Norton 
360 forced reinstall and restart occurred on the test device, 
which initially suggested a firewall-related cause; Windows had 
handed firewall management off to Norton, greying out the native 
Windows Defender Firewall toggle entirely. Checking the 
compliance policy's per-setting detail (rather than relying on 
the overall Compliant/Not Compliant status) revealed that 
Firewall and Defender were both still compliant, and the actual 
failure was BitLocker. 

This was a more realistic and arguably more valuable incident 
than the one originally planned : a personally-owned, BYOD-style 
device enrolled without disk encryption enabled is a common 
real world compliance gap, distinct from a company issued laptop 
with a single misconfigured setting.

### Project Adjustment
Also encountered a platform level blocker when Entra ID refused 
to enable a Conditional Access policy while security defaults 
were active. Disabled security defaults for this single tenant 
lab environment and documented the tradeoff, noting that a 
production tenant would require more consideration before making 
that change.

## What I Learned

* How Intune compliance policies and Entra ID Conditional Access 
  work together to enforce security requirements at sign-in, 
  rather than just flagging a device with no real consequence
* The importance of checking per-setting compliance detail rather 
  than trusting an overall Compliant/Not Compliant status, since 
  the obvious cause isn't always the real one
* How to safely validate a Conditional Access policy using 
  Report-only mode before enforcing it live, to avoid locking out 
  real users by mistake
* Why personally owned/BYOD devices are a common source of 
  compliance gaps compared to company-provisioned hardware
* How to document a real incident in osTicket the way a working 
  help desk technician would, including a false lead that was 
  properly ruled out, not just the final correct answer
* That architectural quirks (a third-party security suite taking 
  over firewall management, a tenant-wide security default 
  blocking a needed policy) are normal parts of real IT work, and 
  knowing how to investigate and work around them is a core 
  professional skill
