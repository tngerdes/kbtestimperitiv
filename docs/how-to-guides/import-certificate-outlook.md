---
title: "How to Import a Digital Certificate into Outlook"
category: "How-To Guides"
source: "Import Digitial Certificate to Outlook.docx"
---

# How to Import a Digital Certificate into Outlook

This guide explains how to import a digital certificate (`.p12` or `.pfx` file) into Microsoft Outlook for use with email security.

## Prerequisites

- You must have a `.p12` or `.pfx` digital certificate file (e.g., downloaded from DigiCert) and the password you set for it.
- Microsoft Outlook must be installed.

## Steps

### Step 1: Open Outlook Trust Center

1. In Outlook, click **File** > **Options**.
2. In the Outlook Options dialog box, click **Trust Center** in the left bar.
3. Click the **Trust Center Settings** button.

### Step 2: Navigate to E-mail Security

In the Trust Center dialog box, click **E-mail Security** in the left bar.

### Step 3: Open Import/Export

Go to the **Digital IDs (Certificates)** section and click the **Import / Export** button.

### Step 4: Import the Certificate

In the Import / Export Digital ID dialog box:

1. Select **Import existing Digital ID from a file**.
2. Click the **Browse** button. In the Locate Security Profile dialog box, select your `.p12` file (the one downloaded from DigiCert), then click **Open**.
3. Enter the certificate password in the **Password** box.
4. Enter a descriptive name in the **Digital ID Name** box.
5. Click **OK**.

### Step 5: Set Security Level (if prompted)

If prompted, click **OK** to set the security level.

### Step 6: Configure the Certificate for Use in Outlook

1. Click **Settings** to configure Outlook to use the certificate.
2. Enter a name in the **Security Settings Name** field (choose something meaningful to you).
3. Click the top **Choose** button, then click **OK** on the Select a Certificate window. The newly imported certificate should be the only option. If multiple certificates appear, select the DigiCert certificate dated the day you downloaded it.
4. Change the **Hash Algorithm** to **SHA256**.
5. Click **OK** to complete the configuration.
6. Click **OK** again to close the Trust Center.
