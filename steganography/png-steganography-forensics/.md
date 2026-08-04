# PNG Image Steganography Investigation

Forensic steganographic analysis of a PNG image to identify and recover concealed data embedded within image pixel channels.

## Case Information

| Field | Value |
|---|---|
| **Case Reference** | PNG Image Steganography Investigation |
| **Image Source** | https://tinyurl.com/2zp9acat |
| **Investigator** | *Julian Derry* |
| **Date of Analysis** | ‎February ‎25, ‎2026 |
| **Platform** | Kali Linux |

---

## Objective

Identify and extract hidden or embedded information from the provided PNG image using forensic and steganographic analysis techniques, with the goal of recovering the challenge flag.

---

## Methodology

### 1. Acquisition and Preservation

The PNG image was acquired from the provided source and preserved in its original state to maintain evidential integrity throughout the examination.

### 2. Initial Inspection

#### Metadata Analysis

Metadata was examined using **ExifTool**.

```bash
exiftool red.png
```

**Result:** No suspicious metadata or hidden flag identified.

#### Steghide Examination

A steganographic extraction attempt was performed using **Steghide** with common password guesses.

```bash
steghide extract -sf red.png
```

**Result:** No hidden data was extracted.

---

## Advanced Steganographic Analysis

A deep analysis was conducted using **zsteg**, which is designed to detect hidden data within PNG and BMP images, particularly in Least Significant Bit (LSB) channels.

### Command

```bash
zsteg red.png
```
<img width="1243" height="440" alt="1" src="https://github.com/user-attachments/assets/55025c19-5da2-4544-b19e-c9c9d3d952f4" />

### Result

Hidden data was identified within the image's pixel LSB channels.

<img width="1276" height="467" alt="2" src="https://github.com/user-attachments/assets/ee932c4d-5246-4609-a0e2-cc1afa8d9fdd" />

---

## String Decoding

The recovered encoded string was decoded using the Linux `base64` utility.

### Command

```bash
echo "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ" | base64 --decode
```
<img width="1274" height="435" alt="3" src="https://github.com/user-attachments/assets/02945560-755f-4583-8e7a-7ba7d4252f30" />

### Decoded Output

```text
picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355}
```
<img width="1378" height="883" alt="4" src="https://github.com/user-attachments/assets/6ddb7500-e7ef-4c37-a3e1-bbad88b83a5d" />

---

## Findings

| Analysis Phase | Result |
|---|---|
| **File Examined** | `red.png` |
| **Metadata Inspection** | No suspicious metadata detected |
| **Steghide Analysis** | No hidden data extracted |
| **zsteg Analysis** | Hidden data identified in pixel LSB channels |
| **Recovered String** | `cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ` |
| **Decoded Output** | `picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355}` |

---

## Conclusion

The investigation successfully recovered a Base64-encoded string embedded within the image's Least Significant Bit layers. Decoding the string revealed the challenge flag:

```text
picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355}
```

The results indicate that the hidden data was embedded using **Least Significant Bit (LSB) steganography**, with the **red color channel** acting as the carrier channel for the concealed information.

---

## Tools Used

- **zsteg** – LSB steganography detection
- **base64** (GNU Core Utilities) – Encoding and decoding utility
- **Kali Linux** – Analysis platform
- **ExifTool** – Metadata inspection
- **Steghide** – Steganography detection

---

## Reproduction Steps

```bash
# Metadata inspection
exiftool red.png

# Steghide extraction attempt
steghide extract -sf red.png

# LSB analysis
zsteg red.png

# Decode recovered string
echo "cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ" | base64 --decode
```

---

## Repository Structure

```text
.
├── red.png
├── README.md
└── screenshots/
```

---

## Analyst Notes

This project demonstrates a basic forensic workflow for PNG steganography analysis:

1. Evidence preservation
2. Metadata inspection
3. Steganography screening
4. LSB analysis
5. Encoded data recovery
6. Decoding and interpretation

It is intended for educational and portfolio purposes within a digital forensics and CTF context.

---

**Prepared by:** *[Investigator Name]*

**Date:** *[Insert Date]*
