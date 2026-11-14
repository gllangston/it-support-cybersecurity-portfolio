# IT Support and Cybersecurity Portfolio

I use my Windows Server and VMware home lab to practice Active Directory administration, troubleshoot user access, and investigate security alerts. These case studies show what I checked, what I changed, and how I verified the outcome.

## IAM Labs

| Case study | What it demonstrates |
| --- | --- |
| [Active Directory Password Policy and GPO Scope](iam-labs/ad-gpo-password-policy/README.md) | Configured password and lockout settings, documented an AD unlock step, and examined policy scope. |
| [Active Directory OU Management](iam-labs/ad-ou-management/README.md) | Created and verified a hierarchy for users, groups, and computers. |
| [Finance File Share Permissions](iam-labs/finance-file-share-permissions/README.md) | Configured share and NTFS permissions and tested access from the client. |
| [Group Policy Management](iam-labs/ad-group-policy-management/README.md) | Linked a policy to an OU and verified the Control Panel restriction. |
| [User Management and Access Control](iam-labs/ad-user-management/README.md) | Practiced account administration, security group membership, and permission checks. |

## IT Support

| Case study | What it demonstrates |
| --- | --- |
| [Active Directory DNS Recovery](it-support/ad-dns-recovery/README.md) | Traced domain discovery failure to a stale DNS setting and verified recovery after removing the temporary workaround. |
| [Account Lockout and Password Reset](it-support/ad-account-lockout/README.md) | Confirmed a lockout, restored account access, and verified the user could sign in. |
| [User Access Troubleshooting](it-support/ad-user-access-troubleshooting/README.md) | Diagnosed missing Finance group membership and verified access after correcting it. |

## SOC Investigations

| Case study | What it demonstrates |
| --- | --- |
| [Spearphishing with an Attachment](soc-investigations/spearphishing-attachment/README.md) | Correlated email headers, VirusTotal, sandbox behavior, and Wazuh logs to assess a malicious attachment. |
| [Suspicious RDP Logons](soc-investigations/suspicious-rdp-logons/README.md) | Filtered Windows authentication events and reviewed failed logons targeting a privileged account. |
| [Suspicious Squid Proxy Traffic](soc-investigations/suspicious-squid-proxy-traffic/README.md) | Narrowed Wazuh proxy events to a suspicious outbound destination and checked domain reputation. |
| [Phishing Email and Proxy Logs](soc-investigations/phishing-email-proxy-investigation/README.md) | Reviewed proxy activity to assess user interaction with a reported phishing email. |
