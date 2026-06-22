# 01 - Domain Controller Setup

This section covers the step-by-step process of building and configuring DC01, the domain controller for this Active Directory home lab. DC01 serves as the backbone of the entire environment — handling authentication, DNS, DHCP, and NAT routing for all machines on the internal network.

---

## Step 1: Create the Virtual Machine

Created a new virtual machine in VMware for DC01 and configured it with the following specs:

- **RAM:** 4GB
- **Processors:** 2 cores
- **Network Adapter 1 (NIC 1):** NAT — provides internet access to the domain controller
- **Network Adapter 2 (NIC 2):** Host-Only — connects DC01 to the internal private network

Having two NICs allows DC01 to act as a gateway between the internal lab network and the internet, which is essential for NAT routing later.

<img width="1897" height="848" alt="DC01 VM_Setup" src="https://github.com/user-attachments/assets/b02026ae-fad2-4cc5-a10f-19e002e60448" />


---

## Step 2: Install Windows Server 2022

Installed **Windows Server 2022 Standard Evaluation with Desktop Experience** on DC01. The Desktop Experience option includes the full GUI, making it easier to configure roles and features through Server Manager.

<img width="1401" height="847" alt="Windows Server Desktop" src="https://github.com/user-attachments/assets/4c2e29dd-d1c4-446b-814b-f4eb0afc5906" />


---

## Step 3: Install VMware Tools and Configure Static IP

After logging into the Administrator account, installed **VMware Tools** to improve VM performance and enable features like clipboard sharing and display scaling.

Next, configured a **static IP address** on NIC 2 (the Host-Only adapter) so that client machines on the internal network always know where to find the domain controller:

- **IP Address:** 192.168.10.1
- **Subnet Mask:** 255.255.255.0
- **DNS:** 127.0.0.1 (points to itself since DC01 will host DNS)

Also renamed NIC 2 to "Host-Only" to easily distinguish it from the NAT adapter.

A static IP is critical for the domain controller — if the IP changed, client machines would lose the ability to find Active Directory and DNS.

<img width="1260" height="601" alt="Static IP Configuration" src="https://github.com/user-attachments/assets/86f6cc57-f7cd-47b4-9c20-763f8928c7ed" />


---

## Step 4: Install the Active Directory Domain Services Role

Opened **Server Manager** and added the **Active Directory Domain Services (AD DS)** role. AD DS is the core Windows Server role that enables the machine to function as a domain controller and manage users, computers, and policies across the network.

<img width="1425" height="612" alt="Server Dashboard with AD" src="https://github.com/user-attachments/assets/78c18065-f140-4e9e-9fc6-28b83eb4373a" />


---

## Step 5: Promote DC01 to Domain Controller

After installing the AD DS role, used the post-installation wizard in Server Manager to **promote DC01 to a domain controller**. During promotion:

- Selected **"Add a new forest"** since this is a brand new domain
- Set the domain name to **mydomainlab.local**
- Set the Directory Services Restore Mode (DSRM) password

After the promotion completed, DC01 rebooted automatically. The login screen confirmed the domain was live by showing **MYDOMAINLAB\Administrator** as the login account.

<img width="783" height="566" alt="domain name" src="https://github.com/user-attachments/assets/e6155897-5623-44a2-9084-7a674c66a93c" />


<img width="1407" height="722" alt="Mydomain " src="https://github.com/user-attachments/assets/5aeab703-ec04-41cd-9afb-784727bfe098" />


---

## Step 6: Bulk Create Users with PowerShell

First manually created a user account through Active Directory Users and Computers (ADUC) to understand the process and verify everything was working correctly.

Then used a **PowerShell script** to automatically generate over **1,000 user accounts** to simulate a real enterprise environment with a large user base. The script pulled names from a list and created accounts with standardized usernames and a default password, placing them into the appropriate Organizational Units.

Bulk user creation through PowerShell is a common real-world task for sysadmins onboarding large numbers of employees or setting up a new domain.

<img width="842" height="542" alt="users" src="https://github.com/user-attachments/assets/04366aa9-1d7c-4127-9539-5dc1a075f57b" />


---

## Step 7: Configure NAT Routing (Remote Access)

Installed the **Remote Access** role and configured **NAT (Network Address Translation)** routing on DC01. This allows client machines on the internal Host-Only network to access the internet through DC01, even though they have no direct internet connection of their own.

NAT works by translating the private internal IP addresses of client machines into DC01's public-facing NAT IP before sending traffic out to the internet — and then routing the responses back to the correct internal machine.

<img width="582" height="371" alt="ras" src="https://github.com/user-attachments/assets/7ba6f6a1-6fab-43c6-a6f8-8927bb887aa4" />


---

## Step 8: Install and Configure DHCP

Installed the **DHCP Server** role and created a scope to automatically assign IP addresses to client machines joining the internal network:

- **Scope Range:** 192.168.10.100 – 192.168.10.200
- **Default Gateway:** 192.168.10.1 (DC01)
- **DNS Server:** 192.168.10.1 (DC01)

DHCP eliminates the need to manually assign IP addresses to every machine. When a client joins the network, it automatically receives an IP, gateway, and DNS server from DC01, allowing it to communicate on the network and find Active Directory right away.


<img width="607" height="406" alt="address range" src="https://github.com/user-attachments/assets/69615963-2fdd-4e2d-94b0-7a885562a53e" />


---

## Summary

By the end of this section, DC01 was fully configured as the domain controller for mydomainlab.local with the following services running:

| Service | Purpose |
|---|---|
| Active Directory Domain Services | User and computer authentication across the domain |
| DNS | Name resolution for the domain and external internet |
| DHCP | Automatic IP assignment for internal client machines |
| NAT Routing | Internet access for internal network clients through DC01 |

DC01 is now ready to support client workstations joining the domain, which is covered in the next section.
