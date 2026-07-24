# Windows USB Activity Analysis Using KAPE and Eric Zimmerman Tools

A host-based digital forensic investigation to reconstruct USB device activity on a Windows 11 virtual machine using KAPE, Eric Zimmerman tools, Registry Explorer, USB Detective, and Timeline Explorer. The examination focused on identifying the connected USB device, determining the user who interacted with it, reconstructing USB activity, and analysing user artifacts to establish file access and system interaction.

---

## Case Information

| Property | Value |
|----------|-------|
| Investigation Type | Windows USB Forensics |
| Evidence | E01 Disk Image |
| Image Name | NFTS_USB_FORENSICS.E01 |
| Acquisition Tool | FTK Imager v8.2.0.33 SP1 |
| Mount Tool | Arsenal Image Mounter v3.12.331 |
| Primary Tool | KAPE (gKAPE v1.3.0.2) |
| Secondary Tools | Registry Explorer, USB Detective, Timeline Explorer, LECmd, MFTECmd, RECmd, EvtxECmd |
| Operating System | Windows 11 Pro 25H2 |
| Analyst | Julian Derry |
| Status | Completed |

---

# Table of Contents

- [Objective](#objective)
- [Host Information](#host-information)
- [Evidence Acquisition](#evidence-acquisition)
- [Evidence Mounting](#evidence-mounting)
- [Evidence Collection](#evidence-collection)
- [Timeline Creation](#timeline-creation)
- [USB Device Identification](#usb-device-identification)
- [Assigned Drive Letter](#assigned-drive-letter)
- [Windows User Identification](#windows-user-identification)
- [Files Opened From the USB](#files-opened-from-the-usb)
- [Folders Browsed](#folders-browsed)
- [Applications Used](#applications-used)
- [File Copy Analysis](#file-copy-analysis)
- [USB Activity Timeline](#usb-activity-timeline)
- [Registry Integrity and Limitations](#registry-integrity-and-limitations)
- [Findings](#findings)
- [Tools Used](#tools-used)

---

# Objective

This investigation aimed to answer the following questions:

- What USB device was connected?
- When was the USB first installed, last connected and removed?
- What drive letter was assigned?
- Which Windows user interacted with the USB?
- What files were opened from the USB?
- What folders on the USB were browsed?
- Which applications opened files from the USB?
- Were files copied between the USB and the computer?
- What is the complete timeline of USB activity?
- Are there inconsistencies or limitations within the available evidence?

---

# Host Information

| Property | Value |
|----------|-------|
| Device Name | win_11_vm |
| Processor | 13th Gen Intel(R) Core(TM) i5-13420H (2.61 GHz) |
| RAM | 8 GB |
| Graphics | VMware SVGA 3D |
| Storage | 64 GB (37 GB Used) |
| Operating System | Windows 11 Pro |
| Version | 25H2 |
| OS Build | 26200.8875 |
| Installation Date | 25 June 2026 |

---

# Evidence Acquisition

The host computer was acquired as an E01 forensic image using FTK Imager. MD5 and SHA1 hash values were calculated during acquisition and verified to ensure the integrity of the evidence.

| Property | Value |
|----------|-------|
| Image Name | NFTS_USB_FORENSICS.E01 |
| Acquisition Tool | FTK Imager v8.2.0.33 SP1 |
| Acquisition Date | 20 July 2026 |
| MD5 | 17d2ea844e4f66b14e1d6ac60b4d1c91 |
| SHA1 | 059e70eb50e8d2082fa379bb9b093bd4234a0480 |
| Examiner | Julian Derry |

---

# Evidence Mounting

The forensic image was mounted using Arsenal Image Mounter v3.12.331.

The image was mounted as a physical disk using **Disk Device** with **Write Temporary** enabled so that all write operations were redirected to a separate differencing file rather than modifying the original evidence.

The mounted image appeared as:

```text
F:
```

---

# Evidence Collection

KAPE (gKAPE v1.3.0.2) was used to collect Windows forensic artifacts.

## Registry Targets

- NTUSER.DAT
- SYSTEM
- SOFTWARE
- SAM
- SECURITY
- USRCLASS.DAT

## Additional Targets

- USBDetective
- USBDevicesLogs
- $MFT
- $J
- $LogFile

## Parsing Modules

- EZParser
- MFTECmd
- RECmd
- EvtxECmd

---

# Timeline Creation

After collection, the following modules were executed to parse the acquired artifacts.

- EZParser
- MFTECmd
- RECmd
- EvtxECmd

The generated CSV files were analysed using Timeline Explorer to reconstruct USB activity.

---

# USB Device Identification

Registry Explorer was used to analyse:

```text
SYSTEM\CurrentControlSet\Enum\USBSTOR
```

Three USB devices were identified.

The device of interest was the most recently connected USB on:

**20 July 2026 13:23:17**

The USBSTOR entry contained a ParentIdPrefix rather than the device's actual serial number.

To verify the serial number, the following locations were examined:

- SYSTEM\CurrentControlSet\Enum\USB
- MountedDevices
- SetupAPI.dev.log
- USB Detective

All artifacts confirmed the USB serial number:

```text
20240418000000
```

---

# Assigned Drive Letter

MountedDevices did not contain a traditional drive letter assignment.

Instead, Windows recorded:

```text
\??\Volume
```

This indicates Windows recognised the storage device but did not preserve a drive letter assignment within MountedDevices.

Later, Windows Shortcut (LNK) artifacts referenced files located on drive **E:**, corroborating that the USB was mounted as drive E when the document was accessed.

Possible causes include:

- Low quality flash controller
- Interrupted USB enumeration
- Corrupted partition table
- AutoMount disabled

---

# Windows User Identification

User-specific artifacts were analysed from the extracted NTUSER.DAT hive.

Artifacts examined included:

- RecentDocs
- UserAssist
- Windows Shortcut (LNK) files

The activity originated from:

```text
C:\Users\jderr
```

**Windows User**

```text
jderr
```

---

# Files Opened From the USB

Windows Shortcut (LNK) artifacts were analysed using LECmd.

The examination confirmed the following document was opened directly from the USB.

```text
Leasafric Logistics Ghana Limited.pdf
```

The USN Journal further confirmed Windows updated the associated shortcut shortly after access, corroborating the LNK evidence.

---

# Folders Browsed

LNK and RecentDocs artifacts confirmed that the USB device itself was accessed.

However, no artifacts identified specific folders that were browsed.

Normally, this information is reconstructed using ShellBag artifacts. Since sufficient ShellBag evidence was unavailable, only access to the USB root can be confirmed.

---

# Applications Used

The available evidence confirms that the PDF document was opened.

However, the available artifacts do not identify which application opened the document.

Normally, this determination would be made using Jump List artifacts.

No relevant Jump List artifacts were identified.

---

# File Copy Analysis

The Master File Table ($MFT) and USN Journal ($J) were analysed to determine whether files were copied between the USB device and the host computer.

Although similarly named files existed on the local system, the available evidence does not establish whether they originated from the USB.

No conclusive evidence was identified demonstrating a file copy operation in either direction.

---

# USB Activity Timeline

| Date & Time | Event | Artifact |
|-------------|-------|----------|
| 2026-07-20 13:23:17 | USB connected | SYSTEM Registry |
| 2026-07-20 13:23:17 | Device installed | SYSTEM Registry |
| 2026-07-20 13:23:30 | USB removed | SYSTEM Registry |
| 2026-07-20 13:29:04 | PDF opened | Windows Shortcut (LNK) |
| 2026-07-20 13:29:05 | Shortcut updated | USN Journal |
| 2026-07-20 13:29:07 | RecentDocs updated | NTUSER.DAT |

---

# Registry Integrity and Limitations

During analysis, Registry Explorer reported that the registry hive was in a dirty state and that associated transaction logs (.LOG1 and .LOG2) were present but required replay.

This behaviour is expected when evidence is acquired from a live Windows system because pending registry transactions may not yet have been committed to the hive.

As a result, registry-derived timestamps including:

- Installed
- First Installed
- Last Connected
- Last Removed

may not represent the latest committed state.

These artifacts were interpreted with appropriate caution throughout the investigation.

Additional limitations include:

- No ShellBag artifacts confirming folder navigation.
- No Jump List artifacts identifying the application used to open the document.
- No conclusive evidence demonstrating a file copy operation.

A separate technical guide documenting registry transaction log replay and dirty hive recovery has already been produced and applies directly to this investigation.

---

# Findings

| Finding | Result |
|----------|--------|
| USB Device | Generic Mass-Storage USB Device |
| Manufacturer | MASS STORAGE USB DEVICE / Ven_Generic |
| Model | VID_349C&PID_0418 |
| Serial Number | 20240418000000 |
| Windows User | jderr |
| Mounted Volume | \??\Volume |
| Corroborated Drive Letter | E: |
| File Confirmed Opened | Leasafric Logistics Ghana Limited.pdf |
| Folder Browsing | USB Root Confirmed |
| Application Used | Not Determined |
| File Copy Activity | Not Proven |
| Timeline Reconstructed | Yes |

---

# Tools Used

- FTK Imager
- Arsenal Image Mounter
- KAPE (gKAPE)
- Registry Explorer
- USB Detective
- Timeline Explorer
- LECmd
- MFTECmd
- RECmd
- EvtxECmd
- Windows Registry
