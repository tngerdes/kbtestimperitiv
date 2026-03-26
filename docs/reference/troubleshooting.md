---
title: "Troubleshooting Reference"
category: "Reference"
source: "TroubleShooting.pdf"
---

# Troubleshooting Reference

A collected reference of common IT troubleshooting topics, commands, and resolutions.

---

## OneDrive

### Fix OneDrive for Business Sync Problems
- [Fix OneDrive sync problems (microsoft.com)](https://support.microsoft.com/en-us/office/fix-onedrive-sync-problems-0899b115-05f7-45ec-945b-f6edbe7a6b67)

### OneDrive Stuck on "Processing Changes" / Office Apps Crashing
If OneDrive is stuck on "Processing changes" without showing files being actively synced, or Office apps crash/freeze when opening documents:

1. Check if **Microsoft Office Click-to-Run** is taking up more than ~50MB of RAM in Task Manager. If so, proceed.
2. Open **Services.msc**, find the **Microsoft Click-to-Run Service**, and set startup behavior to **Disabled**.
3. Reboot.
4. Open `C:\Users\Username\AppData\Local\Microsoft\Office\Spw` and delete all files and folders within.
5. Open `C:\Users\Username\AppData\Local\Microsoft\Office\15.0\OfficeFileCache` and delete all files and folders within.
6. Re-open **Services.msc**, find **Microsoft Click-to-Run Service**, and set startup back to **Automatic**.
7. Reboot.
8. Verify that OneDrive now shows "Your files are synced" or shows a list of actively syncing files.
9. Verify in Task Manager that **Microsoft Office Click-to-Run** is taking no more than ~50MB of RAM.

> **Note:** Check the OneDrive DAT file — it has a 2 GB size limit in older versions. Performance degrades when the file exceeds ~200 MB.
> Path: `%localappdata%\Microsoft\OneDrive\settings\Business1`

---

## Microsoft Support and Recovery Assistant
- [About Microsoft Support and Recovery Assistant](https://support.microsoft.com/en-us/office/about-the-microsoft-support-and-recovery-assistant-e90bb691-c2a7-4697-a94f-88836856c72f)
- **Download:** https://aka.ms/SaRA-FirstScreen

---

## Microsoft Teams

### Request Teams Chat Export
- [Export my data (live.com / Microsoft)](https://account.live.com/exportdata)

### Teams Meeting Add-in Missing from Outlook
- [Troubleshoot the Teams Meeting add-in in Outlook for Windows — Microsoft Support](https://support.microsoft.com/en-us/office/troubleshoot-the-teams-meeting-add-in-in-outlook-for-windows-8a853a5b-3e41-493a-a665-9c9f9088e91b)

### Teams Voice Calling Diagnostics
- [Teams PSTN Diagnostic Tool](https://aka.ms/TeamsPSTNDiag) — Used to diagnose domestic and international calling issues. Must login as a Teams or Global Admin.

---

## Office

### Office Software Not Updating
- [Office not updating automatically — Microsoft Q&A](https://learn.microsoft.com/en-us/answers/questions/902651/office-not-updating-automatically)

**Silent update (no GUI):**
```
C:\Program Files\Common Files\microsoft shared\ClickToRun\OfficeC2RClient.exe /update user updatepromptuser=false forceappshutdown=true displaylevel=false
```

**GUI update:**
```
C:\Program Files\Common Files\microsoft shared\ClickToRun\OfficeC2RClient.exe /update user updatepromptuser=false forceappshutdown=false displaylevel=true
```

### Add a User's Mailbox to Outlook (as Delegate)
1. In Outlook, click **File > Account Settings > Account Settings**.
2. On the **Email** tab, click the Exchange account, then click **Change**, then **More Settings**.
3. On the **Advanced** tab, under **Open these additional mailboxes**, click **Add** and enter the mailbox name of the person whose mailbox you want to add.

### Where to Install Office, Project, and Visio
- [My account — Office](https://account.microsoft.com/account)

---

## Intune / MEM

### Machine Not in MEM — Add a Windows Computer to MEM Manually
Check for the registry key `HKLM:\SOFTWARE\Policies\Microsoft\Windows\CurrentVersion\MDM` with DWORD values:
- `AutoEnrollMDM` = 1
- `UseAADCredentialType` = 1

**Create via PowerShell:**
```powershell
$registryPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\CurrentVersion\MDM"
New-Item -Path $registryPath
$Name = "AutoEnrollMDM"
$Name2 = "UseAADCredentialType"
$value = "1"
New-ItemProperty -Path $registryPath -Name $Name -Value $value -PropertyType DWORD -Force | Out-Null
New-ItemProperty -Path $registryPath -Name $Name2 -Value $value -PropertyType DWORD -Force | Out-Null
gpupdate /force
```

After creation, run (using PsExec):
```
C:\Windows\system32\deviceenroller.exe /c /AutoEnrollMDM
```

### Intune Resultant Set of Policies
On the Azure AD domain-joined Windows 10 device:
1. Navigate to **Access work or school**.
2. Click on the connected account, then click **Info**.
3. Scroll to the bottom and click **Create report**.
4. Report is saved to: `C:\Users\Public\Documents\MDMDiagnostics\MDMDiagReport.html`

### Re-Running Failed Intune PowerShell Scripts
Registry location for Intune PowerShell scripts:
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\IntuneManagementExtension\Policies\faa7491a-8a07-4a3d-8637-9571d5dc192c
```
Delete any sub-keys to allow a script to run again.

Script files location:
```
C:\Program Files (x86)\Microsoft Intune Management Extension\Policies\Scripts
```

### Intune Enrollment Errors

#### Error 8018000a — Device Already Enrolled
Delete all GUIDs under:
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Enrollments
```
**Do NOT** delete: `Context`, `Ownership`, `Status`, `ValidNodePaths`.

#### MAM Instead of MDM
```powershell
$EnrollmentsPath = "HKLM:\SOFTWARE\Microsoft\Enrollments\"
$Enrollments = Get-ChildItem -Path $EnrollmentsPath
$DiscoveryServerFullUrls = @("https://wip.mam.manage.microsoft.com/Enroll")
Foreach ($Enrollment in $Enrollments) {
    $EnrollmentObject = Get-ItemProperty Registry::$Enrollment
    if ($EnrollmentObject."DiscoveryServiceFullURL" -in $DiscoveryServerFullUrls) {
        $EnrollmentPath = $EnrollmentsPath + $EnrollmentObject."PSChildName"
        Remove-Item -Path $EnrollmentPath -Recurse
        & "C:\Windows\System32\deviceenroller.exe /c /AutoEnrollMDM"
    }
}
```

#### Error 80190026 — Previously Enrolled in SCCM
Open: `SOFTWARE\Microsoft\Enrollments`
Ensure the `ExternallyManaged` key doesn't exist or is set to **0**, not 1.

#### Error 0x80180026
Check the `Devicemanagement-Enterprise-Diagnostics-Provider` event log. Ensure **Disable MDM Enrollment** policy is not preventing enrollment.

#### Error 0x8018002a / 0x80070003
Likely caused by Conditional Access rules requiring MFA for "All Cloud Apps". Exclude the **Microsoft Intune Enrollment** Cloud app from the Conditional Access rule.

#### Error 0x8018002b
Possible causes:
- **UPN Issues** — Ensure the user's UPN matches your routable domain.
- **MDM Scope** — Configure MDM scope to "All" or include the user in the scoped group.
- **User License** — Verify the user is licensed for Intune.
- **Patience** — Enrollment can take time; wait before retrying.
- **Previously AADR Enrolled** — Remove lingering enrollments via PowerShell:

```powershell
$RegistryKeys = "HKLM:\SOFTWARE\Microsoft\Enrollments",
                "HKLM:\SOFTWARE\Microsoft\Enrollments\Status",
                "HKLM:\SOFTWARE\Microsoft\EnterpriseResourceManager\Tracked",
                "HKLM:\SOFTWARE\Microsoft\PolicyManager\AdmxInstalled",
                "HKLM:\SOFTWARE\Microsoft\PolicyManager\Providers",
                "HKLM:\SOFTWARE\Microsoft\Provisioning\OMADM\Accounts",
                "HKLM:\SOFTWARE\Microsoft\Provisioning\OMADM\Logger",
                "HKLM:\SOFTWARE\Microsoft\Provisioning\OMADM\Sessions"

$IntuneCert = Get-ChildItem -Path Cert:\LocalMachine\My |
    Where-Object { $_.Issuer -match "Intune MDM" } | Remove-Item

$EnrollmentID = Get-ScheduledTask |
    Where-Object { $_.TaskPath -like "*Microsoft*Windows*EnterpriseMgmt*" } |
    Select-Object -ExpandProperty TaskPath -Unique |
    Where-Object { $_ -like "*-*-*" } | Split-Path -Leaf

Get-ScheduledTask |
    Where-Object { $_.Taskpath -match $EnrollmentID } |
    Unregister-ScheduledTask -Confirm:$false

foreach ($Key in $RegistryKeys) {
    if (Test-Path -Path $Key) {
        Get-ChildItem -Path $Key |
            Where-Object { $_.Name -match $EnrollmentID } |
            Remove-Item -Recurse -Force -Confirm:$false -ErrorAction SilentlyContinue
    }
}

Start-Sleep -Seconds 30
$EnrollmentProcess = Start-Process -FilePath "C:\Windows\System32\DeviceEnroller.exe" `
    -ArgumentList "/C /AutoenrollMDM" -NoNewWindow -Wait -PassThru
```

#### Error 0x80180001
Configure the credential type to **User Credential** (not Device Credential). Device Credential is only supported with Co-management or Azure Virtual Desktop.

#### Schedule to Enroll in MDM from AAD Not Created
Manually create the scheduled task:

```powershell
$TaskScheduleName = "Schedule created by enrollment client for automatically enrolling in MDM from AAD"
$Date = Get-Date -Format "yyyy-MM-dd"
$Time = (Get-Date).AddMinutes(5).ToString("HH:mm:ss")
$task = @"
<?xml version="1.0" encoding="UTF-16"?>
<Task version="1.3" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
  <RegistrationInfo>
    <Author>Microsoft Corporation</Author>
    <URI>\Microsoft\Windows\EnterpriseMgmt\Schedule created by enrollment client for automatically enrolling in MDM from AAD</URI>
  </RegistrationInfo>
  <Triggers>
    <TimeTrigger>
      <Repetition>
        <Interval>PT5M</Interval>
        <Duration>P1D</Duration>
        <StopAtDurationEnd>true</StopAtDurationEnd>
      </Repetition>
      <StartBoundary>$($Date)T$($Time)</StartBoundary>
      <Enabled>true</Enabled>
    </TimeTrigger>
  </Triggers>
  <Principals>
    <Principal id="Author">
      <UserId>S-1-5-18</UserId>
      <RunLevel>LeastPrivilege</RunLevel>
    </Principal>
  </Principals>
  <Settings>
    <MultipleInstancesPolicy>Queue</MultipleInstancesPolicy>
    <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
    <StopIfGoingOnBatteries>false</StopIfGoingOnBatteries>
    <AllowHardTerminate>true</AllowHardTerminate>
    <StartWhenAvailable>true</StartWhenAvailable>
    <RunOnlyIfNetworkAvailable>true</RunOnlyIfNetworkAvailable>
    <AllowStartOnDemand>true</AllowStartOnDemand>
    <Enabled>true</Enabled>
    <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
    <Priority>7</Priority>
  </Settings>
  <Actions Context="Author">
    <Exec>
      <Command>%windir%\system32\deviceenroller.exe</Command>
      <Arguments>/c /AutoEnrollMDM</Arguments>
    </Exec>
  </Actions>
</Task>
"@
(Register-ScheduledTask -XML $task -TaskName $TaskScheduleName -Force) | Out-Null
```

### Enroll a Personal iPhone in Intune
- [Set up personal iOS device in Intune Company Portal app — Microsoft Learn](https://learn.microsoft.com/en-us/mem/intune/user-help/enroll-your-device-in-intune-ios)

---

## General Troubleshooting / Repair / Health Check

### DISM Commands (Elevated CMD or PowerShell)
```
DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth
DISM /Image:C:\offline /Cleanup-Image /RestoreHealth /Source:c:\test\mount\windows
```

### Additional Repair Commands
```
SFC /SCANNOW
CHKDSK C: /F
```

### General Computer and Performance Fix Steps
1. Run DISM commands above.
2. Run `SFC /SCANNOW`.
3. Run `CHKDSK C: /F`.
4. Update all drivers.
5. Run .NET Repair Tool: https://download.microsoft.com/download/2/B/D/2BDE5459-2225-48B8-830C-AE19CAF038F1/NetFxRepairTool.exe
6. Launch **MSCONFIG** and disable all non-Microsoft services on the "Services" tab. Go to Task Manager and disable startup services on the "Startup" tab. Take a screenshot before disabling.

### Useful PowerShell Commands
```powershell
# Determine disk space used for a folder
GCI -Recurse | Measure-Object -Sum Length

# Get remaining free disk space (options)
(Get-PSDrive -Name C).Free
Get-CimInstance Win32_LogicalDisk -Filter "DeviceID='C:'" | Select-Object DeviceID,
  @{Name='UsedMB'; Expression={[math]::Round(($_.Size - $_.FreeSpace) / 1MB, 2)}},
  @{Name='FreeMB'; Expression={[math]::Round($_.FreeSpace / 1MB, 2)}},
  @{Name='SizeMB'; Expression={[math]::Round($_.Size / 1MB, 2)}}

# Determine if daylight savings time is on
(Get-Date).IsDaylightSavingTime()
```

### Windows 11 24H2 — Bypass Network Requirement During OOBE
```
Shift+F10 -> oobe\bypassnro
```

---

## Windows Store

### Reinstall the Windows Store
```powershell
Get-AppxPackage -allusers Microsoft.WindowsStore | Foreach {
    Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml"
}
```

### Reset the Windows Store
```
Wsreset.exe
```

---

## Windows Update

### Windows Update Log
```powershell
# Open PowerShell as Administrator
Get-WindowsUpdateLog -LogPath C:\Temp\WindowsUpdate.log
```

### Run the Windows Store Apps Troubleshooter
- Go to **Settings > Update & Security > Troubleshoot > Additional troubleshooters**.
- Find **Windows Store Apps**, then click **Run the troubleshooter**.

### Windows Event Viewer — Events Related to Failed Updates
```powershell
Get-WinEvent -LogName System | Where-Object { $_.Id -in 20, 31, 35, 8024, 80240022, 80240023 } |
    ForEach-Object { $_ | Select-Object TimeCreated, Id, @{Name="Message"; Expression={$_.Message}} } |
    Format-List
```

### Check the CBS Log (Component Based Servicing)
- Location: `C:\Windows\Logs\CBS\CBS.log`
- Search for keywords: "Failed," "Error," or "Warning"

### Check the DISM Log
- Location: `C:\Windows\Logs\DISM\dism.log`

### SetupDiag Tool
Download from Microsoft — analyzes Windows logs to provide detailed reasons for update failures.

---

## PIN Reset (Windows Hello for Business)

**References:**
- https://tech.nicolonsky.ch/unable-to-reset-windows-hello-for-business-pin/
- [Intune Device Windows PIN Reset — Microsoft Learn](https://learn.microsoft.com/en-us/mem/intune/remote-actions/device-windows-pin-reset)

Consent must be manually granted to the following enterprise applications:

- **Microsoft Pin Reset Service Production:**
  `https://login.windows.net/common/oauth2/authorize?response_type=code&client_id=b8456c59-1230-44c7-a4a2-99b085333e84&resource=https%3A%2F%2Fgraph.windows.net&redirect_uri=https%3A%2F%2Fcred.microsoft.com&prompt=admin_consent`

- **Microsoft Pin Reset Client Production** (optional in some cases):
  `https://login.windows.net/common/oauth2/authorize?response_type=code&client_id=9115dd05-fad5-4f9c-acc7-305d08b1b04e&resource=https%3A%2F%2Fcred.microsoft.com%2F&redirect_uri=ms-appx-web%3A%2F%2FMicrosoft.AAD.BrokerPlugin%2F9115dd05-fad5-4f9c-acc7-305d08b1b04e&prompt=admin_consent`

---

## Facebook Account Recovery
1. **Use the Recovery Tool:** Go to the Facebook Account Recovery page and enter your email, phone number, or full name. Follow on-screen instructions to reset your password.
2. **Report the Compromise:** Visit `facebook.com/hacked` and follow the prompts to secure your account.

---

## Google Email (SPF/DKIM Setup)
- [How to set up SPF in Google](https://support.google.com/a/answer/33786?hl=en)
- [How to set up DKIM in Google](https://support.google.com/a/answer/174124?hl=en)

---

## Cloud PC (Windows 365)

### Troubleshoot Cloud PC Boot
- [Troubleshoot Windows 365 Boot — Microsoft Learn](https://learn.microsoft.com/en-us/windows-365/enterprise/troubleshoot-windows-365-boot)

---

## Defender / Attack Surface Reduction
- [Attack surface reduction frequently asked questions (FAQ) — Microsoft Learn](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/attack-surface-reduction-faq)

---

## Wolf Security (HP)

Wolf Security is highly problematic to computers. Multiple apps are connected to it and must all be removed to properly delete it:

- HP Wolf Security app
- HP Assess and Report
- HP Wolf Security Console
- HP Wolf Security Updater

> **Note:** The HP Wolf Security Updater must have all other Wolf Security apps uninstalled before it can be removed.
