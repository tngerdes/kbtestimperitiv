---
title: "Ubiquiti Networks Deployment Process"
category: "Reference"
source: "Ubiquiti SDN.pdf"
---

# Ubiquiti Networks Deployment Process

A reference guide for deploying and configuring Ubiquiti UniFi network infrastructure.

---

## Naming Conventions

Use proper templated names for primary externally-visible devices such as UDMs, UDM Pros, UDRs, Dream Walls, USGs, or Enterprise Gateways:

```
[COMPANY CODE]-[DEVICE CODE]-[TWO LETTER STATE ABBREVIATION]-[CITY ABBREVIATION]
```

### Valid Device Codes

| Code | Device |
|------|--------|
| `UDM` | Dream Machine |
| `UDMPRO` | Dream Machine Pro |
| `UDMSE` | Dream Machine SE |
| `UDR` | Dream Router |
| `UDRULTRA` | Dream Router Ultra |
| `UDW` | Dream Wall |
| `UCK` | Cloud Key |
| `UCKG2` | Cloud Key G2 Plus |
| `USG` | Secure Gateway |
| `USGPRO` | Secure Gateway Professional |
| `UCKENT` | Cloud Key Enterprise |
| `UCC` | UniFi Cloud Console |

> **Note:** For cloud consoles, use `[UCC]-[COMPANYNAME]` — these consoles don't have a specific location.

Remaining UniFi devices should be named appropriately for their location or function. The primary root switch should be named **"Core Switch"**. APs should be labeled with a specific location (e.g., "Conference Room AP" or "Room 3118 AP").

---

## Routers / Gateways / Firewalls

### Wi-Fi Networks
- Set up the primary Wi-Fi network using **WPA3 only** and all bands.
- Set up the guest network (if requested) using **WPA2 or WPA2/3 hybrid mode**, all APs, and the 2.4/5GHz bands only. The 6 GHz performance band should be limited to the primary network only.

### Guest Network Configuration
1. Create a speed limit profile under **Settings → Profiles → Wi-Fi speed limit**. Generally, limit to **25%** each of the maximum upload and download bandwidth of the incoming WAN connection.
2. Configure a captive portal (if requested): enable HTTPS, enforce branding, enforce terms of service, and redirect the success landing page to the client's main webpage.
3. Enforce speed limits: **Settings → WiFi → [guest network name] → WiFi Speed Limit**.
4. Under **Settings → WiFi → [guest network name] → WiFi Scheduler**, create a time schedule to turn off guest WiFi outside of business hours. Typical guideline: shut off from **20:00 to 04:00 daily**.

### Security Settings
- **Settings → Teleport and VPN → Teleport tab:** Turn off Teleport VPN. (The Teleport app only works on iOS, Android, and macOS.)
- **Settings → Application Firewall:**
  - Set Suspicious Activity toggle to **Notify and Block**.
  - Set Detection Sensitivity to **High**.
  - Enable **Dark Web Blocker** and **Malicious Website Blocker**.
- **Settings → Application Firewall → Gateway Engine:**
  - Turn on **Device and Traffic Identification**.
  - Turn on **Country Restrictions** and block all countries in the Country Block List (see below).

### Updates and Backups
- **Settings → System → Updates:** Uncheck "Automate Device Updates." Add a new device update schedule repeating **monthly at 00:00 hours**. Select all applicable devices.
- **Settings → System → Backups:** Click "Console Cloud Backups" and ensure **System Config Backup** is checked with a recent backup.

### Advanced
- **Settings → System → Advanced:** Ensure **WiFiman Support** is checked.
- Under **Wireless Connectivity:** Disable **Wireless Meshing** if all APs are hardwired to the controller. Leave enabled if using wireless UniFi devices (e.g., wireless G4 Doorbell, UniFi Protect wireless cameras).

### Country Block List
Block the following countries in the Application Firewall:

Afghanistan, Azerbaijan, Bangladesh, Bahrain, Brazil, Belarus, Congo (DRC), Central African Republic, Congo (ROC), China, Colombia, Cuba, Egypt, Hong Kong, Honduras, Indonesia, India, Iraq, Iran, Jordan, Kyrgyzstan, North Korea, Kazakhstan, Sri Lanka, Libya, Myanmar, Mongolia, Nigeria, Oman, Philippines, Pakistan, Qatar, Russia, Rwanda, Saudi Arabia, Sudan, Somalia, South Africa, Syria, Thailand, Turkmenistan, Turkey, United Arab Emirates, Uzbekistan, Venezuela, Vietnam, Yemen, Zimbabwe

---

## Switches

- Set switch priorities in **UniFi Devices → [Switch] → Settings tab**.
- Proper switching architecture: the main router should **only** be connected to the core switch. Do not connect any devices directly to the UDM/UDR/UDW except the primary core switch.
- Set the **core switch RSTP priority** to **4096** in Switch Settings → Advanced. This should be the ONLY switch assigned this priority at each site.
  - Secondary switches (directly connected to core): **8192**
  - Tertiary switches (connected to secondary): **12288**
  - And so on.

> **Note:** For setups where a higher-level switch may be added later (e.g., aggregation/leaf switch), start the core switch at **8192** and reserve **4096** for the future aggregator.

- Any core switch connected directly to a UniFi Gateway should use the **highest-bandwidth port available**. For advanced switches, use a 1G or 10G SFP-to-SFP "direct attach cable" to offload routing to the SFP backplane.
  - Example cable: https://www.amazon.com/dp/B09P517YVM/

---

## Access Points

1. Update all APs to the latest firmware version.
2. If all APs are hardwired, disable meshing in **device settings** for each AP (this is per-AP, not in Global AP Settings).
3. Under **Settings → Global AP Settings**, set transmit power to **Medium** for all radios (adjustable after installation).
4. Channel width settings:
   - **2.4 GHz band:** Always set to the **lowest channel width** to maximize coverage on the fallback radio.
   - **5.0 GHz band:** Set to **40 MHz**. If uninterrupted line of sight to >80% of client devices, can be set to 80 MHz.
   - **6 GHz band:** Set to **160 MHz** to maximize bandwidth for supported clients within range.

---

## WAN Failover Devices (U-LTE)

1. Raise the data limit to **5GB/10GB** in device settings (default shuts off after 3GB).
2. Enable external antenna support in device settings **only after connecting the external RP-SMA antenna**.
3. Connect the U-LTE to the **backup WAN port**. If the primary WAN port uses RJ45, you'll need a 1G or 10G/1G auto-negotiating SFP+ to RJ45 module:
   - [UACC-CM-RJ45-1G from Ubiquiti Store](https://store.ui.com/us/en/pro/category/accessories-cables-dacs/products/rj45-to-sfp-module?variant=UACC-CM-RJ45-1G)
