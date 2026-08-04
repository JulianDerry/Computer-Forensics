# PicoCTF Steganography Challenge - Hidden Metadata & Steghide Extraction

Forensic steganographic analysis of a PNG image to identify and recover concealed data embedded within image pixel channels.

## Case Information

| Field | Value |
|---|---|
| **Case Reference** | jpg Image Steganography Investigation |
| **Image Source** | HIVE CONSULT |
| **Investigator** | Julian Derry |
| **Date of Analysis** | ‎February ‎17, ‎2026 |
| **Platform** | Kali Linux |

---
## Overview

This challenge involved analyzing an image file (`img.jpg`) to recover a hidden payload and extract the final flag. The investigation focused on metadata analysis, Base64 decoding, and steganographic extraction.

- **Target File:** `img.jpg`
- **Objective:** Recover the hidden secret and final flag.
  
<img width="640" height="640" alt="1" src="https://github.com/user-attachments/assets/0eb2b75e-80c9-494e-af04-1de9ac64aee4" />

---

## Step 1: Metadata Examination

The first step was to inspect the image metadata using **ExifTool**.

```bash
exiftool img.jpg
```
<img width="544" height="482" alt="2" src="https://github.com/user-attachments/assets/f9ae41ab-d991-44f9-8355-6d3257cac25b" />

The output revealed a suspicious **Comment** field:

```text
c3Rle2hpZGU6cEF6endvcmV9
```
<img width="538" height="62" alt="3" src="https://github.com/user-attachments/assets/4f653c8f-1683-4efb-8232-ceda39244b00" />


This string appeared to be Base64-encoded.

---

## Step 2: Base64 Decoding (Layer 1)

Decode the metadata value:

```bash
echo c3Rle2hpZGU6cEF6endvcmV9 | base64 -d
```
<img width="358" height="84" alt="4" src="https://github.com/user-attachments/assets/2902a7cc-50ea-4f58-872a-d3bc401e2611" />

Result:

```text
steghide:cEF6endvcmV=
```

### Interpretation

- `steghide` → indicates the steganography tool likely used.
- `cEF6endvcmV=` → another Base64-encoded value.

---

## Step 3: Base64 Decoding (Layer 2)

Decode the second value:

```bash
echo cEF6endvcmV= | base64 -d
```
<img width="358" height="84" alt="4" src="https://github.com/user-attachments/assets/57d3a3b0-dd44-4d15-9ea3-51c37b79f285" />

Result:

```text
pAzzword
```

This is the **Steghide passphrase**.

---

## Step 4: Extract the Hidden Payload

Use the recovered passphrase with **Steghide**:

```bash
steghide extract -sf img.jpg
```
<img width="325" height="80" alt="5" src="https://github.com/user-attachments/assets/c57fc5ba-3cc3-4e13-ba68-4cc8a5816e4c" />

When prompted for the passphrase, enter:

```text
pAzzword
```

A hidden text file was extracted successfully.

---

## Step 5: Recover the Flag

Read the contents of the extracted file:

```bash
cat secret.txt
```
<img width="292" height="77" alt="6" src="https://github.com/user-attachments/assets/e9fbffc3-8c3d-45ea-9ad8-44723eb9236a" />

Output:

```text
picoCTF{h1dd3n_1n_1m4g3_5d4cba73}
```

---

# Final Flag

```text
picoCTF{h1dd3n_1n_1m4g3_5d4cba73}
```

---

# Tools Used

- **ExifTool** - metadata analysis
- **base64** - decoding encoded strings
- **Steghide** - steganographic extraction
- **cat** - file content inspection

---

# Investigation Summary

| Stage | Action | Result |
|---|---|---|
| Metadata analysis | `exiftool img.jpg` | Found encoded comment |
| Base64 decode #1 | Decoded metadata value | `steghide:cEF6endvcmV=` |
| Base64 decode #2 | Decoded passphrase value | `pAzzword` |
| Steghide extraction | Extract hidden file | Success |
| Flag recovery | Read extracted file | `picoCTF{h1dd3n_1n_1m4g3_5d4cba73}` |

---

## Key Takeaway

This challenge demonstrates how valuable **image metadata** can be during forensic and steganographic investigations. A seemingly harmless metadata field contained the clues required to recover both the extraction passphrase and the final hidden flag.
