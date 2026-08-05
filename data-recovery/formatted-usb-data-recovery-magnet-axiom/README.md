# Formatted USB Data Recovery Using Magnet AXIOM

Digital forensic examination demonstrating recovery of data from a formatted USB flash drive using Magnet AXIOM Process and AXIOM Examine.

## Case Information

| Field | Value |
|---|---|
| **Case Reference** | DFIR-2026-RMF-011 |
| **Image Source** | Personal USB flash drive 8GB-Generic Vendor |
| **Analyst** | Julian Derry |
| **Date of Analysis** | ‎February ‎09, ‎2026 |
| **Time of Analysis** | ‎4:45 PM GMT |
| **Platform** | Windows |
| **Tools** | Magnet Axiom 8.2, Magnet Examine 8.2 |
---

## Overview

This case study documents the forensic acquisition and analysis of a formatted USB flash drive using **Magnet AXIOM Process** and **Magnet AXIOM Examine**. The objective was to assess whether previously stored data remained recoverable after formatting and to document the workflow from acquisition through examination and verification.

The examination confirmed that residual data remained present on the device and could be reconstructed through forensic processing. The case highlights an important forensic principle: formatting a storage device does not necessarily result in permanent data destruction.

---

## Investigation Objectives

- Acquire a forensic image of the USB flash drive.
- Process the evidence using Magnet AXIOM.
- Recover deleted or formatted data where possible.
- Verify recovered artifacts and associated metadata.
- Assess the effectiveness of the formatting operation.

---

## Case Initialization

A new case was created in **Magnet AXIOM Process** and the formatted USB flash drive was connected to the forensic workstation. The device was identified as a removable storage device and the displayed device details were reviewed to confirm that the correct evidence source had been selected.

A dedicated case folder was created to store acquired evidence, case files, and examination outputs for consistent organization throughout the investigation.

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/e68baf10-f64a-4385-b629-ed6b2d54202c" />

---

## Evidence Source Selection

The USB flash drive was selected as the sole evidence source by choosing **Computer** from the list of available evidence sources in Magnet AXIOM Process.

**Windows** was selected as the source operating system. This ensured that the acquisition remained limited to the target device and prevented the inclusion of unrelated storage media attached to the workstation.

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/3f368dd3-601c-492b-96be-ef09f10f408b" />
<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/a71dd4b0-2965-4dfb-82fc-b25087588494" />


---

## Evidence Acquisition

During the evidence source stage, **Acquire Evidence** was selected because the acquisition was being performed directly from the connected USB flash drive rather than importing an existing forensic image.

The target device was identified from the list of mounted drives and selected for acquisition. The selected device had a capacity of **8 GB**.

Under **Image Type**, a **Full** acquisition was chosen to create a bit-for-bit forensic image of the device. The image was stored in **E01** format, a commonly accepted forensic imaging standard.

Processing options were configured to support recovery of deleted and formatted data, including examination of both allocated and unallocated space.

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/dd523c1c-474a-475c-aa9c-a770704b47de" />
<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/88c05fbc-ab2c-4c78-a56e-4c5251cb7258" />
<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/9a09705e-b262-4313-8874-e07c4f87d1a9" />

---

## Processing and Analysis

AXIOM Process analyzed the acquired evidence by examining the file system structure and residual data blocks. Although the USB flash drive had been formatted, traces of previously stored data were detected during processing.

The duration of processing depended on the size of the evidence source, the number of artifact categories selected for analysis, and the specifications of the host workstation.

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/ac33388c-daa2-4d25-b995-a89d65c7e08b" />

---

## Data Recovery and Examination

When processing completed, the case was opened automatically in **Magnet AXIOM Examine** for detailed review of the recovered artifacts.

Recovered files and folders were identified within the processing results. The presence of reconstructed file entries indicated that the formatting operation had not fully overwritten the underlying data.

AXIOM Examine organized the results into several review sections, including:

- **Case Overview**
- **Evidence Overview**
- **Place to Start**
- **Artifact Categories**

These sections were used to review case details, evidence information, recovered artifact counts, and associated operating system information.

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/24cabd2a-f3d0-437a-bb25-6bca64cefa2f" />

---

## Artifact Verification

Recovered files were previewed together with available metadata to confirm their relevance to the investigation. This verification step was performed to ensure that the artifacts originated from the acquired USB flash drive and remained accessible for forensic examination.

<img width="1920" height="1080" alt="13" src="https://github.com/user-attachments/assets/a8f5a6b7-ccfc-4e0d-a561-81c6ad34d110" />

---

## Hash Verification

Hash values were not recorded during this acquisition exercise, as this was my first practical attempt at removable-media forensic imaging. In subsequent forensic acquisition and recovery exercises, I incorporated hash calculation and verification as part of my standard evidence-handling procedure.

---

## Key Findings

- Data remained recoverable after the flash drive had been formatted.
- Reconstructed file entries were identified during examination.
- Residual artifacts were recovered from the formatted media.
- The formatting process did not permanently remove all underlying data.

---

## Conclusion

The examination demonstrated successful recovery of data from a formatted USB flash drive and highlighted the effectiveness of **Magnet AXIOM** in removable-media forensic analysis. The case reinforces the importance of proper data sanitization procedures when permanent destruction of digital data is required.

---

## Skills Demonstrated

- Removable media forensic acquisition
- E01 forensic imaging
- Magnet AXIOM evidence processing
- Deleted and formatted data recovery
- Artifact examination and verification
- Forensic case documentation

---

## Portfolio Context

This case forms part of a practical digital forensics portfolio focused on removable-media investigations, forensic imaging, artifact recovery, and evidence verification using industry-standard forensic tools.
