# AD Group Based Access Control and Validation

In my VMware home lab, I followed up on the [Jade-Sec bulk onboarding project](../jade-sec-ad-bulk-onboarding/README.md). After verifying all 200 accounts in their staged, disabled state, I enabled two Wave 1 test accounts for a Day 1 access pilot. I set a temporary password for each account and confirmed that Windows prompted both users to change it at first sign-in.

On Windows Server 2022, I created the `ACQ-Finance-Test` SMB share, removed `Everyone` from its share permissions, and granted the `ACQ-Finance-Users` Active Directory security group **Change** and **Read** at the share level and **Modify** at the NTFS level. I checked the Finance test account’s group membership in Active Directory Users and Computers.

I tested the same share from a domain-joined Windows 10 client in separate Finance and HR user sessions. The initial attempt used an incorrect server name. I copied the exact UNC path from the folder’s **Sharing** tab and used that path for both tests.

| Test account | Expected result | Observed result |
| --- | --- | --- |
| Finance | Open the Finance share | File Explorer opened the share and displayed its empty contents. |
| HR | Sign in to Windows and be denied access to the Finance share | Windows sign-in succeeded. File Explorer reported insufficient permission to open the share. |

The pilot verified folder access for the Finance account and access denial for the HR account. The NTFS screenshot shows **Modify** was configured. File creation, editing, and deletion remain untested. The activation pilot covered two test accounts.

<details>
<summary>Screenshot evidence</summary>

**Finance group membership**

![Active Directory Member Of tab for the Finance test account](screenshots/01-finance-group-membership.webp)

**Finance NTFS permissions**

![NTFS Security tab showing Modify allowed for the Finance security group](screenshots/02-finance-ntfs-permissions.webp)

**Finance share opened**

![File Explorer opened the Finance share and displayed an empty folder](screenshots/03-finance-share-opened.webp)

**HR share access denied**

![Windows reported insufficient permission to open the Finance share during the HR test](screenshots/04-hr-share-access-denied.webp)

</details>

The [Okta and ServiceNow SAML pilot](../okta-servicenow-sso/README.md) documents the related application-access work.
