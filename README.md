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
![policy details](365%20Screenshots/04-ca-policy%20details.png)

## Up next
Intune device enrollment, and some documented troubleshooting scenarios.
