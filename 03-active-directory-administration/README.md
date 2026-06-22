# 03 - Active Directory Administration

This section covers the most common Active Directory administration tasks that helpdesk and sysadmin teams perform on a daily basis. Each task was performed on the mydomainlab.local domain to simulate real enterprise workflows.

---

## Step 1: Create Organizational Units (OUs)

Created Organizational Units to simulate departments within a company and keep user accounts organized under the correct department structure.

OUs were created for the following departments under the **mydomainlab.local** domain:

- **IT**
- **HR**
- **Sales**

Organizing users into OUs is essential in a real environment because it allows Group Policy to be applied at the department level and makes user management much more efficient at scale.

<img width="913" height="525" alt="created departments in dc" src="https://github.com/user-attachments/assets/3915ad83-624f-4207-9a70-979cd51d81a2" />


---

## Step 2: Create a New User Account

Manually created a new user account in Active Directory Users and Computers (ADUC) and placed it in the appropriate department OU.

When creating the account the following was configured:

- **First and Last Name**
- **Username (logon name)**
- **Temporary password** with "User must change password at next logon" enabled
- **Placed in the correct OU** based on department

This mirrors the standard new hire onboarding process where IT creates an account before the employee's first day.

<img width="1092" height="556" alt="creating new user in dc" src="https://github.com/user-attachments/assets/2e1f21c7-7c4f-4ff9-b9d6-fe8e5fb7ced4" />


---

## Step 3: Create a Security Group and Add a User

Created a **Security Group** to manage permissions by department and added a user to it.

Security groups allow administrators to assign permissions to a group rather than individual users — meaning when someone joins or leaves a team, you just update their group membership instead of manually adjusting permissions on every resource.

Steps taken:
1. Created a new security group called **IT Support** under the IT OU
2. Opened the user account and added them to the IT Support group through the **Member Of** tab

<img width="593" height="531" alt="added new user to group" src="https://github.com/user-attachments/assets/51f8a8f3-1fa6-4981-b83b-e4c35549d182" />

---

## Step 4: Reset a User Password

Reset a user's password — one of the most frequent helpdesk tasks in any organization.

Steps taken:
1. Located the user in ADUC
2. Right-clicked the account → **Reset Password**
3. Entered a temporary password
4. Checked **"User must change password at next logon"** to force the user to create their own password on first login

This workflow is used any time a user forgets their password, is locked out, or a new temporary password is needed.

<img width="1048" height="560" alt="setting password for new user" src="https://github.com/user-attachments/assets/e12a8438-8941-43b8-b791-69845dc2990e" />

---

## Step 5: Disable a User Account

Disabled a user account to simulate an employee leaving the company or going on extended leave.

Steps taken:
1. Located the user in ADUC
2. Right-clicked the account → **Disable Account**

The account icon displays a **down arrow** in ADUC indicating the account is disabled. The user will be immediately unable to log in while their account and data remain intact — a best practice before eventual deletion.

Disabling rather than immediately deleting accounts is standard practice because it preserves access to the user's files and email during any transition period.

<img width="677" height="277" alt="disabled user account" src="https://github.com/user-attachments/assets/f58819da-5ae5-4574-8d71-0ff15f4c5d0a" />


---

## Step 6: Unlock a Locked Account

Configured an **account lockout policy** through Group Policy to lock user accounts after **3 failed login attempts**. Then intentionally triggered a lockout on Client01 by entering the wrong password three times to simulate a real lockout scenario.

After the account was locked, unlocked it through ADUC:

1. Located the locked user account in ADUC
2. Double-clicked to open the account properties
3. Clicked the **Account** tab
4. Checked the **"Unlock account"** checkbox
5. Clicked **Apply** and **OK**

Account lockouts are one of the most common helpdesk tickets in any organization. Being able to quickly identify and unlock accounts is an essential skill.

<img width="917" height="536" alt="account locked" src="https://github.com/user-attachments/assets/46ad6c72-3e88-4213-ab88-b9fa0c2a2b1b" />


<img width="497" height="517" alt="unlock account page shows locked" src="https://github.com/user-attachments/assets/9074025e-fc24-44a0-b242-0133aad9ee79" />


---

## Summary

The following Active Directory administration tasks were completed in this section:

| Task | Description |
|---|---|
| Create OUs | Organized users into IT, HR, and Sales department containers |
| Create User Account | Created a new user and placed them in the correct OU |
| Create Security Group | Created IT Support group and added a user as a member |
| Reset Password | Reset a user password with forced change at next logon |
| Disable Account | Disabled a user account to simulate an offboarding scenario |
| Unlock Account | Unlocked a locked account after triggering the lockout policy |

These are the core day-to-day tasks performed by helpdesk and junior sysadmin teams in enterprise Active Directory environments.
