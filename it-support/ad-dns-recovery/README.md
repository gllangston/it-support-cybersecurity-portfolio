# Active Directory DNS Troubleshooting and Domain Connectivity Restoration

**Project type:** VMware home lab | **Focus:** IAM, Windows administration, DNS, Active Directory troubleshooting  
**Date documented:** November 9, 2026 | **Status:** Domain controller discovery restored and verified

## Executive summary

A domain-joined Windows workstation (`WIN-VICTIM`) could not reliably discover its Active Directory domain controller (`WIN-SER2022`) in the `WinServer.local` domain. The workstation was configured to use an obsolete DNS server address (`192.168.230.136`), while the domain controller's current primary address was `192.168.230.200`. This affected domain authentication and administrative workflows used in an IAM training lab.

I investigated client DNS configuration, domain controller service discovery, DNS records, TCP connectivity, VMware NAT networking, and DNS server listener settings. I temporarily assigned the former address as a secondary IP on the domain controller to restore domain access, then used elevated privileges on the workstation to configure its DNS server to `192.168.230.200`. After refreshing DNS registration and retesting discovery, I removed the temporary address and verified the workstation still discovered the domain controller at `.200`.

**Result:** The existing workstation and AD organizational units were preserved; no VM rebuild was required.

> **Accuracy note:** Some `nslookup` queries to `.200` timed out while `Resolve-DnsName` and subsequent `nltest` tests succeeded. The precise cause of that inconsistent query behavior was not conclusively established. This case study documents the observed behavior without claiming a proven UDP or firewall root cause.

## Lab environment

| Component | Configuration |
|---|---|
| Hypervisor | VMware Workstation |
| Virtual network | VMnet8, NAT, `192.168.230.0/24` |
| Domain controller | Windows Server 2022, `WIN-SER2022` |
| AD domain | `WinServer.local` |
| Domain controller primary IP | `192.168.230.200` |
| Workstation | `WIN-VICTIM`, Windows client VM |
| Workstation IP | `192.168.230.138` |
| Affected user | `WINSERVER\jdoe` |
| AD services | Active Directory Domain Services, DNS |
| Existing OUs | Corp-Users, Finance, HR, IT, IAM Class |

```text
                    VMware Workstation / VMnet8 NAT
                             192.168.230.0/24
                                    |
                   +----------------+----------------+
                   |                                 |
       WIN-SER2022 / Domain Controller          WIN-VICTIM / Client
           192.168.230.200                       192.168.230.138
           AD DS + DNS                           WINSERVER\jdoe
                   +------------ WinServer.local ----+
```

## Problem and impact

- The workstation's configured DNS server was `192.168.230.136`, an address no longer assigned to the domain controller.
- `nltest /dsgetdc:WinServer.local /force` returned **1355 / ERROR_NO_SUCH_DOMAIN** during the failure.
- `nslookup WinServer.local` timed out when directed to the obsolete DNS address.
- Attempts to use domain administrator credentials for elevation were hindered while the domain could not be reached.
- A pre-existing Group Policy restricting Control Panel/Settings complicated GUI-based troubleshooting.

This blocked routine IAM exercises requiring domain logon, account administration, and a functional domain-joined workstation.

## Investigation and evidence

| Observation | Command / location | Interpretation |
|---|---|---|
| Workstation DNS pointed to `.136` | `Get-DnsClientServerAddress -AddressFamily IPv4` | Client was configured with an obsolete resolver address |
| Domain discovery failed | `nltest /dsgetdc:WinServer.local /force` | Client could not discover a DC at that point |
| AD DNS records could be queried explicitly via `.200` during earlier testing | `Resolve-DnsName ... -Server 192.168.230.200` | AD DNS data existed on the server |
| TCP port 53 reachable on `.200` | `Test-NetConnection 192.168.230.200 -Port 53` | TCP reachability; does **not** establish UDP reachability |
| DNS service running | `Get-Service DNS` | DNS Windows service was active |
| Server resolved `WinServer.local` using both IPs while alias existed | `Resolve-DnsName -Name WinServer.local -Server ...` | Both listener addresses answered locally |
| `nslookup` to `.200` timed out while PowerShell DNS queries later succeeded | `nslookup`, `Resolve-DnsName` | Inconsistent DNS query behavior; mechanism unconfirmed |
| Domain discovery eventually succeeded at `.200` | `nltest /dsgetdc:WinServer.local /force` | DC discovery restored |
| Discovery still succeeded after removal of `.136` | `nltest /dsgetdc:WinServer.local /force` | Workstation no longer needed temporary IP for DC discovery |

## Remediation timeline

1. **Identified obsolete DNS setting.** Compared the workstation's DNS configuration (`.136`) with the server's actual primary IPv4 address (`.200`).
2. **Validated network and DNS context.** Reviewed VMware VMnet8 NAT subnet and DHCP range, DNS service, listening addresses, AD records, and network tests.
3. **Created a recovery path.** Added `192.168.230.136/24` as a temporary secondary address on the domain controller's `Ethernet0` interface after checking for apparent address conflicts. This made the client's existing DNS setting usable long enough to regain domain access.
4. **Recovered domain login and administrative access.** The domain user `jdoe` was prompted to change an expired password and successfully changed it. Domain controller discovery succeeded while the temporary address was available.
5. **Corrected workstation DNS.** In elevated PowerShell on `WIN-VICTIM`, ran:

   ```powershell
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet0" -ServerAddresses 192.168.230.200
   Get-DnsClientServerAddress -InterfaceAlias "Ethernet0" -AddressFamily IPv4
   ```

6. **Investigated inconsistent results.** `nslookup` to `.200` timed out at points, but `Test-NetConnection` to TCP 53 succeeded and `Resolve-DnsName` to `.200` later returned A records. Avoided treating a single successful lookup as proof that every DNS path was healthy.
7. **Refreshed registration and verified discovery.** Used `ipconfig /registerdns` and `nltest /dsgetdc:WinServer.local /force`; the latter returned `WIN-SER2022.WinServer.local` at `192.168.230.200` and **The command completed successfully**.
8. **Removed temporary IP.** On Windows Server:

   ```powershell
   Remove-NetIPAddress -InterfaceAlias "Ethernet0" -IPAddress 192.168.230.136 -Confirm:$false
   Get-NetIPAddress -InterfaceAlias "Ethernet0" -AddressFamily IPv4
   ```

   Verified only `.200` remained as the server's IPv4 address on the interface.
9. **Performed post-removal validation.** On the workstation:

   ```powershell
   ipconfig /flushdns
   nltest /dsgetdc:WinServer.local /force
   ```

   The domain controller was again discovered at `.200`.
10. **Reviewed DNS Manager.** The `WinServer.local` zone showed domain and server A records at `.200` and workstation A record at `.138`, with no `.136` record visible. The server's DNS **Interfaces** tab showed `.200` and an IPv6 address selected; `.136` was no longer listed.

## Outcome and validation

- **Verified:** `nltest` successfully located `WIN-SER2022.WinServer.local` at `192.168.230.200` after removal of the temporary `.136` IP.
- **Verified:** DNS Manager displayed expected A records for the domain controller and workstation.
- **Verified:** DNS listener configuration no longer listed `.136`.
- **Preserved:** Existing domain controller, Windows workstation, domain users, and OUs.
- **Not conclusively proven:** Why `nslookup` intermittently timed out to `.200`; complete long-term DNS stability, secure-channel status, and every IAM application workflow were outside the documented verification.

## Lessons learned

1. **Active Directory depends on DNS.** Domain logon and DC discovery rely on finding AD-specific DNS records, especially SRV records.
2. **An OU is not a workstation.** OUs organize AD objects; the domain-joined Windows client VM is the workstation used for IAM practice.
3. **Compare intended versus actual state.** A stale client resolver setting can break authentication even when the DC and DNS service are running.
4. **Separate network reachability from name resolution.** TCP port 53 connectivity alone does not prove UDP DNS queries work.
5. **Use reversible changes.** A temporary IP alias restored access without rebuilding; it was removed after verification.
6. **Verify after cleanup.** A fix is more convincing when it still works after the workaround is removed.
7. **Preserve evidence and state uncertainty.** Record successful and failed commands without inventing a definitive cause.

## Next lab hardening actions

- Take VMware snapshots or backups of both VMs at a known-good state.
- Document domain administrator and local recovery account access securely (not in GitHub).
- Check the workstation's secure channel using `Test-ComputerSecureChannel` from an appropriate elevated client session.
- Consider checking DNS SRV lookups and normal domain sign-in again after VM restarts.
- Keep AD DNS settings consistent and avoid hard-coded obsolete DNS addresses.

## Skills demonstrated

`Active Directory` · `DNS` · `Windows Server 2022` · `PowerShell` · `Domain Controller Discovery` · `IAM Fundamentals` · `Windows Troubleshooting` · `VMware Workstation` · `Incident Documentation`
