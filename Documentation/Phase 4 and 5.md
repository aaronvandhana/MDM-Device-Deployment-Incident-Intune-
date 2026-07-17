# Phase 4 & 5 — Working the Incident in osTicket
## Objective
Work Ticket #617768 inside osTicket using the CompTIA 6-step 
troubleshooting methodology, documenting each step directly in 
the ticket thread through internal notes and replies to the end 
user, then close the ticket once resolved.
---
## Step 1: Identify the Problem
Claimed the ticket and reviewed Alie's report of being blocked 
from company resources on DESKTOP-1QBQANA after a successful 
login. Checked Intune and confirmed the device was showing Not 
Compliant, then drilled into the per-setting compliance detail 
under AaronLab Windows Compliance Policy:
- Firewall: Compliant
- Microsoft Defender Antimalware: Compliant
- BitLocker: Not compliant

Alie's mention of a recent Norton 360 reinstall/restart was 
initially considered as a possible cause, but the per-setting 
detail ruled out firewall involvement. Replied to Alie to 
acknowledge the issue and let her know a fix was in progress, 
without requiring her to bring the laptop in.

Screenshot: P4_01_Ticket617768_Step1.png
---
## Step 2: Establish a Theory of Probable Cause
Working theory: DESKTOP-1QBQANA is enrolled as a personal/BYOD 
device, and BitLocker was never enabled on it prior to 
enrollment, a common gap for personally-owned hardware that 
wasn't provisioned through a standard corporate imaging process. 
Ruled out the Norton reinstall as the root cause, since Firewall 
and Defender both still reported compliant. Confirmed the 
AaronLab-Block Non-Compliant Devices Conditional Access policy is 
assigned to Alie's account, which explains why she was fully 
blocked at sign-in rather than just flagged.

Theory: enabling BitLocker will bring the device back into 
compliance, and once Intune re-syncs, the Conditional Access 
block will lift automatically with no policy changes required.

## Step 3: Test the Theory
Re-verified the per-setting compliance breakdown to confirm 
BitLocker was the isolated failure point:
- Firewall: Compliant
- Microsoft Defender Antimalware: Compliant
- BitLocker: Not compliant

Also re-confirmed the Conditional Access policy was functioning 
correctly — tested in Report-only mode first (result: Report-only 
Failure), then switched to enforced (On), reproducing the same 
block Alie described. Theory confirmed on both fronts: BitLocker 
is the sole compliance failure, and Conditional Access is 
enforcing correctly. No misconfiguration found on the policy 
side.

## Step 4: Establish a Plan of Action and Implement the Solution
Since this was a genuine compliance gap rather than a policy 
misconfiguration, the plan was to bring the device into 
compliance rather than adjust the Conditional Access or 
compliance policy:
1. Confirm with Alie that BitLocker needs to be enabled.
2. Walk her through enabling BitLocker via Settings > Privacy & 
   Security > Device encryption (or Control Panel > BitLocker 
   Drive Encryption).
3. Advise her to keep the laptop plugged in and connected during 
   encryption.
4. Let her know Intune would need one sync cycle after encryption 
   completed before compliance status updated and the block 
   lifted.

Sent Alie step-by-step instructions to enable BitLocker.

Screenshot: P4_02_Ticket617768_Steps_2_3_4.png
---
## Step 5: Verify Full System Functionality
Alie confirmed BitLocker was enabled and access was restored 
after the device re-synced with Intune. Cross-checked on the 
admin side:
- Intune: DESKTOP-1QBQANA compliance status updated from Not 
  compliant to Compliant.
- Entra ID sign-in logs: Alie's most recent sign-in passed 
  Conditional Access evaluation successfully with no block 
  triggered.

Confirmed the fix resolved the issue without requiring any 
changes to the Conditional Access policy or compliance policy. 
System functioning as designed: compliant device grants access, 
noncompliant device blocks access. No side effects observed on 
other users or devices.

Screenshot: P4_03_Ticket617768_Step5.png
---
## Step 6: Document Findings, Actions, and Outcomes
Documented the full incident directly in the ticket:
- **Root cause:** Personally-owned device (DESKTOP-1QBQANA) 
  failed the AaronLab Windows Compliance Policy due to BitLocker 
  not being enabled, triggering the AaronLab-Block Non-Compliant 
  Devices Conditional Access policy.
- **Resolution:** User enabled BitLocker; no changes were made to 
  either policy, both of which were confirmed working as 
  designed throughout the incident.
- **Contributing factor:** A coincidental Norton 360 
  reinstall/restart initially suggested a firewall-related cause, 
  ruled out early by checking per-setting compliance detail.
- **Recommendation:** Verify BitLocker is enabled on 
  personally-owned/BYOD devices during onboarding, before 
  Conditional Access enforcement is applied, to avoid first-day 
  access disruptions for new hires.

Sent a final reply to Alie closing the loop, and closed the 
ticket with a status of Resolved.

Screenshot: P4_04_Ticket617768_Step6.png
---
## Outcome
Ticket #617768 was worked end-to-end using the full CompTIA 
6-step troubleshooting methodology, documented entirely within 
the ticket thread through internal notes and user-facing replies. 
The incident is resolved and closed. Phase 5 formally confirms 
and verifies this resolution outside the ticket for project 
documentation purposes.
