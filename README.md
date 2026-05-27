# Active Directory File Shares – Managing Permissions with Security Groups

This project demonstrates how to configure network file shares with role-based access control using Active Directory security groups on Windows Server 2019. Four shared folders were created on the Domain Controller with different permission levels. A standard domain user tested access to each folder from a Windows 11 client, verifying read-only, read/write, and restricted access behaviors. A dedicated security group was then created and granted access to a restricted folder — after adding the user to the group and re-authenticating, access was successfully granted.

This lab simulates how IT administrators manage file share permissions in a real enterprise environment using the principle of least privilege.

---

## Environments and Technologies Used

- Oracle VirtualBox
- Windows Server 2019 (Domain Controller — DC-1)
- Windows 11 Enterprise (Client — Client-1)
- Active Directory Users and Computers (ADUC)
- Windows File Explorer (Network File Sharing)
- Command Prompt (`gpupdate /force`)

---

## Prerequisites

This lab builds on an existing Active Directory environment:

- Domain: `mydomain.com`
- Domain Controller: DC-1 (Windows Server 2019)
- Client machine: Client-1 (Windows 11, domain-joined)
- Admin account: Thomas (Domain Admin)
- Test user: Solomon Yohanis (Domain User)

---


## Step 1 – Create the Shared Folders on DC-1

On DC-1, File Explorer was opened and four folders were created directly on the `C:\` drive by right-clicking on empty space and selecting **New → Folder**.

![Server Manager Dashboard](screenshots/01-server-manager-dashboard.png)

DC-1 Server Manager Dashboard showing the starting state — roles AD DS, DNS, and File and Storage Services are active.

![New Folder Context Menu](screenshots/02-new-folder-context-menu.png)

Right-clicking inside C:\ and navigating to New → Folder to begin creating the shared folders.

![Four Folders Created](screenshots/03-four-folders-created.png)

C:\ drive showing all four folders created: Read-Access, Write-Access, No-Access, and Accounting — 4 items selected in the status bar.

---

## Step 2 – Configure Share Permissions

Each folder was right-clicked → **Properties** → **Sharing** tab → **Share...** to open the Network access dialog, where the appropriate group and permission level were assigned.

### Read-Access

![Read Access Properties General](screenshots/04-read-access-properties-general.png)

Read-Access folder Properties — General tab confirming the folder was just created (0 bytes, empty, located at C:\).

![Read Access Right Click Properties](screenshots/05-read-access-properties-general.png)

Right-clicking Read-Access and selecting Properties to begin the sharing configuration.

![Read Access Sharing Not Shared](screenshots/06-read-access-sharing-not-shared.png)

Read-Access Properties — Sharing tab showing the folder is currently Not Shared. Clicking Share... to proceed.

![Read Access Network Access Empty](screenshots/07-read-access-network-access-empty.png)

Network access dialog opened for Read-Access — only Thomas (Owner) is listed. Ready to add Domain Users.

![Read Access Adding Domain Users](screenshots/08-read-access-adding-domain-users.png)

"Domain users" typed into the search field — clicking Add to grant all domain users access.

![Read Access Domain Users Read](screenshots/09-read-access-domain-users-read.png)

Domain Users added to Read-Access with Read permission only — Thomas remains as Owner.

![Read Access Share Confirmed](screenshots/10-read-access-share-confirmed.png)

"Your folder is shared" confirmation — network path confirmed as \\DCSERVER\Read-Access.

---

### Write-Access

![Write Access Sharing Not Shared](screenshots/11-write-access-sharing-not-shared.png)

Write-Access Properties — Sharing tab showing the folder is currently Not Shared. Clicking Share... to proceed.

![Write Access Domain Users ReadWrite](screenshots/12-write-access-domain-users-readwrite.png)

Domain Users added to Write-Access with Read/Write permission — allows creating, editing, and deleting files.

![Write Access Share Confirmed](screenshots/13-write-access-share-confirmed.png)

"Your folder is shared" confirmation — network path confirmed as \\DCSERVER\Write-Access.

![Write Access Sharing Tab Shared](screenshots/14-write-access-sharing-tab-shared.png)

Write-Access Properties Sharing tab now shows Shared status with network path \\DCSERVER\Write-Access.

---

### No-Access

![No Access Sharing Not Shared](screenshots/15-no-access-sharing-not-shared.png)

No-Access Properties — Sharing tab showing the folder is currently Not Shared. Clicking Share... to proceed.

![No Access Domain Admins Added](screenshots/16-no-access-domain-admins-added.png)

Domain Admins added to No-Access with Read/Write permission — standard Domain Users will be completely blocked.

![No Access Share Confirmed](screenshots/17-no-access-share-confirmed.png)

"Your folder is shared" confirmation — network path confirmed as \\DCSERVER\No-Access.

![No Access Sharing Tab Shared](screenshots/18-no-access-sharing-tab-shared.png)

No-Access Properties Sharing tab now shows Shared status with network path \\DCSERVER\No-Access.

---

## Step 3 – Access the Shares from Client-1

On Client-1, logged in as domain user Solomon Yohanis. In File Explorer, `\\dcserver` was typed into the address bar to browse the Domain Controller's available network shares.

![Client1 Network Right Click](screenshots/19-client1-network-right-click.png)

Client-1 File Explorer on Windows 11 — right-clicking Network in the left panel.

![Client1 Type dcserver Path](screenshots/20-client1-type-dcserver-path.png)

Typing \\dcserver into the address bar on Client-1 — autocomplete suggestion appears.

![Client1 Network Folders Visible](screenshots/21-client1-network-folders-visible.png)

Client-1 successfully browsing \\dcserver — all shared folders visible: NETLOGON, No-Access, Read-Access, SYSVOL, and Write-Access.

---

## Step 4 – Test Permissions as a Standard Domain User

With Solomon Yohanis logged in as a standard domain user on Client-1, access to each shared folder was tested to verify the permissions were enforced correctly.

### Read-Access

![Read Access Opens Client](screenshots/22-read-access-opens-client.png)

Standard domain user successfully opens the Read-Access folder — folder is accessible. 

![Read Access Write Denied](screenshots/23-read-access-write-denied.png)

Attempting to create a file inside Read-Access returns "Destination Folder Access Denied — You need permission to perform this action" — read-only enforcement confirmed. 

---

### Write-Access

![Write Access Opens Client](screenshots/24-write-access-opens-client.png)

Standard domain user successfully opens the Write-Access folder — folder is accessible. 

![Write Access File Created](screenshots/25-write-access-file-created.png)

Text file named "permission granted" successfully created inside Write-Access — Read/Write permission confirmed. 

---

### No-Access

![No Access Folder Selected](screenshots/26-no-access-folder-selected.png)

No-Access folder selected in \\dcserver — about to attempt access as a standard domain user.

![No Access Denied Client](screenshots/27-no-access-denied-client.png)

Network Error: "Windows cannot access \\dcserver\No-Access — You do not have permission to access this folder." — Domain Admins only restriction confirmed. 

---

## Step 5 – Create the ACCOUNTANTS Security Group

Back on DC-1, a dedicated security group was created in Active Directory Users and Computers to control access to the Accounting share. Rather than granting permissions to individual users, a group is used so access can be managed simply by adding or removing members.

![ADUC New OU Context Menu](screenshots/28-aduc-new-ou-context-menu.png)

Active Directory Users and Computers — right-clicking the domain root → New → Organizational Unit to create a container for the security group.

![New OU Named GROUPS](screenshots/29-new-ou-named-groups.png)

New Object — Organizational Unit dialog with "_GROUPS" entered as the name. The underscore prefix keeps it sorted at the top of the AD tree for easy navigation.

![GROUPS OU New Group Menu](screenshots/30-groups-ou-new-group-menu.png)

Inside the _GROUPS OU — right-clicking → New → Group to create the ACCOUNTANTS security group.

![New Group ACCOUNTANTS](screenshots/31-new-group-accountants.png)

New Object — Group dialog: Group name set to "ACCOUNTANTS", Group scope set to Global, Group type set to Security. Created in mydomain.com/_GROUPS.

![ACCOUNTANTS Group Created](screenshots/32-accountants-group-created.png)

ACCOUNTANTS security group successfully created and visible in the _GROUPS OU — listed as Security Group type.

---

## Step 6 – Share the Accounting Folder with the ACCOUNTANTS Group

With the security group created, the Accounting folder was shared and the ACCOUNTANTS group was granted Read/Write access.

![Accounting Sharing Not Shared](screenshots/33-accounting-sharing-not-shared.png)

Accounting folder Properties — Sharing tab showing the folder is currently Not Shared. Clicking Share... to proceed.

![Accounting Add ACCOUNTANTS Group](screenshots/34-accounting-add-accountants-group.png)

"ACCOUNTANTS" typed into the Network access search field — clicking Add to grant the security group access.

![Accounting ACCOUNTANTS ReadWrite Tooltip](screenshots/35-accounting-accountants-readwrite-tooltip.png)

ACCOUNTANTS group added with Read/Write permission — tooltip confirms MYDOMAIN\ACCOUNTANTS with Read/Write access, allowing users to open, change, and create files.

![Accounting Share Confirmed](screenshots/36-accounting-accountants-readwrite-confirmed.png)

"Your folder is shared" confirmation — network path confirmed as \\DCSERVER\Accounting.

---

## Step 7 – Test Accounting Access Before Adding User to Group

Before adding Solomon Yohanis to the ACCOUNTANTS group, access to the Accounting share was tested from Client-1 to confirm the restriction was in effect.

![Accounting Access Denied No Permission](screenshots/37-accounting-access-denied-no-permission.png)

Network Error: "Windows cannot access \\dcserver\Accounting — You do not have permission to access this folder." — confirmed blocked for users outside the ACCOUNTANTS group. 

---

## Step 8 – Add Solomon Yohanis to the ACCOUNTANTS Group

Back on DC-1, Solomon Yohanis was added to the ACCOUNTANTS security group through Active Directory Users and Computers.

![Solomon Member Of Before](screenshots/38-solomon-member-of-before.png)

Solomon Yohanis Properties — Member Of tab showing current group membership: Domain Users only (mydomain.com/Users).

![Select Groups Dialog Empty](screenshots/39-select-groups-dialog-empty.png)

Select Groups dialog opened after clicking Add — ready to search for the ACCOUNTANTS group in mydomain.com.

![Select Groups ACCOUNTANTS Typed](screenshots/40-select-groups-accountants-typed.png)

"ACCOUNTANTS" typed into the object name field — clicking OK to confirm and add the group membership.

![Solomon Member Of ACCOUNTANTS Added](screenshots/41-solomon-member-of-accountants-added.png)

Solomon Yohanis Member Of tab now shows two groups: ACCOUNTANTS (mydomain.com/_GROUPS) and Domain Users (mydomain.com/Users) — group membership successfully updated. 

---

## Step 9 – Verify Accounting Access

![Accounting Now Visible Client](screenshots/42-accounting-now-visible-client.png)

Client-1 browsing \\dcserver and selecting Accounting folder.

![Accounting Access Granted](screenshots/43-accounting-access-granted.png)

Solomon Yohanis successfully opens the Accounting folder — access granted as a member of the ACCOUNTANTS security group. 

![Accounting File Created](screenshots/44-accounting-file-created.png)

Text file named "permission granted" successfully created inside the Accounting folder — Read/Write permission via group membership confirmed. 

---

## Conclusion
This lab successfully demonstrated how to configure and manage network file share permissions using Active Directory security groups on Windows Server 2019. By creating four shared folders with different permission levels and testing access from a domain-joined Windows 11 client, the lab confirmed that share permissions were enforced correctly for each group.
The most important concept demonstrated is role-based access control through security groups. Rather than assigning permissions to individual users, access to the Accounting share was controlled entirely through the ACCOUNTANTS group — meaning access can be granted or revoked simply by managing group membership, with no changes to the share configuration required. This is how permissions are managed in real enterprise environments and is a fundamental skill for IT administrators and help desk professionals.
