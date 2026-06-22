# 05 - PowerShell Administration

PowerShell allows administrators to perform Active Directory tasks through the command line instead of clicking through the GUI. This is faster for repetitive tasks and essential for automation. In this section, the most common helpdesk PowerShell commands for managing user accounts in Active Directory were practiced and documented.

---

## Step 1: Retrieve All User Accounts with Get-ADUser

Used `Get-ADUser` with the `-Filter *` wildcard to retrieve every user account in the domain. This returns all accounts without any filtering and gives a quick overview of everything in Active Directory.

```powershell
Get-ADUser -Filter *
```

The `-Filter *` parameter tells PowerShell to return all users. In a real environment this command is useful for auditing accounts, running reports, or piping the results into another command for further processing.

<img width="952" height="537" alt="List all users command" src="https://github.com/user-attachments/assets/e7de3bea-ceae-41ac-9832-1cf2bdcf24b5" />


---

## Step 2: Create a New User Account with New-ADUser

Created new user accounts using `New-ADUser`. The command specifies the display name, first and last name, login username, password, and which Organizational Unit to place the user in.

```powershell
New-ADUser `
  -Name "Jane Smith" `
  -GivenName "Jane" `
  -Surname "Smith" `
  -SamAccountName "jsmith" `
  -UserPrincipalName "jsmith@mydomainlab.local" `
  -Path "OU=HR,DC=mydomainlab,DC=local" `
  -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) `
  -Enabled $true
```

The `-Path` parameter uses Active Directory's **distinguished name format** to point to the exact OU where the account should be placed. The password must be converted to a secure string because PowerShell requires encrypted passwords for security — plain text passwords are never accepted.

<img width="1308" height="123" alt="powershell created users" src="https://github.com/user-attachments/assets/cddb93a6-4347-4973-80ce-ebb05b217c52" />


---

## Step 3: Reset a User Password with Set-ADAccountPassword

Used `Set-ADAccountPassword` to reset a user's password. The `-Reset` flag allows the password to be changed without knowing the old one — which is exactly how helpdesk handles password reset requests from users who are locked out or have forgotten their credentials.

```powershell
Set-ADAccountPassword -Identity "jsmith" `
  -Reset `
  -NewPassword (ConvertTo-SecureString "NewPassword123!" -AsPlainText -Force)
```

The new password is converted to a secure string before being applied. This is the PowerShell equivalent of right-clicking a user in ADUC and selecting **Reset Password**.

<img width="1410" height="530" alt="powershell reset password" src="https://github.com/user-attachments/assets/48875fcb-6bc4-4ba9-88a1-2794efee16ce" />


---

## Step 4: Unlock a Locked Account with Unlock-ADAccount

Used `Unlock-ADAccount` to unlock a user account that was locked out after too many failed login attempts.

```powershell
Unlock-ADAccount -Identity "jsmith"
```

This is the PowerShell equivalent of opening the user's properties in ADUC and checking the **Unlock account** checkbox on the Account tab. On helpdesk, account lockouts are one of the most frequent tickets — being able to resolve them quickly from the command line is a valuable skill.

<img width="1311" height="143" alt="powershell unlock account" src="https://github.com/user-attachments/assets/f018747b-8350-4e79-8eb4-e40d34df1148" />


---

## Step 5: Audit Group Membership with Get-ADGroupMember

Used `Get-ADGroupMember` to check which users belong to the **Domain Admins** group. This is useful for security auditing and verifying that only the right people have elevated access.

```powershell
Get-ADGroupMember -Identity "Domain Admins"
```

Regularly auditing privileged group membership is a security best practice in enterprise environments. If unauthorized accounts appear in Domain Admins or other sensitive groups, it can indicate a security incident or misconfiguration that needs to be addressed immediately.

<img width="1355" height="415" alt="powershell get group member" src="https://github.com/user-attachments/assets/d9391a3f-e7b2-4942-9522-bebf9f4da2f2" />


---

## Summary

The following PowerShell cmdlets were practiced and documented in this section:

| Cmdlet | Purpose |
|---|---|
| `Get-ADUser -Filter *` | Retrieve all user accounts in the domain |
| `New-ADUser` | Create a new user account and place it in the correct OU |
| `Set-ADAccountPassword` | Reset a user password without knowing the old one |
| `Unlock-ADAccount` | Unlock a user account locked out after failed login attempts |
| `Get-ADGroupMember` | Audit membership of a security or administrative group |

Managing Active Directory through PowerShell is faster than the GUI for repetitive tasks and essential for automation and scripting in enterprise environments. These five cmdlets cover the majority of what a helpdesk or junior sysadmin will use on a daily basis.
