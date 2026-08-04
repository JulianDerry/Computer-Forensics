# MD5 Hash Recovery

| Field | Value |
|---|---|
| **Case Reference** | Microsoft Office 2013 Password Recovery |
| **Image Source** | HIVE CONSULT |
| **Investigator** | Julian Derry |
| **Date of Analysis** | ‎February ‎16, ‎2026 |
| **Platform** | Kali Linux |
| **Tools** | Hashcat, Hash-Identifier, rockyou.txt |
## Overview

This exercise involved identifying an unknown 32-character hexadecimal hash and recovering its plaintext value using standard password recovery techniques in a Kali Linux environment. The process included hash identification, validation, and a dictionary-based cracking attempt using Hashcat.

---

## Table of Contents

1. [Overview](#overview)
2. [Evidence Handling](#evidence-handling)
3. [Hash Identification](#hash-identification)
4. [Recovery Process (Cracking)](#recovery-process-cracking)
5. [Results](#results)
6. [Tools Used](#tools-used)
7. [Limitations](#limitations)
8. [Conclusion](#conclusion)
9. [Appendix](#appendix)

---

## Evidence Handling

The target hash was saved to a text file for processing.

**Action Performed**

- Echoed hash to `hash.txt`

**Note**

A cryptographic hash verification of the working file was **not performed prior to analysis**. This step was omitted during my early training period before I understood the importance of verifying evidence integrity before examination.

---

## Hash Identification

Before launching a recovery attempt, the hash structure was analyzed using two primary tools in the Kali Linux environment.

### Hashcat Identification

Running `hashcat --identify` against the string returned **11 potential matches**. The top candidates included:

- **MD5** (Mode 0)
- **MD4** (Mode 900)

### Hash-Identifier Verification

`Hash-Identifier` was used for secondary verification and returned a **high-confidence match for MD5**.

<img width="948" height="327" alt="1" src="https://github.com/user-attachments/assets/5e4257ab-8782-4e99-b1a6-7f6022eaad1c" />


<img width="651" height="358" alt="2" src="https://github.com/user-attachments/assets/dfa12942-c85e-4164-8e06-039f0a8e282f" />

---

## Recovery Process (Cracking)

Once the hash was identified as MD5, a dictionary attack was performed using Hashcat.

- **Hash Mode:** 0 (MD5)
- **Wordlist:** `rockyou.txt`

### Command Used

```bash
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
```

The attack completed successfully in approximately **2 seconds**.

<img width="760" height="535" alt="4" src="https://github.com/user-attachments/assets/a466e441-fc96-487e-bbcb-f12c1356c8c8" />

---

## Results

The recovered plaintext value was:

- **beer**

This value corresponded to the supplied MD5 hash:

```text
3e1867f5aee83045775fbe355e6a3ce1
```

---

## Tools Used

- Kali Linux
- Hashcat
- Hash-Identifier
- `rockyou.txt` wordlist

---

## Limitations

- The recovery depended on the presence of the plaintext value within the selected wordlist.
- No brute-force or rule-based attack was required because the password was present in `rockyou.txt`.
- Evidence file integrity verification was not performed prior to analysis.

---

## Conclusion

The supplied hash was successfully identified as **MD5** and recovered through a dictionary-based attack using Hashcat. The plaintext value **beer** was obtained, demonstrating the effectiveness of standard wordlist attacks against weak MD5-protected passwords.

---

## Appendix

### Target Hash

```text
3e1867f5aee83045775fbe355e6a3ce1
```

### Recovered Plaintext

```text
beer
```

### Hashcat Command

```bash
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
```
