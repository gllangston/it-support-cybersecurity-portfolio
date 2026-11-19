# AD Department Transfer and Access Validation

**Jade-Sec IAM home lab · Mover stage of identity lifecycle management**

I simulated Cameron Carter’s transfer from Finance to HR in my Windows Server 2022 and Windows client VMware lab. I updated his role information and security group memberships, then tested that his previous Finance share was denied and his new HR share was accessible. I also created, saved, reopened, and deleted a test file in the HR share.

This exercise builds on [AD Group Based Access Control and Validation](../ad-group-based-access-control-and-validation/README.md) and the [200-user acquisition onboarding project](../jade-sec-ad-bulk-onboarding/README.md).

## Scenario and intended access

The lab scenario represented an approved department transfer. I used Human Resource Operations Manager as the simulated destination role. An actual HR request and approval workflow were outside this exercise.

| Item | Before transfer | After transfer |
| --- | --- | --- |
| Department | Finance | HR |
| Job title | Finance Operations Manager | Human Resource Operations Manager |
| Department security group | ACQ-Finance-Users | ACQ-HR-Users |
| Primary group | Domain Users | Domain Users |
| Finance share access | Previously tested successfully | Denied after transfer |
| HR share access | HR test share created during this exercise | Opened successfully |

## Update role information and access

In Active Directory Users and Computers, I reviewed Cameron’s Organization and Member Of tabs. His department was Finance, and his memberships were ACQ-Finance-Users and Domain Users.

I changed Department to HR and Job Title to Human Resource Operations Manager. Member Of still showed the Finance group afterward. This demonstrated that editing those descriptive attributes did not automatically change the group permissions in this lab.

I resolved ACQ-HR-Users with Check Names, added it, and removed ACQ-Finance-Users. I confirmed that ACQ-HR-Users and Domain Users remained.

On the domain-joined client, I signed Cameron out and back in before testing. A fresh sign-in provided updated group information for the access tests.

## Verify removal of Finance access

I opened the same Finance network share that Cameron had previously accessed. Windows reported insufficient permission to open it.

This was the expected outcome for the previous department’s resource.

## Configure the HR test resource

An HR folder was absent from the server’s C drive, so I created `C:\ACQ-HR-Test` and shared it as ACQ-HR-Test.

| Permission layer | Configuration |
| --- | --- |
| SMB share | Removed Everyone and granted ACQ-HR-Users Change and Read |
| NTFS | Granted ACQ-HR-Users Modify for this folder, subfolders, and files |
| Administrative entries | Retained SYSTEM, Administrators, and CREATOR OWNER |

The new folder inherited two Users permission entries from C:\. An attempt to remove an inherited entry produced a Windows warning. I disabled inheritance on the HR test folder and selected the option to convert inherited entries into explicit permissions. I then removed both Users entries and saved the changes.

The final Advanced Security Settings showed ACQ-HR-Users with Modify and the retained administrative entries, with Inherited from set to None.

## Verify new access and file operations

From Cameron’s session on the Windows client, I opened the HR network share. File Explorer displayed its empty contents.

The Explorer New menu offered a folder option without a text-document option. I used Notepad to enter test text and saved it directly to `HR-Transfer-Test.txt` through the HR share’s network path.

| Test | Expected outcome | Observed outcome |
| --- | --- | --- |
| Open Finance share | Denied | Permission denial displayed |
| Open HR share | Allowed | Folder opened |
| Create and save HR-Transfer-Test.txt | Allowed | File saved |
| Close and reopen the file | Saved text persists | Test text remained |
| Close and delete the test file | Allowed | File deleted |

The supplied lab evidence shows the Finance department and title before the transfer, the HR department and updated title entered during the change, the before-and-after group memberships, the Finance access denial, and HR share and NTFS permissions. It also shows HR-Transfer-Test listed in the share and its text displayed in Notepad. Saving the role changes, the fresh sign-in, reopening the file with its text intact, and successful deletion were confirmed during the guided manual lab. The submitted file screenshots show the file before deletion.

The supporting screenshots are retained in the lab conversation and are pending publication approval.

## Lessons and interview explanation

- **Least privilege.** I removed Finance membership when that access was no longer required for the simulated role.
- **Privilege creep.** Adding HR membership while retaining Finance membership would leave obsolete department access.
- **Permission inheritance.** I identified broad entries inherited from the parent and converted them before removing them from the test folder.
- **Validation.** I checked both the removal of previous access and the ability to work in the new resource.

> In my Active Directory home lab, I simulated an employee transfer from Finance to HR. I updated the department and title, replaced the Finance security group membership with HR membership, and used a fresh client sign-in for testing. I verified that the Finance share returned a permissions denial and the HR share opened successfully. While setting up the HR resource, I resolved an inherited-permissions warning and restricted its NTFS permissions. I then confirmed that the user could save, reopen, and delete a test file in the HR share.

The completed scope covers one AD user’s transfer and access to two SMB shares. Application access through Okta and ServiceNow was outside this transfer test.
