# M365 / Entra ID Admin Lab

## Why I built this

I have Tier 1 help desk experience from Wells Fargo, along with several years supporting MSP clients on the sales and recruiting side, and I'm working toward a full-time IT Help Desk role with the long-term goal of moving into Systems Administration and Network Engineering. To build on that experience, I set up a real Microsoft 365 tenant and worked through the kind of admin tasks that come up daily on a help desk: creating users, managing groups, and setting up security and device management policies.

## What's in here

### 1. Creating users
Added a few test users through the M365 admin center — basically the "new hire needs an account" task you'd do constantly on a help desk.

![add users](365%20Screenshots/01-add-users.png)

### 2. Building a security group
Instead of managing each user one by one, I put them all into a Security group called `Test-Users`. That way if I need to apply a policy (like the MFA one below), I do it once at the group level and it applies to everyone in it. This is basically how access management works in any real company — you don't touch individual accounts for stuff like this, you manage the group.

![security group](365%20Screenshots/02-security-group.png)

### 3. Setting up a Conditional Access policy (require MFA)
This was the part I wanted to actually understand, not just click through. Conditional Access lets you say "before someone can sign in, require X" — in this case, requiring MFA for anyone in the Test-Users group, across all apps.

A few things I ran into along the way that I think are actually the more useful part of this whole lab:

- Conditional Access wouldn't even show up until I added the extra licensing it needs (Entra ID P2) and assigned it to myself. Licenses aren't automatic just because you add them to the tenant — you have to assign them to a user.
- Before I could turn the policy on, I had to disable Microsoft's built-in "Security Defaults" first, since you can't run Security Defaults and Conditional Access at the same time. I made sure to do that step AFTER my policy was ready, not before, so there wasn't a gap where nothing was requiring MFA.
- I left the policy in **Report-only** mode instead of flipping it live. Report-only logs what would've happened without actually enforcing it — basically a safe way to test a policy before it can lock anyone out. A bad Conditional Access policy can genuinely lock an entire company out of their own accounts if you're not careful, so this felt like the responsible way to do it even in a sandbox with nobody else on it but me.

![policy list](365%20Screenshots/03-ca-policy-list.png)
![policy details](365%20Screenshots/04-ca-policy-details.png)

### 4. Intune — compliance policy and configuration profile
Once I had Entra ID down, I wanted to get into Intune, since that's the other tool named directly in a lot of help desk postings (the Sagent one I was looking at specifically calls out Azure and Intune).

First thing I ran into: Intune only manages users who actually have a license that includes it. I'd added the EMS E5 trial earlier for Conditional Access, but my test users didn't have it yet, so I had to go back and assign it to each of them. Same lesson as before — a license sitting on the tenant doesn't do anything until it's assigned to a person.

**Compliance policy** — this is basically a checklist Intune uses to grade a device. I built one for Windows requiring BitLocker (disk encryption), a firewall, antivirus, and a minimum OS version. Assigned it to Test-Users, same group as everything else.

![compliance settings](365%20Screenshots/07-compliance-settings.png)
![compliance policy list](365%20Screenshots/09-compliance-policy-list.png)

**Configuration profile** — this is different from compliance. Compliance just checks whether a device meets the rules. A configuration profile actually pushes a setting onto the device. I built one that sets a 10-minute inactivity timeout before the screen locks.

![configuration profile settings](365%20Screenshots/10-config-profile-settings.png)
![configuration profile list](365%20Screenshots/11-config-profile-list.png)

### 5. Enrolling a real device (iPhone)
I wanted to actually enroll something instead of just building policies nobody's device would ever see. I used my personal iPhone for this instead of my laptop, since enrolling gives the tenant real management power over the device (including the ability to wipe it), and I didn't want that on the machine I actually work from. A phone is safer to test with since it's easy to remove afterward.

First attempt failed with an error called `AccountNotOnboarded`. Turns out Apple devices need something called an Apple MDM Push Certificate before Intune can manage them at all — it's a one-time setup where you generate a certificate through Apple's own site (using any Apple ID) and upload it into Intune. Without it, Intune has no way to actually talk to Apple's servers.

![push certificate created](365%20Screenshots/12-apns-certificate-created.png)
![push certificate uploaded](365%20Screenshots/13-apns-certificate-uploaded.png)

After that, enrollment went through. I went through Intune's Company Portal app, installed the management profile, and confirmed the phone showed up in the admin console as enrolled and managed.

![device retired](365%20Screenshots/15-device-retired.png)

Once I had what I needed, I retired the device from Intune (not wiped — Retire just removes management, it doesn't touch personal data) and double-checked the management profile was gone from the phone itself.

## Up next
Windows imaging/deployment basics, and some documented troubleshooting scenarios for common help desk tickets.
