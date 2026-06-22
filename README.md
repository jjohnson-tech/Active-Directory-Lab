# Active-Directory-Lab
Hands-on Active Directory home lab covering domain controller setup, user administration, Group Policy, and PowerShell built to simulate a real enterprise environment.

## Overview
A fully functional Active Directory environment built in VMware to simulate a corporate network. The lab includes a domain controller running AD DS, DNS, DHCP, and NAT routing, along with a Windows 10 client workstation joined to the domain. This lab was built to develop hands-on skills in identity management, user administration, Group Policy, PowerShell automation, and real-world troubleshooting.

---

## Technologies Used
- Windows Server 2022
- Windows 10 Enterprise
- Active Directory Domain Services (AD DS)
- DNS & DHCP Server
- Group Policy Management (GPMC)
- PowerShell
- VMware Workstation
- Remote Access / NAT Routing

---

## Lab Environment

| Machine  | Role                      | IP Address                    |
|----------|---------------------------|-------------------------------|
| DC01     | Domain Controller         | 192.168.10.1 (static)         |
| Client01 | Windows 10 Workstation    | 192.168.10.100–200 (DHCP)     |

**Domain:** mydomainlab.local

---

## What I Built

### 1. Domain Controller Setup
Configured DC01 with two NICs — NAT for internet access and Host-Only for the internal network. Installed Windows Server 2022, promoted DC01 to a domain controller, and created a new forest under mydomainlab.local. Installed and configured AD DS, DNS, DHCP, and NAT routing. Created a DHCP scope ranging from 192.168.10.100 to 192.168.10.200 for internal clients. Used a PowerShell script to bulk-create over 1,000 user accounts to simulate a real enterprise environment.

### 2. Client Workstation Setup
Configured Client01 with a Host-Only adapter, pointed DNS to DC01, and joined the machine to the domain. Verified DHCP assignment, domain authentication, and internet access through DC01's NAT routing.

### 3. Active Directory Administration
Created Organizational Units for IT, HR, and Sales departments. Performed common helpdesk tasks including:
- Creating user accounts
- Resetting passwords
- Disabling accounts
- Managing security group memberships
- Unlocking locked accounts

### 4. Group Policy
Configured and tested GPOs for:
- **Password Policy** — minimum 8 characters, 90-day expiration
- **Account Lockout** — locks after 3 failed login attempts
- **Drive Mapping** — automatically maps network drive for all domain users on login

Verified policy application using `gpupdate /force` and `gpresult /r` on the client.

### 5. PowerShell Administration
Managed Active Directory entirely from the command line using:

| Command | Purpose |
|---|---|
| `Get-ADUser` | Retrieve and filter user accounts |
| `New-ADUser` | Create new user accounts |
| `Set-ADAccountPassword` | Reset passwords without knowing the old one |
| `Unlock-ADAccount` | Unlock locked accounts |
| `Get-ADGroupMember` | Audit group membership |

### 6. Troubleshooting
Resolved five real-world issues during the build:

1. **Subnet Mismatch** — VMware Host-Only network was on a different subnet than DC01, preventing communication between the two machines
2. **Incorrect DHCP Gateway** — network address used instead of a host IP, blocking internet access on the client
3. **DNS Forwarder Failure** — stale VMware default forwarder preventing name resolution for external domains
4. **Admin Account Lockout** — accidentally locked the domain Administrator account during lockout policy testing; resolved using DSRM (Directory Services Repair Mode)
5. **GPO Drive Mapping Failure** — wrong computer name in the UNC path caused the mapped drive to fail on the client

---

## Key Takeaways
- Built and managed a fully functional AD domain from scratch
- Practiced the most common helpdesk AD tasks in a realistic environment
- Learned how Group Policy pushes settings across a domain without touching individual machines
- Developed real troubleshooting instincts by dia
