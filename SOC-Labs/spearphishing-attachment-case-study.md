
Case Study 

Spearphishing with an Attachment  

“We received a ticket for a user who got a suspicious email with an attachment that looked like it came from Adobe. 

I analyzed the email and noticed inconsistencies, like issues in the headers and an invalid DKIM signature, which made it suspicious. 

I then took the file hash and checked it in VirusTotal, where multiple vendors flagged it as malicious and tied it to a known exploit, confirming the file wasn’t safe. <img width="1414" height="770" alt="VirusTotal Hits" src="https://github.com/user-attachments/assets/7a310d03-7cd1-421a-898f-50135ad2a2a6" />


After that, I wanted to see if the system was actually impacted, so I looked at the behavior and logs (Wazuh). I then ran the file in a sandbox environment (AnyRun), that’s when I saw that PowerShell was triggered, and the machine-made outbound connections to a known malicious IP, which isn’t normal for a Word document. <img width="1407" height="826" alt="Wazuh Log PowerShell + IP" src="https://github.com/user-attachments/assets/7cc033a0-3e8f-4bbe-9e11-2d18ead422e7" />


So based on that, I concluded the attachment was malicious and the system was likely compromised.” 

From there, I wanted to determine the scope of the incident, so I checked the environment for any additional activity related to the same indicators—like the malicious IP, domain, and file hash—over a recent period. 

I didn’t find any similar activity on other systems, which indicated the incident was isolated to that single user. 

 

 
