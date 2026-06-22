# 06 - Troubleshooting

During the build of this Active Directory home lab, several real-world networking and configuration issues came up that required diagnosing the root cause and applying a fix. These troubleshooting experiences mirror the types of problems encountered daily in helpdesk and systems administration roles.

---

## Issue 1: Subnet Mismatch Between VMware and Domain Controller

**Problem:**
Client01 could not communicate with DC01 at all. Running `ipconfig` on both machines revealed that VMware's Host-Only network was configured on the `192.168.109.0` subnet while DC01 was set up on `192.168.10.0`. The two machines were on completely different subnets and could not see each other.

**Fix:**
Changed the subnet in VMware's **Virtual Network Editor** to `192.168.10.0` to match DC01's network. Also disabled VMware's built-in DHCP service to prevent it from conflicting with the DHCP server configured on DC01.

**Commands used to diagnose:**
```
ipconfig
```

![VMware Virtual Network Editor with subnet corrected to 192.168.10.0 and built-in DHCP disabled](images/01-subnet-mismatch-fixed.png)

**What I learned:**
All devices that need to communicate must be on the same subnet. If a client can't reach a server, verifying both machines are on the same subnet is one of the first things to check. Running `ipconfig` on both ends and comparing the subnet masks quickly reveals this type of mismatch.

---

## Issue 2: Incorrect Default Gateway in DHCP Scope

**Problem:**
Client01 could reach DC01 on the internal network but had no internet access. Running `ipconfig` on Client01 showed the default gateway was set to `192.168.10.0` instead of `192.168.10.1`. The `.0` address is the network address and cannot be used as a gateway — it is not an actual host on the network.

**Fix:**
Corrected the router option in the DHCP scope on DC01 to `192.168.10.1` and ran `ipconfig /renew` on Client01 to pick up the updated DHCP settings.

**Commands used to diagnose and fix:**
```
ipconfig
ipconfig /renew
```

![DHCP scope options showing the corrected default gateway of 192.168.10.1](images/02-dhcp-gateway-fixed.png)

**What I learned:**
The default gateway must always be an actual host IP address — never the network address. When a client can reach local machines but not the internet, the default gateway is one of the first things to verify. A quick `ipconfig` reveals the gateway immediately.

---

## Issue 3: DNS Forwarder Not Working

**Problem:**
Client01 could ping external IP addresses like `8.8.8.8` but could not load websites or resolve domain names like `www.google.com`. The machine had internet connectivity but name resolution was broken.

**Fix:**
Checked the DNS forwarders on DC01 in DNS Manager and found the forwarder was set to `192.168.16.2` — an old VMware default address that was no longer responding. Changed the forwarder to `192.168.87.2` and name resolution worked immediately.

**Commands used to diagnose:**
```
ping 8.8.8.8
ping www.google.com
```

![DNS forwarders tab on DC01 showing the corrected forwarder address](images/03-dns-forwarder-fixed.png)

**What I learned:**
When a machine can reach IP addresses but not domain names, it is always a DNS problem. The quick diagnostic test is to ping an IP address first, then ping a domain name. If the IP works but the name doesn't resolve, the issue is in DNS — not the network connection itself.

---

## Issue 4: Domain Administrator Account Lockout

**Problem:**
While testing the account lockout Group Policy, the domain Administrator account was accidentally locked out by entering the wrong password too many times. DC01 could not be logged into through normal means.

**Fix:**
Booted DC01 into **Directory Services Restore Mode (DSRM)** using the DSRM password set during the domain controller promotion. Waited for the lockout observation window timer to expire, then restarted DC01 normally and logged back in as Administrator.

![Directory Services Restore Mode boot option selected on DC01](images/04-dsrm-boot.png)

**What I learned:**
Account lockout policies apply to every account in the domain — including the Administrator. Always test security policies using a regular user account first before applying them domain-wide. The DSRM password is the emergency backdoor into the domain controller and must always be documented and stored securely.

---

## Issue 5: Drive Mapping GPO Using Wrong Computer Name

**Problem:**
The Drive Mapping Group Policy was applied and verified with `gpresult /r` as applied to Client01, but the S: drive did not appear in File Explorer. Manually mapping the drive also failed with the error **"network name cannot be found."**

**Fix:**
Checked the shared folder properties on DC01 and discovered the actual computer name was `DC` — not `DC01` as assumed. The GPO was pointing to `\\DC01\SharedFiles` but the correct UNC path was `\\DC\SharedFiles`. After correcting the path in the GPO and running `gpupdate /force` on Client01, the drive mapped successfully.

**Commands used to diagnose and fix:**
```
hostname
gpupdate /force
gpresult /r
```

![Drive mapping GPO updated with the corrected UNC path \\DC\SharedFiles](images/05-drive-mapping-corrected.png)

![S: drive successfully mapped in File Explorer after correcting the computer name in the GPO](images/06-drive-mapping-success.png)

**What I learned:**
Network paths must use the exact computer name — even a small difference like `DC` vs `DC01` will cause the connection to fail completely. When a mapped drive or shared folder isn't connecting, verifying the actual computer hostname is a basic but critical troubleshooting step. The `hostname` command instantly confirms the correct name.

---

## Summary

| Issue | Root Cause | Fix |
|---|---|---|
| Client01 couldn't reach DC01 | Subnet mismatch between VMware and DC01 | Corrected subnet in VMware Virtual Network Editor |
| No internet access on Client01 | DHCP gateway set to network address instead of host IP | Corrected gateway to 192.168.10.1 in DHCP scope |
| Domain names not resolving | Stale DNS forwarder pointing to an inactive address | Updated DNS forwarder to correct address |
| Administrator account locked out | Lockout policy applied to all accounts including admin | Booted into DSRM and waited for lockout timer to expire |
| Drive mapping GPO not working | Wrong computer name used in the UNC path | Corrected UNC path in the GPO to match actual hostname |

Every issue in this lab was a real problem that required actual diagnosis and troubleshooting — not a scripted walkthrough. These are exactly the types of issues that come up in helpdesk and sysadmin roles and knowing how to methodically work through them is what separates a good technician from a great one.
