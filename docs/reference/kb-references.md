---
title: "KB References & Quick Links"
category: "Reference"
source: "References (KB).pdf"
---

# KB References & Quick Links

A collected reference notebook of quick links, tips, and technical notes organized by topic.

---

## Apple / iOS

- **iOS Firmware Downloads:** https://ipsw.me/

---

## Audit Logs (Microsoft 365)

- **What is audited and how long does it take:** [Search the audit log in the Microsoft 365 compliance center](https://docs.microsoft.com/en-us/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)
- **Setup Cisco Umbrella MSP Account:** Create a Cisco ID at https://id.cisco.com/signin/register

---

## Computer Performance — NIC Latency Improvements

To improve latency on a NIC:

1. **Update NIC Drivers** — Updated drivers can include performance improvements and bug fixes.
2. **Enable Jumbo Frames** — Increases data transmission efficiency (more beneficial for throughput than latency).
3. **Disable Interrupt Moderation** — Reduces CPU load-induced latency at the cost of higher CPU usage.
4. **Adjust Receive and Transmit Buffers** — Increasing buffer size can improve performance, but setting too high can have adverse effects.
5. **Enable Flow Control** — Helps control data rate, preventing packet loss during high traffic.
6. **Disable Energy Efficient Ethernet (EEE)** — Prevents the NIC from having to "wake up" from a low-power state.
7. **Disable Green Ethernet** — Similar to EEE; disabling can provide a slight latency improvement.
8. **Optimize for Performance over Power Saving** — Select performance priority in the NIC's advanced properties.
9. **Tweak TCP/IP Settings** — Via Windows Registry or third-party utilities (proceed with caution).
10. **Use Wired Connections** — Wired Ethernet is generally faster and more stable than Wi-Fi.

---

## Cisco Umbrella

| Resource | Link |
|----------|------|
| Documentation | https://docs.umbrella.com/ |
| User Guide | https://docs.umbrella.com/deployment-umbrella/docs/welcome-to-cisco-umbrella |
| Service Status | http://status.umbrella.com/ |
| Protection Check | https://welcome.umbrella.com/ |
| Test Domains | https://support.umbrella.com/hc/en-us/articles/115000411528 |
| Test Domain IPs | https://support.umbrella.com/hc/en-us/articles/115001357688 |
| Policy Debug | https://policy-debug.checkumbrella.com/ |
| Cisco Secure Client Prerequisites | https://docs.umbrella.com/deployment-umbrella/docs/prerequisites-5 |
| Cisco Secure Client Protection Status | https://docs.umbrella.com/deployment-umbrella/docs/ipv4-and-ipv6-dns-protection-status-1 |
| Command Line and RMM Reference | https://support.umbrella.com/hc/en-us/articles/18584514390932 |
| Support Email | Umbrella_support@cisco.com |

**Umbrella Profile JSON location:**
```
C:\ProgramData\Cisco\Cisco Secure Client\Umbrella\OrgInfo.json
```
Restart the service after any changes.

---

## MFA: Windows Hello + Authenticator vs. Windows Hello + Duo

### Trust & Integration
- **Hello + Authenticator (Microsoft native):** Full native integration with Windows sign-in, Azure AD, Conditional Access, and Intune. Supports phishing-resistant MFA with number matching. Enables passwordless sign-in via FIDO2 or Hello for Business.
- **Hello + Duo:** Handles the second factor but can't natively enforce all Conditional Access conditions. Works well for non-Microsoft apps, legacy VPNs, and RDP gateways.

### Security Strength Comparison

| Factor | Hello + Authenticator | Hello + Duo |
|--------|-----------------------|-------------|
| Phishing Resistance | High with passwordless/number matching | High with Duo push and number matching |
| Policy Enforcement | Unified in Azure AD Conditional Access | Split between Azure AD and Duo |
| User Experience | Seamless, passwordless, SSO across Microsoft | Slightly more friction, may prompt separately |
| Legacy/Non-Microsoft Apps | Requires custom integration | Integrates more easily with legacy apps & VPNs |

### When Authenticator Wins
- All-in on Microsoft 365/Azure AD/Intune.
- Want passwordless sign-in with phishing-resistant MFA built into Windows Hello.
- Fewer moving parts and a single policy engine.
- Native protection for OAuth and WS-Fed.

### When Duo Still Adds Value
- Many non-Microsoft apps not integrated with Azure AD.
- Need MFA for VPNs, SSH, or RDP that can't use Azure MFA natively.
- Need MFA logging/reporting independent of Microsoft.
- Compliance requirements specify a separate MFA provider.

> **Bottom line:** If your environment is primarily Microsoft-based, Windows Hello + Conditional Access + Microsoft Authenticator is usually more secure and streamlined. Duo makes the most sense for cross-platform MFA or layered defense requirements.

---

## Dell — Power Light Blink Codes

| Blink Pattern | Problem | Resolution |
|---------------|---------|------------|
| 1, 1 | MBIST: TPM Detection Failure | Contact Dell support |
| 1, 2 | MBIST: SPI Flash — Unrecoverable Failure | Contact Dell support |
| 1, 5 | i-Fuse Failure | Contact Dell support |
| 1, 6 | EC Internal Failure | Disconnect all power, drain flea power (hold power button) |
| 2, 1 | CPU Failure | Run Intel CPU diagnostic tools |
| 2, 2 | Bad Motherboard/PSU/PSU cabling | Flash latest BIOS; if persists, contact Dell |
| 2, 3 | No Memory/RAM detected | Reseat memory, swap known-good module |
| 2, 4 | Memory RAM Failure | Swap known-good DIMM |
| 2, 5 | Invalid Memory Installed | Swap known-good DIMM |
| 2, 6 | Motherboard/Chipset Error | Flash latest BIOS |
| 2, 7 | LCD Failure — SBIOS message | Flash latest BIOS |
| 2, 8 | LCD Failure — EC detection of Power Rail failure | Contact Dell support |
| 3, 1 | RTC Power Failure / CMOS Battery Failure | Swap known-good CMOS battery |
| 3, 2 | PCI or Video Card/Chip Failure | Contact Dell support |
| 3, 3 | BIOS Recovery 1: Image not found | Flash latest BIOS |
| 3, 4 | BIOS Recovery 2: Image found but invalid | Flash latest BIOS |
| 3, 5 | Power Rail Failure | Contact Dell support |
| 3, 6 | Paid SPI Volume Error | Flash latest BIOS |
| 3, 7 | ME Error — timeout waiting on ME to reply | Contact Dell support |
| 4, 1 | Memory DIMM Power Rail Failure | Contact Dell support |
| 4, 2 | CPU Power Cable Connection Issue | Run PSU BIST; if passes but persists, contact Dell |

Reference: https://www.dell.com/support/kbdoc/en-us/000126021/a-reference-guide-to-the-dell-optiplex-diagnostic-indicators

---

## Wolf Security (HP)

Wolf Security is highly problematic to computers. Multiple components must all be removed:

- HP Wolf Security App
- HP Assess and Report
- HP Wolf Security Console
- HP Wolf Security Updater

> **Note:** The HP Wolf Security Updater requires all other Wolf Security apps to be uninstalled before it can be removed.

---

## Azure Virtual Desktop (AVD) / Cloud PC / Kiosk

### AVD Kiosk Setup (Windows 365 / Cloud PC)
- Deploy via Autopilot Self-Deploying profile. Set "Convert all target devices to Autopilot" to Yes.
- Deploy the Remote Desktop app from the **Microsoft Store (new)** in Intune for auto-updating support.
- Configure a Kiosk profile: **Devices → Policy → Configuration profiles → Windows 10 and later → Templates → Kiosk**.
- Disable **Windows Hello for Business** on Kiosk endpoints via an Identity Protection configuration profile.
- Use a dynamic Azure AD group for Kiosk Users (exclude Global Admins).
- Auto-subscribe Remote Desktop Client via a provisioning policy using settings catalog.

### Speed Up AVD Performance
```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
DWORD: DWMFRAMEINTERVAL
Value: 15 (decimal)
```

### AVD Resources
- [AVD Experience Estimator](https://azure.microsoft.com/en-us/products/virtual-desktop/experience-estimator/)
- [Enable RDP Shortpath](https://learn.microsoft.com/en-us/azure/virtual-desktop/configure-rdp-shortpath?tabs=public-networks)
- [RDP Bandwidth Requirements](https://learn.microsoft.com/en-us/azure/virtual-desktop/rdp-bandwidth)
- [Remote Desktop App Download](https://learn.microsoft.com/en-us/azure/virtual-desktop/users/connect-windows?tabs=subscribe)
- **AVD Web Client:** https://client.wvd.microsoft.com/arm/webclient/

### Shadow a User's AVD Session (Remote Control)
1. Have the user run: `QWINSTA`
2. Get the Session ID from the results.
3. On your machine, run: `MSTSC /v:IP_ADDRESS /Shadow:Session_ID /Control`
4. The user will be prompted to allow the session.
> You can also get the session ID from the AVD Admin Portal: Users → [user] → Sessions tab.

### Disable Cloud PC Boot Option
In Syncro, go to Backgrounding Tools, open Registry Editor, and change the following values from `1` to `0`, then reboot:
```
HKLM:\Software\Microsoft\PolicyManager\current\device\CloudDesktop  → BootToCloudMode
HKLM:\Software\Microsoft\PolicyManager\current\device\WindowsLogon  → OverrideShellProgram
```

---

## Azure Files

### Mapping Azure Files with Azure AD (No AAD DS)
Use **Microsoft Entra Kerberos** for hybrid identities to authenticate users against Azure AD:

1. **Enable Microsoft Entra Kerberos Authentication** on the storage account.
2. **Assign Share-Level Permissions** via Azure RBAC (roles: Storage File Data SMB Share Reader/Contributor/Elevated Contributor).
3. **Configure Directory and File-Level Permissions** using Windows ACLs.

> **Note:** Entra Kerberos supports hybrid user identities only — users must exist in both on-premises AD and Azure AD.

**When to use AAD DS vs. Entra Kerberos:**
- **Use AAD DS** if: cloud-only identities, need granular NTFS folder-level permissions.
- **Use Entra Kerberos** if: hybrid environment with on-prem AD, share-level permissions are sufficient.

### Azure Files Resources
- [Create a profile container with Azure Files and Azure AD](https://learn.microsoft.com/en-us/azure/virtual-desktop/create-profile-container-azure-ad)
- [Azure Files networking considerations](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-networking-overview)
- [Overview — Azure Files identity-based authentication](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-active-directory-overview)

---

## Azure / Microsoft 365 — Dynamic Groups

### Dynamic Group Based on Assigned License
```powershell
# Get license ObjectIDs
Get-AzureADSubscribedSKU

# Get service plans for a specific license
Get-AzureADSubscribedSku -objectid <ObjectID> | Select-Object -ExpandProperty ServicePlans
```

**Example dynamic group query:**
```
(user.assignedPlans -any (assignedPlan.servicePlanId -eq "21b439ba-a0ca-424f-a6cc-52f954a5b111" -and assignedPlan.capabilityStatus -eq "Enabled"))
```

---

## Enterprise State Roaming (Microsoft Entra ID)

Synchronizes across devices:
- Windows Settings (language, ease of access, system preferences)
- Application Settings and App Data (UWP apps)
- Internet Explorer and Edge Settings (favorites, bookmarks, reading lists)
- Passwords (credentials for apps and websites)

> **Note:** User files and documents are NOT synchronized through Enterprise State Roaming. Use OneDrive for file sync.

---

## Exchange / Exchange Online

| Topic | Resource |
|-------|---------|
| Exchange Online Service Description | [Microsoft Learn](https://learn.microsoft.com/en-us/office365/servicedescriptions/exchange-online-service-description/exchange-online-service-description) |
| Exchange Online Archiving | [Microsoft Learn](https://learn.microsoft.com/en-us/office365/servicedescriptions/exchange-online-archiving-service-description/exchange-online-archiving-service-description) |
| Exchange Online Protection (EOP) | [Microsoft Learn](https://learn.microsoft.com/en-us/office365/servicedescriptions/exchange-online-protection-service-description/exchange-online-protection-service-description) |
| Exchange Server Troubleshooting | [Microsoft Learn](https://learn.microsoft.com/en-us/exchange/troubleshoot/exchange-server-welcome) |

### Exchange Online Protection (EOP) Limits
- Accepted domains per tenant: **5,000**
- Remote domains per tenant: **200**
- Max message size (including attachments): **150 MB**
- IP Allow/Block list entries: **1,273**
- Spam quarantine retention: **30 days** (default, configurable)
- Message deferral retry: **every 15 minutes** for up to **1 day**

### Useful Exchange Commands
```powershell
# Enable litigation hold for all mailboxes (2555 days = 7 years)
Get-Mailbox -ResultSize Unlimited -Filter "RecipientTypeDetails -eq 'UserMailbox'" |
    Set-Mailbox -LitigationHoldEnabled $true -LitigationHoldDuration 2555

# Set Out of Office for a shared mailbox
Set-MailboxAutoReplyConfiguration -Identity "sharedmailbox@yourdomain.com" `
    -AutoReplyState Enabled `
    -InternalMessage "We are out of office..." `
    -ExternalMessage "We are out of office..."
```

### Exchange Online — Useful URLs
- Access another user's settings as admin: `https://outlook.office.com/ecp/USERID@XXX.com/`
- Change M365 password: https://account.activedirectory.windowsazure.com/ChangePassword.aspx
- See a user's Exchange Online rules as admin: `https://outlook.office365.com/ecp/emailaddress@SomeCompany.com`
- [Add a shared mailbox to Outlook Webmail](https://support.microsoft.com/en-us/office/open-and-use-a-shared-mailbox-in-outlook-on-the-web-98b5a90d-4e38-402b-9f38-4fcf236aaa34)

---

## Microsoft Defender

### Collect Microsoft Defender Antivirus Diagnostic Data
```
mpcmdrun.exe -GetFiles
```
Generates a `.cab` file at:
`C:\ProgramData\Microsoft\Microsoft Defender\Support\MpSupportFiles.cab`

To redirect:
```
mpcmdrun.exe -GetFiles -SupportLogLocation <path>
```

### Defender Resources
- [Troubleshoot Defender performance issues](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/troubleshoot-performance-issues)
- [Performance analyzer for Microsoft Defender Antivirus](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/tune-performance-defender-antivirus)
- [Data Loss Prevention (DLP)](https://learn.microsoft.com/en-us/microsoft-365/compliance/dlp-learn-about-dlp)

### Deploy Application Guard for Office
```powershell
Enable-WindowsOptionalFeature -online -FeatureName Windows-Defender-ApplicationGuard
```

---

## File Explorer — Windows 11 Classic Context Menu

Revert Windows 11 to the classic (Windows 10-style) right-click context menu:
```
reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /ve /d "" /f
Stop-Process -Name explorer -Force
Start-Process explorer
```

---

## Global Secure Access (Microsoft)

- [Get started with Global Secure Access](https://learn.microsoft.com/en-us/entra/global-secure-access/how-to-get-started-with-global-secure-access)
- [What is Global Secure Access](https://learn.microsoft.com/en-us/entra/global-secure-access/overview-what-is-global-secure-access)

**Issue — Users Cannot Connect to Office:**
If the M365 tab is red in the Channels tab but everything in the Services tab is green: right-click the **Global Secure Access client** in the system tray → click **"Switch User"** → log in with the user's account. Reboots alone have not been shown to fix this issue.

---

## General References

- [Windows Version Charts — Wikipedia](https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_versions)
- [Anti-spam message headers in Microsoft 365](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/message-headers-eop-mdo)
- [LAPS Overview — Video](https://www.youtube.com/results?search_query=Windows+LAPS+9+mins)
- [FSLogix AV Exclusions Prerequisites](https://learn.microsoft.com/en-us/fslogix/overview-prerequisites)
- [Virtual Machines — Start/Stop on Schedule](https://learn.microsoft.com/en-us/azure/azure-functions/start-stop-vms/overview)
