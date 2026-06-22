# 04 - Group Policy

Group Policy allows administrators to enforce security settings, deploy software, and configure user environments across the entire domain from one central location. Instead of configuring each machine individually, Group Policy Objects (GPOs) push settings automatically to all users and computers in the domain. In this section, several GPOs commonly used in enterprise environments were created and tested.

---

## Step 1: Configure a Domain-Wide Password Policy

Configured a password policy through the **Default Domain Policy** to enforce security standards across all user accounts in the domain. This ensures every user on the network must follow the same password requirements without any individual configuration needed on each machine.

Password policy settings configured:

- **Minimum password length:** 8 characters
- **Password complexity:** Enabled (must include uppercase, lowercase, numbers, or symbols)
- **Maximum password age:** 90 days (users must change their password every 90 days)

<img width="782" height="568" alt="password policy settings" src="https://github.com/user-attachments/assets/3273326a-28b1-49b8-b294-701540b611c3" />

---

## Step 2: Configure an Account Lockout Policy

Configured an account lockout policy to automatically lock user accounts after a set number of failed login attempts. This protects against brute force attacks and unauthorized access to user accounts.

Account lockout settings configured:

- **Account lockout threshold:** 3 invalid login attempts
- After 3 failed attempts the account is automatically locked and requires an administrator to unlock it

<img width="848" height="582" alt="account lockout policy" src="https://github.com/user-attachments/assets/1a1aba91-5448-4b99-bc70-c8e436fe4cf3" />


---

## Step 3: Create a Shared Folder on DC01

Created a shared folder called **SharedFiles** on DC01 and enabled network sharing. This folder will be automatically mapped to client workstations through Group Policy, giving all domain users access to shared company files without any manual setup on each machine.

Setting up the shared folder first is required before the drive mapping GPO can reference it.



<img width="447" height="473" alt="shared files properties" src="https://github.com/user-attachments/assets/9bdb8574-a00a-45fc-8889-76634b84c572" />


## Step 4: Create the Drive Mapping GPO

Created a new Group Policy Object called **Drive Mapping Policy** and linked it to the **mydomainlab.local** domain. Linking the GPO to the domain means it applies to all users and computers in the domain automatically.

<img width="291" height="367" alt="Drive mapping policy" src="https://github.com/user-attachments/assets/64e1c969-bb97-4520-9574-85b8598cf4f7" />


---

## Step 5: Configure the Drive Mapping

Inside the Drive Mapping Policy GPO, configured the drive mapping to automatically map a network drive for all domain users when they log in:

- **Drive Letter:** S:
- **Path:** `\\DC01\SharedFiles`
- **Action:** Create — maps the drive every time a user logs in

This gives all domain users immediate access to the shared company folder from File Explorer without any manual configuration on their end.

<img width="775" height="478" alt="shared mapping" src="https://github.com/user-attachments/assets/a6d6023e-078a-4d22-b7e4-897a9d540267" />


---

## Step 6: Apply Group Policy with gpupdate /force

Ran `gpupdate /force` on Client01 to immediately pull and apply the latest Group Policy changes from the domain controller instead of waiting for the default 90-minute automatic refresh cycle:

```
gpupdate /force
```

This is a standard command used in real environments any time a policy change needs to take effect immediately without waiting for the next scheduled refresh.

<img width="993" height="516" alt="gpupdate command" src="https://github.com/user-attachments/assets/d2a87852-38ef-4638-9793-59f544e4987d" />


---

## Step 7: Verify Policy Application with gpresult /r

Ran `gpresult /r` on Client01 to verify which Group Policy Objects were successfully applied to the computer and the logged-in user:

```
gpresult /r
```

Confirmed the **Drive Mapping Policy** appeared in the list of applied GPOs, verifying the policy successfully reached the client machine.

This is an important troubleshooting and verification command — it shows exactly which GPOs are applied and helps diagnose any policies that aren't reaching clients as expected.

<img width="987" height="658" alt="gpresult command" src="https://github.com/user-attachments/assets/366521e4-a8fe-4387-819f-c9cde492bf63" />


---

## Step 8: Confirm Drive Mapping in File Explorer

Opened **File Explorer** on Client01 and confirmed the **S: drive** was automatically mapped to `\\DC01\SharedFiles`. This proves the GPO is working correctly and all domain users now have access to the shared network folder on login.

<img width="268" height="523" alt="shared files on client" src="https://github.com/user-attachments/assets/ad922897-a4e2-4e01-8cc0-3c1f9bae985c" />


---

## Summary

The following Group Policy tasks were completed in this section:

| GPO Task | Purpose |
|---|---|
| Password Policy | Enforced minimum length, complexity, and 90-day expiration domain-wide |
| Account Lockout Policy | Locked accounts after 3 failed login attempts to prevent brute force |
| Shared Folder | Created SharedFiles on DC01 as the target for drive mapping |
| Drive Mapping GPO | Automatically mapped S: drive to \\DC01\SharedFiles for all domain users |
| gpupdate /force | Immediately applied policy changes without waiting for the refresh cycle |
| gpresult /r | Verified the Drive Mapping Policy was successfully applied to Client01 |
| File Explorer Confirmation | Confirmed S: drive appeared and was accessible on the client workstation |

Group Policy is one of the most powerful tools in a sysadmin's toolkit — these tasks reflect the configurations most commonly deployed and maintained in enterprise environments.
