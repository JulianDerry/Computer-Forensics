# PicoCTF Steganography Challenge - Hidden Metadata & Steghide Extraction

## Overview

This challenge involved analyzing an image file (`img.jpg`) to recover a hidden payload and extract the final flag. The investigation focused on metadata analysis, Base64 decoding, and steganographic extraction.

- **Target File:** `img.jpg`
- **Objective:** Recover the hidden secret and final flag.

---

## Step 1: Metadata Examination

The first step was to inspect the image metadata using **ExifTool**.

```bash
exiftool img.jpg
```

The output revealed a suspicious **Comment** field:

```text
c3Rle2hpZGU6cEF6endvcmV9
```

This string appeared to be Base64-encoded.

---

## Step 2: Base64 Decoding (Layer 1)

Decode the metadata value:

```bash
echo c3Rle2hpZGU6cEF6endvcmV9 | base64 -d
```

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
