---
title: "SOP: Manage Firefox Settings in Microsoft Endpoint Manager (MEM)"
category: "SOPs"
source: "SOP - Steps to Manage Firefox Settings in MEM.docx"
---

# SOP: Manage Firefox Settings in Microsoft Endpoint Manager (MEM)

This SOP describes how to create Mozilla Firefox extension policies using Microsoft Endpoint Manager (Intune) and custom OMA-URI settings.

---

## Steps to Create a Firefox Extension Policy

1. Navigate to **Microsoft Endpoint Manager** > **Devices** > **Configuration Profiles** > **Create Profile**.

2. Set:
   - **Platform:** Windows 10 and later
   - **Profile type:** Templates > **Custom**

3. **Name** the profile appropriately — for example: `[Company Name] – Extension Policy – Firefox`

4. **Add an OMA-URI setting** by following the instructions at:
   [Managing Firefox with Microsoft Endpoint Manager (Intune) | Firefox for Enterprise Help](https://support.mozilla.org/en-US/kb/managing-firefox-with-microsoft-endpoint-manager)

5. The OMA-URI setting should look like the following:
   ```
   ./Device/Vendor/MSFT/Policy/ConfigOperations/ADMXInstall/Firefox/Policy/FirefoxAdmx
   ```

6. For **Data Type**, select **String** and paste the entire contents of the **Firefox ADMX file** into the value field.
   - Download the ADMX file from: [mozilla/policy-templates Releases on GitHub](https://github.com/mozilla/policy-templates/releases)
   - Open the downloaded ADMX file in Notepad and copy all of its text.

7. **Save** the profile.

---

## Adding Specific Firefox Policy Settings

To add specific Firefox settings after the ADMX has been ingested:

1. Go back into the extension policy profile.
2. Under **"Configuration Settings"**, click **Edit**.
3. Add additional settings from the policy template reference:
   [policy-templates README on GitHub](https://github.com/mozilla/policy-templates/blob/v3.9/README.md)
