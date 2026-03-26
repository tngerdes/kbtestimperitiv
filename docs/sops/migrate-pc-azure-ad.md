---
title: "SOP: Migrate a PC from On-Premises AD to Azure AD"
category: "SOPs"
source: "SOP - Steps to Migrate PC to Azure AD.docx"
---

# SOP: Migrate a PC from On-Premises AD to Azure AD

This SOP outlines the step-by-step process for migrating a Windows PC from an on-premises Active Directory (AD) domain to Azure Active Directory (Azure AD).

---

## Stage I — Register Device with Autopilot

1. Open **PowerShell as an administrator** and run the following commands:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   Install-Script Upload-WindowsAutopilotDeviceInfo
   Upload-WindowsAutopilotDeviceInfo -TenantName XXXXX.OnMicrosoft.com
   ```

2. Authenticate with the **Imperitiv admin account** for the client tenant.
3. Ensure **successful execution** of the command.
4. **Reboot** the computer.
5. On reboot, enter the **BIOS** and verify:
   - **Secure Boot** is enabled.
   - The computer has a **TPM** and it is enabled.

---

## Stage II — Prepare the User's Data

Have the user who is being migrated log in, then complete the following:

1. Ensure **Desktop**, **Documents**, and **Pictures** folders are being synced with OneDrive (for the target tenant) and that the **Backup** option is enabled and successful for all three folders.
2. Have the user review their **Downloads** folder for anything they need to retain.
3. Verify **browser bookmarks** (Chrome/Firefox/Edge, etc.) are exported and saved to OneDrive and synchronized.
4. **PST files:**
   - Check Outlook for any attached PST files.
   - Verify the size and that the data will fit in Office 365.
   - Migrate any PST data to the user's O365 mailbox.
   - If the PST data is too large to migrate within the allotted time:
     1. Record the path to the PST.
     2. Detach the PST from the Outlook profile.
     3. Close Outlook.
     4. Move the PST to a location such as `C:\Outlook Files\`.
     5. After migration, move the PST back to the user's profile (`C:\Users\%USERNAME%`) and reattach it to Outlook.
     6. Schedule a time to help the user migrate the PST data to the Exchange Online mailbox.
5. Open **Device Manager** and verify there are no issues with drivers.

---

## Stage III — Disjoin from On-Premises AD and Join Azure AD

1. Create a **local computer user** in **Computer Management** (e.g., `imp-admin`) and make it a member of the local **Administrators** group.
2. Log out the current user and log in as `imp-admin`.
3. **Disjoin** the computer from the on-premises AD domain and **reboot** the computer.
4. Log in as `imp-admin` after the reboot.
5. **Join the PC to Azure Active Directory** using the user's account:
   1. Go to **Settings** > **Accounts** > **Access Work or School**.
   2. Click **Connect**.
   3. Click **Join this device to Azure Active Directory**.
   4. Have the user enter their **email address** (user ID) for the new tenant.
   5. Have them enter their **password**.
   6. Click **Next**.
   7. Click **Join**.
   8. Click **Done** and then **reboot** the computer.

---

## Stage IV — First Login as Azure AD Account

1. Log in to the machine using the user's Azure AD account: `%USERNAME%@DomainName.com`.
2. The **"Setting up your device"** window will appear. This may take several minutes.
   - If the first two sections do not show **"Completed"**, the machine did not receive the Disable ESP Configuration policy. Force reboots and policy updates until it does.
3. If **Windows Hello** is enabled, the user will be prompted to set a PIN and may be able to configure biometrics (fingerprint or facial recognition if supported).

---

## Stage V — Testing and Cleanup

### Testing

After the user logs in:

1. Let the machine sit for **5–6 minutes** and wait for OneDrive to set up and begin syncing automatically.
2. Verify **OneDrive** sets up and syncs.
3. Select **"Always keep on this device"** for OneDrive files.
4. Set up and verify **email access**.
5. **Import bookmarks**.
6. Move any Downloads data retained from Stage II.

### Cleanup

1. Ensure the user is **not** a local administrator.
2. Delete `C:\Temp` (if created) or any other temp folders.
3. **Rename** the Azure AD computer object and force a reboot (naming convention: `CompanyCode-SerialNumber`).
4. Have the user log back in and verify SharePoint Document Libraries are mapped.
5. Open **Computer Management** as an Admin and **disable** the user's local account so they cannot accidentally switch back to it. Do **not** delete it.

---

## Notes / Troubleshooting

### Error 8018000a

**Message:** "Something went wrong. The device is already enrolled. You can contact your system administrator with the error code 8018000a."

**Resolution:**

1. Use Process Monitor to identify traces from an "ongoing Azure AD join."
2. Delete all GUIDs under:
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Enrollments
   ```
3. **Do NOT delete** the following keys: `Context`, `Ownership`, `Status`, and `ValidNodePaths`.

**Reference:** [https://jocha.se/blog/tech/azure-ad-mdm-intune-error-8018000a](https://jocha.se/blog/tech/azure-ad-mdm-intune-error-8018000a)
