# Microsoft Office 2013 Password Recovery Analysis

| Field | Value |
|---|---|
| **Case Reference** | Microsoft Office 2013 Password Recovery |
| **Image Source** | HIVE CONSULT |
| **Investigator** | Julian Derry |
| **Date of Analysis** | ‎February ‎16, ‎2026 |
| **Platform** | Kali Linux |
| **Tools** | office2john, Hashcat, John the Ripper, rockyou.txt, Linux command-line shell |
---

## Overview

This examination documents the recovery of a password-protected Microsoft Office 2013 document through offline password cracking techniques. The process involved identifying the encryption type, extracting the Office hash, selecting the correct cracking mode, and validating successful decryption.

---

## Methodological Note

This examination was conducted during my early practical training period in digital forensics. At the time of the analysis, a cryptographic integrity hash (for example, SHA-256) of the original Microsoft Word document was **not calculated prior to examination**.

The omission occurred because I was still developing my forensic workflow and was not yet aware of the importance of documenting evidence integrity before analysis. My current forensic practice includes hashing evidence files before examination and recording the resulting hash values as part of the chain of custody and integrity verification process.

No retrospective hash values have been added to this report.

---

## Encrypted Document Prompt (Before Cracking)

The Microsoft Word document displayed a password prompt when opened, indicating that the file was encrypted and access to its contents was restricted.

The prompt confirmed that the document used **Microsoft Office 2013 document encryption**. At this stage, the contents could not be viewed pending successful password recovery.

Subsequent analysis confirmed that the encryption used:

- SHA-256
- 100,000 iterations
- 256-bit key size

### Screenshot 1 – Password Prompt

<img width="597" height="274" alt="1" src="https://github.com/user-attachments/assets/cc971e57-5702-40f1-aaa1-312b479536df" />

---

## Hash Extraction

The encrypted document was processed with **office2john** to extract the password hash in a format suitable for offline cracking.

The extracted hash began with:

```text
$office$*2013*100000*256*16*...
```

This structure confirmed the use of **Microsoft Office 2013 encryption**.

The hash contained:

- Version identifier (2013)
- Iteration count (100,000)
- Key size (256-bit)
- Salt value
- Encrypted verifier data
- Encrypted hash data

The full hash was saved to a text file while preserving the complete `$office$` structure.

### Screenshot 2 – office2john Hash Extraction

<img width="926" height="138" alt="2" src="https://github.com/user-attachments/assets/4c6d1608-0379-42fc-a646-bba275d7903c" />

---

## Preparing the Hash File

After extraction, the full structured hash was saved to **officehash.txt**.

Maintaining the complete line exactly as generated, including the `$office$` prefix, was necessary for compatibility with cracking tools.

Command used:

```bash
echo '$office$*2013*100000*256*16*...' > officehash.txt
```

### Screenshot 3 – Hash Saved to officehash.txt

<img width="690" height="130" alt="3" src="https://github.com/user-attachments/assets/8a2ac271-102f-4970-9208-b8e207d784c2" />

---

## Verifying the Correct Hashcat Mode

Before starting the attack, the correct Hashcat mode was verified.

Command used:

```bash
hashcat --help | grep -i office
```

The output listed Microsoft Office versions and their corresponding mode numbers. Because the extracted hash contained `*2013*`, the selected mode was:

- **Mode 9600 – MS Office 2013**

Using an incorrect mode would prevent successful cracking even if the password were correct.

### Screenshot 4 – Hashcat Mode Verification

<img width="702" height="243" alt="4" src="https://github.com/user-attachments/assets/77572e89-7fc8-496a-92ec-1f05ae0213eb" />

---

## Cracking the Hash with Hashcat

The Office 2013 hash was attacked using **Hashcat** in mode **9600**.

Command used:

```bash
hashcat -m 9600 officehash.txt /usr/share/wordlists/rockyou.txt
```

This command performed a wordlist attack using the **rockyou.txt** password list.

### Screenshot 5 – Hashcat Cracking Process

<img width="903" height="135" alt="5" src="https://github.com/user-attachments/assets/b0bc129c-5708-4665-b17f-76ada0ccbd33" />
<img width="899" height="443" alt="9" src="https://github.com/user-attachments/assets/e98be173-3902-41e6-bc16-4602bbee9e46" />

---

## Cracking the Hash with John the Ripper

The same hash was also tested with **John the Ripper** using the Office format.

Command used:

```bash
john --format=office officehash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

This provided an alternative verification of the password recovery process.

### Screenshot 6 – John the Ripper Cracking Process

<img width="847" height="391" alt="6" src="https://github.com/user-attachments/assets/a7a49360-931b-4a69-8b8b-1882a712b3a8" />

---

## Successful Document Decryption

After password recovery, the document was opened successfully using the recovered password. This confirmed that the recovered credential correctly derived the encryption key required to decrypt the document contents.

### Screenshot 7 – Successfully Opened Document

<img width="1398" height="1057" alt="7" src="https://github.com/user-attachments/assets/2bf90da4-c6ba-42ca-8037-4941c3123c6f" />

---

## Validation of Findings

Successful decryption validated the following:

- Correct hash extraction
- Proper identification of the encryption type
- Accurate selection of Hashcat mode 9600
- Effective wordlist-based password attack

The examination demonstrated a complete and successful recovery workflow for a Microsoft Office 2013 encrypted document.

No retrospective hash values have been added to this report.

