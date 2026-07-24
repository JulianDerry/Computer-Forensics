# Windows USB Activity Analysis Using KAPE and Eric Zimmerman Tools

A host based Windows USB forensic investigation performed against an acquired E01 image to identify USB device activity, reconstruct the timeline, determine user interaction and analyze artifacts using KAPE, Registry Explorer, Eric Zimmerman tools, USB Detective and Timeline Explorer.

---

## Case Information

| Property | Value |
|----------|-------|
| Investigation Type | Windows USB Forensics |
| Evidence | E01 Disk Image |
| Image Name | NFTS_USB_FORENSICS.E01 |
| Primary Tool | KAPE (gKAPE v1.3.0.2) |
| Secondary Tools | Registry Explorer, USB Detective, LECmd, MFTECmd, RECmd, EvtxECmd, Timeline Explorer |
| Operating System | Windows 11 Pro 25H2 |
| Analyst | Julian Derry |
| Status | Completed |

---

# Table of Contents

- [Host Details](#host-details)
- [1. Acquire Computer](#1-acquire-computer)
- [2. Mount the Evidence](#2-mount-the-evidence)
- [3. Evidence Collection](#3-evidence-collection)
- [4. Timeline Creation](#4-timeline-creation)
- [5. Identify the USB Device](#5-identify-the-usb-device)
- [6. Determine Assigned Drive Letter](#6-determine-assigned-drive-letter)
- [7. Findings](#7-findings)
- [8. Files Opened From the USB](#8-files-opened-from-the-usb)
- [9. Windows User Interaction](#9-windows-user-interaction)
- [10. Applications Used](#10-applications-used)
- [11. File Copy Analysis](#11-file-copy-analysis)
- [12. Registry Integrity](#12-registry-integrity)

---

# Host Details

| Property | Value |
|----------|-------|
| Device name | win_11_vm |
| Processor | 13th Gen Intel(R) Core(TM) i5-13420H (2.61 GHz) (2 processors) |
| Installed RAM | 8.00 GB |
| Graphics card | VMware SVGA 3D (4 MB) |
| Storage | 37 GB of 64 GB used |
| Device ID | XXXXXXXXXXXXXXXXXXX |
| Product ID | XXXXXXXXXXXXXXXXXXX |
| System type | 64 bit operating system, x64 based processor |
| Pen and touch | Touch support with 8 touch points |
| Edition | Windows 11 Pro |
| Version | 25H2 |
| Installed on | 6/25/2026 |
| System Timezone | GMT |
| OS Build | 26200.8875 |
| Experience | Windows Feature Experience Pack 1000.26100.334.0 |

> **Figure 1. Host machine details**


<img width="889" height="919" alt="Screenshot 2026-07-20 133537" src="https://github.com/user-attachments/assets/4976c3ec-106e-4156-9e5d-b9799b398f7c" />

---

# 1. Acquire Computer

I acquired an E01 image, calculated the hash value using MD5 and SHA256 and verified the hash values.

| Property | Value |
|----------|-------|
| Image file name | NFTS_USB_FORENSICS.E01 |
| Image Name | NFTS_USB_FORENSICS.E01 |
| Acquisition Tool | FTK Imager v 8.2.0.33 SP1 |
| Date | 7/20/2026 |
| MD5 Hash | 17d2ea844e4f66b14e1d6ac60b4d1c91 |
| SHA1 Hash | 059e70eb50e8d2082fa379bb9b093bd4234a0480 |
| Examiner | JULIAN DERRY |

> **Figure 2. FTK Imager acquisition**

```
Insert screenshot here
```

---

# 2. Mount the Evidence

I mounted my E01 using Arsenal Image Mounter v 3.12.331. I chose to mount the image file using FTK Imager because it mounts the image as a physical disk, not just a logical volume. Also, because it would allow the other tools I'd use (KAPE, Eric Zimmerman's tools, VM software) to interact with it like actual storage.

I also went with the option of Disk device, write temporary, storing the write changes in an alternate differencing file location so as not to modify or alter the image file.

In this case, my mount image showed up as **F:**

> **Figure 3. Arsenal Image Mounter configuration**

```
Insert screenshot here
```

---

# 3. Evidence Collection

I ran KAPE (gKAPE v1.3.0.2) and collected the following targets.

| Registry Targets | Additional Targets |
|------------------|--------------------|
| NTUSER.DAT | USBDetective |
| SYSTEM | $J |
| SOFTWARE | $LOGFILE |
| SAM | $MFT |
| SECURITY | |
| USRCLASS.DAT | |

Under Module, I selected **EZParser**.

I also added USBDetective Windows App for redundancy and USBDevicesLogs for redundancy of USB artifacts and logs.

> **Figure 4. KAPE collection configuration**

```
Insert screenshot here
```

---

# 4. Timeline Creation

To create a timeline, I ran the following KAPE modules.

| Modules |
|---------|
| EZParser |
| MFTECmd |
| RECmd |
| EvtxECmd |

Inside the Module Output folder were several folders categorized with CSV files which were viewed using Timeline Explorer.

> **Figure 5. Timeline Explorer**

```
Insert screenshot here
```

---

# 5. Identify the USB Device

To confirm the USB device identity, I used Registry Explorer, loaded the SYSTEM hive and explored:

```text
SYSTEM\CurrentControlSet\Enum\USBSTOR
```

We can see three USB devices. Our main focus is the last USB inserted on **2026-07-20 13:23:17**.

The registry displays:

- Manufacturer
- Version
- Serial Number
- Device Name
- Installed
- First Installed
- Last Connected
- Last Removed

**NB**

`8&25cc1fb8&0` is a Windows generated ParentIdPrefix which Windows creates when the USB device does not report a unique serial number to the operating system.

To verify the actual serial number, I checked:

- SYSTEM\CurrentControlSet\Enum\USB
- MountedDevices
- SetupAPI.dev.log

I also verified the device using USB Detective.

**NB**

I later came to learn that the serial number can be easily identified by looking at the numbers assigned to the drive in the folder name listed under USBSTOR and could either be globally or system unique. The LastWrite time can also be identified in the LastWrite field by the serial number folder.

> **Figure 6. USBSTOR Registry key**

```
Insert screenshot here
```

> **Figure 7. USB Detective verification**

```
Insert screenshot here
```

---

# 6. Determine Assigned Drive Letter

At first, I couldn't determine the assigned drive letter in the other locations so I checked MountedDevices.

The serial number confirmed the USB under examination.

Instead of a drive letter, Windows recorded:

```text
\??\Volume
```

Possible reasons include:

- Low quality flash controller
- Corrupted partition tables
- AutoMount disabled

My guess is the first option because the other drives had letters successfully assigned.

> **Figure 8. MountedDevices**

```
Insert screenshot here
```

---

# 7. Findings

| Property | Value |
|----------|-------|
| USB Device Name | Generic Mass Storage USB Device |
| Manufacturer | MASS STORAGE USB DEVICE / Ven_Generic |
| Model | VID_349C&PID_0418 |
| Serial Number | 20240418000000 |
| Installed | 2026-07-20 13:23:17.4716162 |
| First Installed | 2026-07-20 13:23:17.4716162 |
| Last Connected | 2026-07-20 13:23:17.4639635 |
| Last Removed | 2026-07-20 13:23:30.1536493 |
| Port Number | Port_#0001.Hub_#0005 |
| User profile interacted with | Jderr |
| Timezone | UTC |
| Assigned letter | \??\Volume (Later confirmed as E: through passed lnk file and shellbags) |

The manufacturer appears as Generic MASS STORAGE USB DEVICE and Ven_Generic because the USB is unbranded.

---

# 8. Files Opened From the USB

To find the files that were opened from the USB drive, I checked the **LECmd.csv** output filtered by Drive Type and looked at **Removable Storage Media**.

The Local Path started with **E:\**.

The Working Directory also started with **E:\**.

The file:

```text
Leasafric Logistics Ghana Limited.pdf
```

was visible.

The Source Created and Target Accessed dates matched the USB timeline.

Under **Target Name** in **NTUSER.DAT.csv**, files I accessed were also visible together with **USB DRIVE (E:)**.

> **Figure 9. LECmd output**

```
Insert screenshot here
```

---

# 9. Windows User Interaction

Jderr was the user that interacted with the USB.

This was the only user profile that appeared in:

- FileFolderAccess
- Jump Lists
- AutomaticDestinations
- LECmd.csv

> **Figure 10. User artifacts**

```
Insert screenshot here
```

---

# 10. Applications Used

Microsoft Edge (Chromium) opened:

```text
Leasafric Logistics Ghana Limited.pdf
```

The information was obtained from **AutomaticDestinations.csv**.

The timestamps aligned with the USB activity.

> **Figure 11. AutomaticDestinations.csv**

```
Insert screenshot here
```

---

# 11. File Copy Analysis

No files were copied between the USB and the computer.

Both Local Path and TargetID Absolute Path showed only:

```text
E:\
```

> **Figure 12. LNK analysis**

```
Insert screenshot here
```

---

# 12. Registry Integrity

During analysis, Registry Explorer reported that the registry hive was in a dirty state and that associated transaction logs (.LOG1 and .LOG2) were present but required replay.

This observation was anticipated because the evidence was acquired from a live Windows system.

USB related artifacts including connection history, LastWrite timestamps, Installed, First Installed, Last Connected and Removed may therefore represent only the last committed transaction.

Prior to this investigation, I produced a separate technical guide documenting registry transaction log replay and dirty hive recovery.

Reference:

```
Registry Transaction Log Replay (Dirty Hive Recovery)

Insert documentation link
```

**NB**

The SYSTEM hive contained the registry value **NtfsDisableLastAccessUpdate** with a value of **2147483650 (0x80000002)**.

This indicates that NTFS Last Access timestamp updates were disabled under a system managed configuration.

Therefore, NTFS Last Access timestamps may not reliably reflect file access activity.
