# Microsoft Office 2013 Password Recovery Analysis

| Field | Value |
|---|---|
| **Case Reference** | Microsoft Office 2013 Password Recovery |
| **Image Source** | HIVE CONSULT |
| **Investigator** | Julian Derry |
| **Date of Analysis** | ‎February ‎16, ‎2026 |
| **Platform** | Kali Linux |
---

## Overview

This examination documents the recovery of a password-protected Microsoft Office 2013 document through offline password cracking techniques. The process involved identifying the encryption type, extracting the Office hash, selecting the correct cracking mode, and validating successful decryption.

---

## Encrypted Document Prompt (Before Cracking)

The Microsoft Word document displayed a password prompt when opened, indicating that the file was encrypted and access to its contents was restricted.

The prompt confirmed that the document used **Microsoft Office 2013 document encryption**. At this stage, the contents could not be viewed pending successful password recovery.

Subsequent analysis confirmed that the encryption used:

- SHA-256
- 100,000 iterations
- 256-bit key size

### Screenshot 1 – Password Prompt

![Encrypted Word Document Prompt](images/01-password-prompt.png)

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

![office2john Output](images/02-office2john-output.png)

---

## Preparing the Hash File

After extraction, the full structured hash was saved to **officehash.txt**.

Maintaining the complete line exactly as generated, including the `$office$` prefix, was necessary for compatibility with cracking tools.

Command used:

```bash
echo '$office$*2013*100000*256*16*...' > officehash.txt
```

### Screenshot 3 – Hash Saved to officehash.txt

![Hash File Creation](images/03-officehash-file.png)

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

![Hashcat Help Output](images/04-hashcat-mode.png)

---

## Cracking the Hash with Hashcat

The Office 2013 hash was attacked using **Hashcat** in mode **9600**.

Command used:

```bash
hashcat -m 9600 officehash.txt /usr/share/wordlists/rockyou.txt
```

This command performed a wordlist attack using the **rockyou.txt** password list.

### Screenshot 5 – Hashcat Cracking Process

![Hashcat Cracking](images/05-hashcat-cracking.png)

---

## Cracking the Hash with John the Ripper

The same hash was also tested with **John the Ripper** using the Office format.

Command used:

```bash
john --format=office officehash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

This provided an alternative verification of the password recovery process.

### Screenshot 6 – John the Ripper Cracking Process

![John the Ripper Output](images/06-john-cracking.png)

---

## Successful Document Decryption

After password recovery, the document was opened successfully using the recovered password. This confirmed that the recovered credential correctly derived the encryption key required to decrypt the document contents.

### Screenshot 7 – Successfully Opened Document

![Decrypted Document](images/07-opened-document.png)

---

## Validation of Findings

Successful decryption validated the following:

- Correct hash extraction
- Proper identification of the encryption type
- Accurate selection of Hashcat mode 9600
- Effective wordlist-based password attack

The examination demonstrated a complete and successful recovery workflow for a Microsoft Office 2013 encrypted document.
