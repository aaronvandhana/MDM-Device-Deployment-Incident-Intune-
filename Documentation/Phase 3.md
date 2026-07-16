# Phase 3 — Compliance Policy Creation, Engineered Failure, and Incident Ticket

## Objective

Create a device compliance policy in Microsoft Intune, engineer a real compliance failure on the enrolled test device, configure Conditional Access in Entra ID to enforce that policy, verify the block actually works, and open the resulting incident ticket in osTicket.

---

## Step 1 — Created Compliance Policy in Intune

Built a new compliance policy in the Intune admin center to define the security baseline a device must meet before it's allowed to access company resources.

- **Policy name:** AaronLab Windows Compliance Policy
- **Platform:** Windows 10 and later
- **Compliance settings configured:**
  - **Device Health → BitLocker:** Require
  - **System Security → Firewall:** Require
  - **System Security → Microsoft Defender Antimalware:** Require
- **Action for noncompliance:** Mark device noncompliant — Immediately
- **Assigned to:** All users

*(Screenshot: Intune compliance policy settings summary)*

---

## Step 2 — Device Registered as Non-Compliant

After the policy synced, **DESKTOP-1QBQANA** (enrolled under alayus@AaronLabIT.onmicrosoft.com) flipped from **Compliant** to **Not Compliant** in the Intune device list.

Drilling into the per-setting compliance detail confirmed:

| Setting | State |
|---|---|
| Firewall | ✅ Compliant |
| Microsoft Defender Antimalware | ✅ Compliant |
| BitLocker | ❌ Not compliant |

The device failed specifically on the **BitLocker** requirement — disk encryption was not enabled on the host machine.

*(Screenshot: Device compliance detail — DESKTOP-1QBQANA)*

---

## Step 3 — Configured Conditional Access in Entra ID

Created a Conditional Access policy in the Entra admin center to actually enforce the compliance requirement at sign-in, rather than just flagging the device inside Intune with no real-world consequence.

- **Policy name:** AaronLab-Block Non-Compliant Devices
- **Users:** alayus (test user)
- **Target resources:** All resources (All cloud apps)
- **Grant control:** Require device to be marked as compliant
- **Enable policy:** Started in **Report-only** mode to validate behavior safely before enforcing

Tested Report-only mode first by having alayus sign in and checking the **Report-only** tab of the sign-in log — result showed **Report-only: Failure**, confirming the policy correctly identified the non-compliant device and would have blocked access.

Once confirmed, switched the policy from **Report-only** to **On**.

*(Screenshot: Conditional Access policy configuration)*
*(Screenshot: Report-only sign-in log result)*

---

## Step 4 — Verified the Block

With the policy enforced, alayus attempted to sign in to a company resource again. This time the sign-in was actually blocked, returning a message stating the device did not meet the organization's security requirements and access was denied.

This confirmed the Conditional Access policy was working correctly end-to-end — from compliance evaluation in Intune through to enforcement in Entra ID.

*(Screenshot: Access blocked message shown to alayus)*

---

## Step 5 — Created Incident Ticket in osTicket

With the block confirmed and reproducible, submitted the resulting incident as a ticket in osTicket to begin the formal support workflow.

- **Ticket:** #617768
- **Title:** Can't log into anything on my new laptop
- **Submitted by:** Alie Layus (alayus@aaronlab.local)
- **Source:** Web (end user portal, http://localhost:8080/osticket/)

The ticket description mirrors what a real end user would report — no technical detail about compliance policies, just the symptom (blocked from company portal) and one potentially relevant detail she noticed on her own (a recent Norton antivirus reinstall and restart).

*(Screenshot: Ticket #617768 as submitted)*

---

## Issues Encountered and Resolutions

**Issue: Windows Defender Firewall toggle was greyed out on the test machine.**
Norton 360 for Gamers was installed as the active third-party firewall provider, so Windows had handed off firewall management to Norton and disabled its own native toggle. This meant the firewall compliance setting couldn't be manipulated directly through Windows Security.
*Resolution:* Rather than fighting Norton's management of the firewall, let the compliance failure emerge naturally through a different path — see below.

**Issue: A Norton 360 forced resubscription/reinstall and restart occurred mid-lab, and the device flipped to Not Compliant on its own — before firewall was ever touched.**
Initially assumed this meant the firewall was the failure point, since that was the setting being investigated at the time.
*Resolution:* Checked the per-setting compliance breakdown instead of relying on the overall Compliant/Not Compliant status. This revealed the actual failure was **BitLocker**, not firewall — firewall and Defender were both still reporting compliant. This turned out to be a more realistic and arguably more valuable incident scenario: a personally-owned device enrolled into the tenant without disk encryption enabled, which is a common real-world compliance gap for BYOD scenarios.

**Issue: Conditional Access policy creation was blocked by a security defaults warning.**
Entra ID's security defaults and Conditional Access policies can't be active at the same time — the portal wouldn't allow the new CA policy to be created until security defaults were disabled.
*Resolution:* Disabled security defaults under Entra ID → Overview → Properties, since this is a single-tenant lab environment with no other users or policies depending on that baseline protection. Documented for awareness that this tradeoff would need more consideration in a production tenant.

---

## Outcome

By the end of Phase 3, the lab environment had a working, tested compliance enforcement chain:

**Compliance policy (Intune) → non-compliant device → Conditional Access (Entra ID) → enforced block → real user impact → incident ticket opened.**

This sets up Phase 4, where the incident will be worked inside osTicket using the CompTIA 6-step troubleshooting methodology.
