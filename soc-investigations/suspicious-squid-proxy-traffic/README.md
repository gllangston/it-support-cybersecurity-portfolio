# Suspicious Web Traffic via Squid Proxy

A web proxy (Squid) was recently integrated into the environment, resulting in a surge of alerts in Wazuh.
An analyst identified a potentially suspicious URI between 19:00–19:30 on February 1, 2024, prompting further investigation.

Investigation Process
Initial Triage
Accessed SIEM
Filtered logs by timeframe:
Feb 1, 2024 | 19:00 – 19:30
Identified 71 events related to Squid proxy activity 
Log Analysis
Focused on:
Squid: URI Connection Established
Squid: Forbidden events
Expanded individual logs to inspect fields:
data.url
full_log
Suspicious URL Identified
`www.getrekt[.]systems:443`
Observed repeated connections to this domain
Traffic used TCP_TUNNEL (HTTPS over port 443)
Certificate Validation Traffic
The proxy logs also showed a request to an Amazon Trust Services OCSP endpoint. This records a certificate status check. The suspicious destination was evaluated separately using the proxy connection record and reputation check.

Threat Intelligence Check
Checked domain reputation (e.g., VirusTotal)
Result:
1 vendor flagged the domain as malicious
 Findings
A user system initiated outbound connections to:
`www.getrekt[.]systems:443`
The domain exhibited suspicious characteristics:
Unusual naming convention
Flagged by threat intelligence
Traffic was encrypted (HTTPS), limiting visibility into payload

 Conclusion
The observed traffic to www.getrekt.systems is suspicious and potentially malicious.
Although hosted on legitimate infrastructure (Amazon), the domain itself may be leveraged for malicious activity.

## Evidence

- [wazuh filtered events](screenshots/wazuh-filtered-events.png)
- [wazuh initial events](screenshots/wazuh-initial-events.png)
- [amazon trust services ocsp](screenshots/amazon-trust-services-ocsp.png)
- [suspicious proxy destination](screenshots/suspicious-proxy-destination.png)
