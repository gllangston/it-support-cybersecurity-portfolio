# Suspicious RDP Logon Investigation
 Overview

A security alert showed suspicious RDP logons and failed login attempts between Jan 22–24, 2024.
The goal was to determine if this activity was malicious.

 Investigation

I filtered logs using:

Event ID 4625 (failed logons)
Time range from the alert

This narrowed the results down to 7 failed login attempts. <img width="1066" height="528" alt="Screenshot 2026-03-29 201907" src="https://github.com/user-attachments/assets/a017cacf-1d48-440a-b37e-19277e70930d" />


 Key Findings
Source IPs:
10.91.11.197 <img width="1004" height="597" alt="Screenshot 2026-03-29 193827" src="https://github.com/user-attachments/assets/be99ab8a-9f3a-48ab-af89-042062ace12e" />

10.91.14.201
Target account: Administrator <img width="1016" height="606" alt="Screenshot 2026-03-29 194608" src="https://github.com/user-attachments/assets/b3680971-1ac5-4226-b611-5c7259dbc026" />


Logon Type: 3 (network logon)
Authentication: NTLM
 Analysis
Multiple failed attempts → possible password guessing
Targeting Administrator → high-value account
NTLM + RDP → common in credential attacks

This behavior is consistent with a:
 Pass-the-Hash attack

 Conclusion

The activity is likely malicious due to repeated login failures and targeting of a privileged account.
