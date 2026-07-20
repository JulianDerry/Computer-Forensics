# Computer-Forensics

Windows, Linux, memory, and disk forensics — registry, browser, event log, and file-system artefacts, plus the tools, scripts, and case reports used to work them.

Part of the [Digital-Forensics](../) collection of repositories:
`Computer-Forensics` · `Mobile-Forensics` · `Cloud-Forensics` · `Vehicle-Forensics`

## Repository Structure

```
Computer-Forensics/
├── Case-Reports/
│   ├── CTFs/
│   ├── Investigation-Reports/
│   └── Lab-Cases/
├── Disk-Forensics/
│   ├── AFF4/
│   ├── Data-Recovery/
│   ├── E01/
│   ├── Hashing/
│   └── Imaging/
├── Linux-Forensics/
│   ├── Bash-History/
│   ├── File-System/
│   ├── Logs/
│   └── User-Artifacts/
├── Memory-Forensics/
│   ├── Malware-Analysis/
│   ├── Memory-Dumps/
│   ├── Network-Connections/
│   ├── Process-Analysis/
│   └── Volatility3/
├── Scripts/
│   ├── Automation/
│   ├── PowerShell/
│   └── Python/
├── Tools/
│   ├── Autopsy/
│   ├── FTK-Imager/
│   ├── KAPE/
│   ├── Magnet-AXIOM/
│   ├── Registry-Explorer/
│   └── X-Ways/
└── Windows-Forensics/
    ├── Amcache/
    ├── Browser-Forensics/
    │   ├── Browser-Databases/
    │   ├── Chrome/
    │   ├── Edge/
    │   └── Firefox/
    ├── Event-Logs/
    │   ├── Application-Logs/
    │   ├── PowerShell-Logs/
    │   ├── Security-Logs/
    │   └── System-Logs/
    ├── File-System/
    │   ├── $LogFile/
    │   ├── FAT32/
    │   ├── MFT/
    │   ├── NTFS/
    │   └── USN-Journal/
    ├── JumpLists/
    ├── LNK-Files/
    ├── Prefetch/
    ├── Registry/
    ├── Shellbags/
    ├── Shimcache/
    ├── Timeline-Analysis/
    └── USB-Forensics/
```

Each subfolder holds artefacts, notes, scripts, and reports specific to that area. `.gitkeep` placeholders mark empty folders so the structure is preserved in Git — replace them with real content as it's added.

## Contributing / Usage

Add write-ups, scripts, and case notes into the relevant subfolder above. Keep tool-specific output (e.g. KAPE, Autopsy, Cellebrite exports) under `Tools/` or `Case-Reports/` as appropriate.
