---
title: "SOP: Build a PC Using Intune (Windows Autopilot)"
category: "SOPs"
source: "SOP - Steps to build a PC using Intune.docx"
---

# SOP: Build a PC Using Intune (Windows Autopilot)

This SOP describes the steps required to provision a new or existing PC using Microsoft Intune and Windows Autopilot.

## Prerequisites

- Access to Intune/Azure portal with Global Admin or Intune Service Admin credentials.
- The device must have Internet connectivity.
- The device's hardware ID must be registered in Intune (via CSV upload or OEM injection).

---

## Part 1: Register the Hardware ID

1. **Order the laptop/desktop** through a VAR or direct from the OEM. Ensure the VAR/OEM either:
   - Provides the hardware ID in a CSV file for upload into Intune, **or**
   - Injects the hardware ID into Intune directly.

2. If the hardware ID is **not already in Intune** and is not provided by the VAR/OEM, run the PowerShell scripts on the Windows machine using one of the two methods below.

### Option A: Device is in OOBE (Out-of-Box Experience)

1. Turn on the PC and wait for the OOBE to start. **Do not proceed through any OOBE steps.**
2. Ensure a network cable is plugged in and the PC has Internet connectivity.
   - If only Wi-Fi is available, proceed through OOBE only until the Wi-Fi setup step. Once connected, do not proceed further.
3. Press and hold **Shift** + **F10** to open a CMD prompt.
4. At the CMD prompt, type `PowerShell` and press Enter.
5. Execute: `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`
6. Execute: `Install-Script -Name Upload-WindowsAutopilotDeviceInfo`
7. Answer **Yes** or **Y** to all prompts that follow.
8. Execute: `Upload-WindowsAutopilotDeviceInfo.ps1 -TenantName "%tenantname%.onmicrosoft.com"` (replace `%tenantname%` with your tenant name).
9. Authenticate when prompted — you must be a **Global Admin** or **Intune Service Admin**.
10. When the script completes successfully, **power off the PC**.

### Option B: Device is Already in Use but Not in Intune

1. Log in to the Windows machine using an **admin account**.
2. Ensure the PC has Internet connectivity.
3. Launch a **PowerShell window as an administrator**.
4. Execute: `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`
5. Execute: `Install-Script -Name Upload-WindowsAutopilotDeviceInfo`
6. Answer **Yes** or **Y** to all prompts.
7. Execute: `Upload-WindowsAutopilotDeviceInfo.ps1 -TenantName "%tenantname%.onmicrosoft.com"` (replace `%tenantname%` with your tenant name).
8. Authenticate when prompted — you must be a **Global Admin** or **Intune Service Admin**.
9. When the script completes successfully, **power off the PC**.

---

## Part 2: Add the Device to Intune Groups

1. Log in to the **Intune portal** at [https://portal.azure.com](https://portal.azure.com).
2. Add the device to the appropriate **device security group** (e.g., `SG-IAP-City`).

---

## Part 3: Assign the Device to a User (Optional)

If you know who the PC will be assigned to, assign the intended user:

1. Go to the Intune **"Windows Autopilot Devices"** blade.
2. Click the checkbox next to the machine you need to assign.
3. Click **"Assign user"** from the menu.
4. Type the user's name into the **"Select"** box, click their name to select them, then click the **"Select"** button.
5. **IMPORTANT:** Ensure the **"Profile status"** on this screen shows **"Assigned"**. The machine cannot be used until this status is reached. If you encounter issues, contact Imperitiv Support.

> **Note:** If you do not know who the PC will be assigned to, you can stop here and place the PC in storage. Be sure to **label the machine** indicating its current state.

---

## Part 4: Issue the PC to the User

1. Hand the computer to the user.
2. Have them plug in power, attach a network cable (or use Wi-Fi), and turn on the PC.
3. When the OOBE screen appears with an Ethernet connection, the user should be able to flow through the steps and be prompted by name to log in to the Azure AD instance.
4. If using Wi-Fi, the user will need to configure their Wi-Fi connection during OOBE before being prompted to log in with their company account.
