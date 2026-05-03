Active Directory Lab: Account Lockout & Password Reset
Scenario

A user reported they were unable to log into their workstation after multiple failed login attempts.

Investigation
Attempted login and received account lockout error

![Account Locked](account-locked.png)

Verified correct login format (WINServer\jdoe) 
Accessed Active Directory Users & Computers (ADUC)
Located user account (jdoe)
Confirmed account was locked
Resolution
Unlocked the affected user account

![Account Unlock](account-unlock.png)

Reset passwords in accordance with domain password policy
Enabled “User must change password at next login”
Validation
User successfully logged in with updated credentials
Confirmed access to system restored
Skills Demonstrated
Active Directory user management
Account lockout troubleshooting
Password reset procedures
User authentication support
