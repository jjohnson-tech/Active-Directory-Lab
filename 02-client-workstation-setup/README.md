# 02 - Client Workstation Setup

This section covers the setup and configuration of Client01, the Windows 10 workstation that joins the mydomainlab.local domain. The goal is to simulate a real end-user machine connecting to a corporate network and authenticating through Active Directory.

---

## Step 1: Create the Virtual Machine

Created a new virtual machine in VMware for Client01 with the following configuration:

- **Network Adapter:** Host-Only — connects Client01 to the internal lab network where DC01 lives
- A single NIC is all that's needed since Client01 gets internet access through DC01's NAT routing

<img width="1435" height="732" alt="Client VM" src="https://github.com/user-attachments/assets/9c023a4b-399a-4822-8259-fb2b0c1579f8" />


---

## Step 2: Install Windows 10 Enterprise

Installed **Windows 10 Enterprise** on Client01 using an offline account during setup. After installation, installed **VMware Tools** to improve performance and enable clipboard sharing and display scaling.

<img width="1262" height="725" alt="client desktop" src="https://github.com/user-attachments/assets/533d24f6-297d-45ee-a913-cd1760039398" />


---

## Step 3: Configure DNS to Point to DC01

Opened the network adapter settings on Client01 and manually set the DNS server to point to DC01's static IP address:

- **DNS Server:** 192.168.10.1

This is a critical step — without pointing DNS to DC01, Client01 cannot resolve the domain name `mydomainlab.local` and will fail to join the domain.

<img width="692" height="477" alt="client dns set" src="https://github.com/user-attachments/assets/28969451-a6fd-441b-9bef-7057677bdd7b" />


---

## Step 4: Verify Network Connectivity

Ran a ping from Client01 to DC01 to confirm the two machines can communicate on the internal Host-Only network before attempting to join the domain.

```
ping 192.168.10.1
```

A successful ping confirms the network adapter is configured correctly and DC01 is reachable.

<img width="642" height="206" alt="client ping dc" src="https://github.com/user-attachments/assets/07758fff-4f7b-49d9-b499-e6c65317a013" />


---

## Step 5: Join Client01 to the Domain

Joined Client01 to the **mydomainlab.local** domain through System Properties:

1. Right-clicked **This PC → Properties → Advanced System Settings**
2. Clicked the **Computer Name** tab → **Change**
3. Selected **Domain** and entered `mydomainlab.local`
4. Entered **domain administrator credentials** when prompted
5. Received the confirmation message — **"Welcome to the mydomainlab.local domain"**
6. Rebooted Client01 to apply the changes

<img width="1295" height="665" alt="joined domain" src="https://github.com/user-attachments/assets/9434ef6c-9cba-4520-bff8-f4ab76d95cf0" />

---

## Step 6: Log In with Domain Credentials

After rebooting, the login screen displayed the **"Other user"** option with the `mydomainlab.local` domain listed below the credentials fields. This confirms Client01 is now domain-joined and ready to authenticate against Active Directory.

Logged in using domain administrator credentials to verify access.

<img width="1410" height="702" alt="other login" src="https://github.com/user-attachments/assets/0d3255ab-a3ff-41d6-9e39-2c6f49037758" />


---

## Step 7: Confirm Domain Authentication with whoami

Opened Command Prompt and ran `whoami` to verify the session is authenticated against the domain rather than a local account:

```
whoami
```

Output confirmed: `mydomainlab\administrator`

<img width="1020" height="537" alt="whoami" src="https://github.com/user-attachments/assets/095f014a-b2cb-4cb5-8117-1b7dfb67dfb7" />


---

## Step 8: Verify DHCP Assignment

Ran `ipconfig /all` to confirm Client01 received its network settings automatically from the DHCP server on DC01:

```
ipconfig /all
```

Verified the following were assigned correctly:
- **IP Address:** 192.168.10.x (within the DHCP scope range)
- **Default Gateway:** 192.168.10.1 (DC01)
- **DNS Server:** 192.168.10.1 (DC01)

<img width="1098" height="563" alt="ipconfig confirm" src="https://github.com/user-attachments/assets/d94c543a-bfbc-4c81-8525-32f83e485daa" />


---

## Step 9: Verify Internet Access Through DC01

Opened a web browser on Client01 and loaded a webpage to confirm internet access is working through DC01's NAT routing.

This verifies the full network path: Client01 → DC01 (NAT) → Internet.

<img width="1388" height="666" alt="first browse" src="https://github.com/user-attachments/assets/931001bc-5db1-4a5e-a372-81380cf66612" />


---

## Summary

By the end of this section, Client01 was fully configured and verified as a domain-joined workstation:

| Check | Result |
|---|---|
| VM created with Host-Only adapter | ✅ |
| Windows 10 installed | ✅ |
| DNS pointing to DC01 | ✅ |
| Network connectivity to DC01 confirmed | ✅ |
| Domain join successful | ✅ |
| Domain login verified | ✅ |
| DHCP assignment confirmed | ✅ |
| Internet access through NAT verified | ✅ |

Client01 is now ready to be used for Active Directory administration tasks covered in the next section.
