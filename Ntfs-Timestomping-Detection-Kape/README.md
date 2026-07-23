| Property | Value |
|----------|-------|
| Document Type | Digital Forensics & Incident Response |
| Investigation Type | NTFS Timestomping Analysis |
| Primary Tools | KAPE, MFTECmd, Timeline Explorer |
| Filesystem | NTFS |
| Analyst | Julian Derry |
| Date | June 2026 |
| Status | Completed |

# Table of Contents

-   [Executive Summary](#executive-summary)
-   [Scope & Objectives](#scope--objectives)
-   [Methodology](#methodology)
    -   [Timestomping Simulation](#timestomping-simulation)
    -   [Collecting the \$MFT with KAPE](#collecting-the-mft-with-kape)
    -   [Timeline Analysis](#timeline-analysis)
-   [Findings](#findings)
-   [Analyst Notes](#analyst-notes)
-   [Tools Referenced](#tools-referenced)
-   [Conclusion](#conclusion)

------------------------------------------------------------------------

# Detecting and Analyzing Timestomping

## Digital Forensics & Incident Response

# Executive Summary

Timestomping is an anti-forensic technique used by threat actors to
manipulate NTFS timestamps on a Windows file system. By forging a file's
timestamps, an attacker can make a newly created malicious tool appear
as though it has existed on a system for years. When an analyst filters
artifacts by the incident timeframe, the stomped file falls outside that
window while remaining hidden in plain sight.

This document demonstrates how to detect and analyze timestomping using
**KAPE**, **MFTECmd**, and **Timeline Explorer**.

------------------------------------------------------------------------

# Scope & Objectives

**Scope**

This document demonstrates a controlled timestomping simulation against
an NTFS file and shows how forensic artifacts expose timestamp
manipulation.

**Objectives**

-   Simulate timestomping using PowerShell.
-   Acquire the `$MFT` using KAPE.
-   Parse the `$MFT` using MFTECmd.
-   Detect timestamp manipulation using Timeline Explorer.
-   Explain the forensic indicators that support a timestomping
    conclusion.

------------------------------------------------------------------------

# Methodology

## Timestomping Simulation

### Original File Timestamps

A test file was created with the following timestamps:

-   **Date Created:** 25 June 2026 -- 11:51 AM
-   **Date Modified:** 25 June 2026 -- 11:51 AM

<img width="1112" height="551" alt="1" src="https://github.com/user-attachments/assets/3cbd4348-2961-4365-bd9b-11c0c482bf93" />

**Figure 1 -- Original file timestamps before modification**

### Stomping the Timestamps

The timestamps were modified using PowerShell.

``` powershell
powershell -command "(Get-Item 'C:\Users\jderr\OneDrive\Documents\Timestomped file.txt').LastWriteTime = '05/20/2022 10:30 AM'"
```

``` powershell
powershell -command "(Get-Item 'C:\Users\jderr\OneDrive\Documents\Timestomped file.txt').CreationTime = '05/20/2021 11:30 AM'"
```
<img width="1073" height="541" alt="2" src="https://github.com/user-attachments/assets/b948dd9b-a742-4d2e-963d-38c74a1d4e9f" />


The file now appears several years older than its true creation date.

**Figure 2 -- File timestamps after timestomping via PowerShell**

## Collecting the \$MFT with KAPE

The Master File Table (\$MFT) stores two independent timestamp records
for every NTFS object and is the primary artifact used to detect
timestomping.

Steps:

1.  Open **gKAPE**.
2.  Set the target source to \*\*C:\*\*.
3.  Choose a destination folder.
4.  Select the **\$MFT** target.
5.  Enable the **MFTECmd\_\$MFT** module.
6.  Execute KAPE.
7.  Review the generated CSV.

**Figure 3 -- gKAPE configuration for \$MFT collection and parsing**

<img width="1282" height="925" alt="3" src="https://github.com/user-attachments/assets/854e218a-4bab-46fa-bc3c-fb12eb5dae5b" />

## Timeline Analysis

Load the parsed CSV into Timeline Explorer and locate the target file.

# Findings

## Finding 5.1 -- Timestamp Mismatch (SI \< FN)

NTFS stores timestamps in both `$STANDARD_INFORMATION (0x10)` and
`$FILE_NAME (0x30)` attributes.

If the 0x10 timestamp predates the 0x30 timestamp, MFTECmd reports **SI
\< FN**, indicating likely timestomping into the past.

**Figure 4 -- SI \< FN indicator in Timeline Explorer**

<img width="1745" height="141" alt="4" src="https://github.com/user-attachments/assets/c22f746c-3388-42c5-b912-3098759ac9b1" />

## Finding 5.2 -- Zeroed Subseconds

Legitimate NTFS timestamps contain nanosecond precision.

Many timestomping methods produce timestamps ending in:

`0000000`

This is a strong secondary indicator but should always be corroborated
with additional evidence because legitimate software may also produce
zeroed subseconds.

<img width="1147" height="447" alt="5" src="https://github.com/user-attachments/assets/4a51259f-a0a5-47c8-9fc9-8fe946864058" />

**Figure 5 -- Zeroed subseconds in Timeline Explorer**

## Finding 5.3 -- Created (0x30) Timestamp

MFTECmd only populates the **Created (0x30)** column when it differs
from **Created (0x10)**.

Its presence confirms a discrepancy between the kernel-maintained and
user-modifiable timestamp records.

Note that the **SI \< FN** and **uSec Zeroes** columns are generated by
MFTECmd and are not stored directly in the raw \$MFT.

<img width="1147" height="447" alt="6" src="https://github.com/user-attachments/assets/e5ff01cd-f1c7-43c2-8c34-f5c8aee9c090" />

------------------------------------------------------------------------

# Analyst Notes

## Attacker Behaviour

Threat actors generally timestomp files into the past rather than the
future to place malicious files outside the expected investigation
window.

When both **SI \< FN** and **zeroed subseconds** are present alongside a
populated **Created (0x30)** value, the combined evidence strongly
supports deliberate timestamp manipulation.

## Timezone Handling

Never change the timezone on the evidence system.

Best practice:

-   Configure forensic tools to display UTC.
-   Determine the target timezone from Registry artifacts.
-   Perform timeline correlation in UTC.
-   Document timezone offsets.

------------------------------------------------------------------------

# Tools Referenced

-   KAPE (Kroll Artifact Parser and Extractor)
-   MFTECmd
-   Timeline Explorer

------------------------------------------------------------------------

# Conclusion

Timestomping manipulates user-visible NTFS timestamps but typically
leaves detectable inconsistencies within NTFS metadata. By collecting
the `$MFT` with KAPE, parsing it with MFTECmd, and examining the results
in Timeline Explorer, investigators can identify discrepancies such as
**SI \< FN**, **zeroed subseconds**, and differing **Created (0x30)**
values. Individually these indicators are useful; collectively they
provide strong evidence supporting timestamp manipulation while
reinforcing the need to correlate multiple artifacts before reaching a
forensic conclusion.
