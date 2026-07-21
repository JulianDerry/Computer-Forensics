# Adinkra Microfinance Ltd
## USB Digital Forensics Investigation

![Cover](images/cover.png)

---

# Executive Summary

Adinkra Microfinance Ltd (Accra) suspected that a staff accountant, Kwabena Asare, who 
worked on workstation FIN-WS-07, had been job-hunting on company time and had moved 
a confidential client list out of the organization ahead of resigning to start a competing 
practice. A USB flash drive linked to the subject was seized and imaged for examination. 
Analysis of that image found browser, document, and receipt evidence consistent with the 
allegation: the subject searched for new jobs and for how to export company data, 
uploaded a client spreadsheet and a QuickBooks backup to a personal Dropbox account, 
emailed himself a backup link, registered a competing business, purchased a personal 
laptop, and drafted a resignation letter, all within a five-day window (15 to 20 September 
2023). No evidence of anti-forensic activity, such as timestamp manipulation, was found. An 
apparent date/time inconsistency identified during initial review was traced to a display 
limitation in the examination tool rather than to the underlying evidence, and is 
documented in Finding 5.10. 

---

# Scope & Objectives

Scope: one USB flash drive image (HIVE-2026-BF-0117-USB.E01), FAT32 volume 
KASARE_USB, containing a preserved Google Chrome profile and personal 
documents, receipts, and images belonging to the subject.

● Objectives: reconstruct the subject's web browsing activity; determine whether that 
activity is corroborated by on-disk documents and receipts; establish whether data 
was exfiltrated and by what channel; build a timeline of the subject's actions 
documents, receipts, and images belonging to the subject. 
between 15 and 20 September 2023. 

● Out of scope: no $MFT, $LOGFILE, $USNJRNL, or Windows registry hives were 
available, the volume is FAT32, not NTFS, so these artifacts do not exist on this drive. 
No live system or memory analysis was performed, and no network-level evidence 
was examined. 

---

# Evidence
Note: this is a synthetic training exhibit, not real custody evidence. The hash values above were independently recalculated against the supplied image and match the values recorded at acquisition, confirming the file examined is unaltered.

## Evidence Acquisition

| Item # | Description | Source | Hash Values | Date Acquired | Acquired By |
|:------:|-------------|--------|-------------|---------------|-------------|
| 1 | **HIVE-2026-BF-0117-USB.E01** | Synthetic training exhibit, HIVE CONSULT | **Container (SHA-256):** `00B16EF7B2E3707C09C0F97B56285F653A33011545A45813450BC6F89A131A07`<br>**Media (MD5):** `f1336b78c7b9771d0f1c6c78c116b479`<br>**Media (SHA-1):** `6f3aba0cb15869372dd1311674e9a353e7ec641a` | 14 Jul 2026, 10:11:18 | HIVE CONSULT |

<img width="1612" height="272" alt="Screenshot 2026-07-18 145502" src="https://github.com/user-attachments/assets/502045c0-b607-4f9e-8243-b65521c6434c" /> Independent hash verification of the container file.


---

# Methodology
●	Verify container integrity against the published MD5/SHA1 (media) and SHA256 (container) hashes before analysis.

●	Mount the E01 (ewfmount) and confirm the partition and filesystem layout (mmls, fsstat).

●	Triage the Chrome profile in Autopsy (Web History, Login Data, Downloads) to reconstruct browsing activity.

●	Where Autopsy's raw SQLite table view produced anomalous timestamps, extract the History database directly (icat) and re-derive timestamps using the WebKit epoch formula, cross-checked against a purpose-written SQL query.

●	Correlate browser artifacts against on-disk documents, receipts, and screenshots recovered from the same image.

●	Build a consolidated timeline and validate each finding against at least one independent artifact.

## Tools Used

- Autopsy 4.22.1
- Sleuth Kit
- sqlite3
- Python
- DCode

---

# Finding 5.1 – Last Known Activity

The final webpage visited was Formula 1 Latest News.

Evidence: Web History: title “Latest News”, URL https://www.formula1.com/en/latest.html, timestamp 2023-09-19 09:58:08 UTC. 

Source path: /Users/kasare/AppData/Local/Google/Chrome/User Data/Default/History.

<img width="1918" height="1029" alt="Screenshot 2026-07-18 152216" src="https://github.com/user-attachments/assets/d6904e24-c418-468b-bc4c-377d1fbdde2e" /> History file location and metadata. 

Interpretation: This is the final entry in the subject's browsing history and marks the end of the reconstructed session on 19 September 2023.


---

# Finding 5.2 – Exit Plan Browsing Pattern
Observation: Five search terms in the history indicate the subject was researching new employment and independently starting a competing accounting practice; leisure searches were excluded.

<img width="1910" height="1029" alt="Screenshot 2026-07-18 154546" src="https://github.com/user-attachments/assets/b4b610b1-67ab-4084-bd63-8c0cb5b4fae0" /> Search-term evidence from Web History. 

| URL | Page Title |
|------|------------|
| `google.com/search?q=starting+an+accounting+firm+in+ghana` | Starting an Accounting Firm in Ghana - Google Search |
| `jobberman.com.gh/jobs?q=accountant` | Accountant Jobs in Ghana \| Jobberman |
| `jobberman.com.gh/` | Find Jobs in Ghana \| Jobberman Ghana |
| `linkedin.com/jobs/` | Jobs \| LinkedIn |
| `google.com/search?q=forensic+accounting+jobs+accra` | Forensic Accounting Jobs Accra - Google Search |

Interpretation: The combination of job-search terms and a business-formation search shows deliberate research into both leaving the company and competing with it, not incidental browsing.

---

# Finding 5.3 – Personal cloud storage and initial access (Dropbox Upload)
Dropbox was the personal cloud service used by the subject, first accessed 2023-06-20 09:09:05 UTC and visited eight times in total.

Evidence: Attribution is established two ways: the URL directory pattern (dropbox.com/upload) and the saved Chrome credential for kwabena.asare.gh@gmail.com against dropbox.com/login. The corrected first-access timestamp is independently corroborated by the Dropbox Plus payment receipt, billing date 20 Jun 2023.

<img width="1917" height="1031" alt="Screenshot 2026-07-18 154934" src="https://github.com/user-attachments/assets/cba04240-bfec-404a-87cd-36cf165e5a0b" /> Url Data: Dropbox upload directory attribution. 

<img width="1918" height="1028" alt="Screenshot 2026-07-18 155601" src="https://github.com/user-attachments/assets/308291f5-bc1e-4da9-9317-412519cd3ce1" /> Login Data: Dropbox credential attribution. 

<img width="1497" height="662" alt="Screenshot 2026-07-18 164407" src="https://github.com/user-attachments/assets/5ab15ff3-d5d7-44f5-8007-328201056e8e" /> Dropbox visit count from Web History.


Interpretation: Dropbox access predates the exfiltration event by roughly three months, consistent with routine personal use of the service before it was repurposed as an exfiltration channel on 18 September 2023

---

# Finding 5.4 – Typed vs Link Navigation (Intent versus accident )
Of the two job-search sites in the history, jobberman.com.gh was reached by direct address entry (typed_count = 1); linkedin.com/jobs was reached only via a search-result link.

Evidence: Web History transition data for both URLs.

<img width="1917" height="1031" alt="Screenshot 2026-07-18 154934" src="https://github.com/user-attachments/assets/731db97f-ea39-4c3d-9480-4075a4f9d48d" /> Typed vs. link visit evidence.

Interpretation: A ‘Typed’ visit requires the user to know and enter the address directly, indicating prior intent to visit that specific site. A ‘Link’ visit can result from incidental browsing and carries less evidential weight on its own.

---

# Finding 5.5 – eBay Session Reconstruction

The subject searched eBay for a laptop and viewed a specific listing: a Lenovo ThinkPad P50s at £589.99.

Evidence: Web History (corrected): search “laptop for sale” at 2023-09-19 09:55:37 UTC, item view at 2023-09-19 09:55:43 UTC, reached via referrer from the search six seconds earlier.

<img width="1032" height="60" alt="db2" src="https://github.com/user-attachments/assets/fa7d4214-b643-412b-886d-279f794f9d99" /> Corrected UTC timestamps for the eBay rows.

Interpretation: The date/time originally reported here during initial review (10/22, 14:56:40 and 16:36:40) came from Autopsy's raw SQLite table viewer, which does not decode Chrome's WebKit epoch correctly. Re-querying the History database directly confirms the question's stated date, 19 September 2023, is correct: there is no date discrepancy. See Finding 5.10 for the full explanation.

---

# Finding 5.6 – Exfiltrated data: Client Spreadsheet
A spreadsheet of customer data, Adinkra_Client_Master_List.xlsx, was recovered in the Client_Data folder on the USB drive.

Evidence: File location: /Personal/Client_Data/Adinkra_Client_Master_List.xlsx. Its presence is corroborated by the QuickBooks-export search (Finding 5.2 window) and by the Dropbox upload page visited at 2023-09-18 14:33:20 UTC.

<img width="1916" height="1032" alt="Screenshot 2026-07-19 124644" src="https://github.com/user-attachments/assets/fcfba753-0e6a-4a41-8ec7-568cbf451e3e" /> Client_Data folder listing on the USB drive.

<img width="1919" height="1030" alt="Screenshot 2026-07-19 135751" src="https://github.com/user-attachments/assets/21c1bb33-6953-4db9-b662-357c9540e50a" /> Dropbox upload confirmation screenshot.


Interpretation: The existence of this file on the subject's personal USB drive, combined with the export search and the Dropbox upload activity, is consistent with data having been moved from the workstation FIN-WS-07 to both a personal cloud account and this drive.

---

# Finding 5.7 – Purchase Confirmation
The ThinkPad viewed on eBay (Finding 5.5) was purchased the same session.

Evidence: Receipt_eBay_ThinkPad_P50s.png, order #12-11834-95027, ordered 19 Sep 2023 09:56 UTC. Item total GBP 634.19 with import charges, GBP 589.99 without, matching the listing viewed in Finding 5.5.

<img width="820" height="1120" alt="Receipt_eBay_ThinkPad_P50s" src="https://github.com/user-attachments/assets/82bbf3ed-dcaa-4b61-a341-ccb5c6286ac6" /> <br> eBay order confirmation receipt.

Interpretation: The receipt timestamp sits six to nineteen seconds after the corrected browser timestamps for the same item, consistent with a genuine search-to-purchase flow and inconsistent with any timestamp manipulation.

---

# Finding 5.8 – Documented intent to leave
Three separate documents on the drive show concrete steps toward resignation and starting a competing practice.

(1) Receipt_RGD_Business_Registration.png and RGD_Business_Name_Application.pdf, business name registration filed 18 September 2023, corroborated by the “starting an accounting firm in ghana” search the same day.

<img width="1916" height="1005" alt="Screenshot 2026-07-19 143848" src="https://github.com/user-attachments/assets/634a3537-f7f8-44da-a8b0-8dfe7117b2c6" />

(2) Sankofa_Accounting_Business_Plan.pdf, a business plan for the registered entity, SANKOFA ACCOUNTING & ADVISORY. 

<img width="1919" height="1019" alt="Screenshot 2026-07-19 143919" src="https://github.com/user-attachments/assets/4eec4089-e739-45ef-a8a4-c0f2e085e292" />

(3) Resignation_Letter_DRAFT.pdf, drafted 20 September 2023, two days after the registration filing, not yet sent, with an effective resignation date of 6 October 2023.

<img width="1919" height="1026" alt="Screenshot 2026-07-19 143839" src="https://github.com/user-attachments/assets/ff7c3fe7-18fa-4085-b483-a1c3516cc982" />

Interpretation: The email, the Dropbox upload screenshot (Finding 5.6), and the Web History entry for dropbox.com/upload are three independent artifacts describing the same event, sent seven minutes after the upload page was visited, which is strong corroboration that the upload was deliberate and that the subject intended to resign shortly afterward.

---

# Finding 5.9 – Exfiltration channel: Self-Addressed Email
A self-addressed email shows how the exfiltrated data left the organisation and ties directly to the Dropbox activity in the browser history.

Evidence: Email_Dropbox_ShareLink.pdf, sent from and to kwabena.asare.gh@gmail.com on 18 September 2023 at 14:41:07 UTC, subject “backup”, sharing a Dropbox link to Client_Master_List_Adinkra.xlsx and noting that AdinkraMFI_Q3_Backup.qbb was also uploaded to the same folder. The email states: “Keeping a copy off the work machine before I hand in notice.”

Interpretation: The email, the Dropbox upload screenshot (Finding 5.6), and the Web History entry for dropbox.com/upload are three independent artifacts describing the same event, sent seven minutes after the upload page was visited, which is strong corroboration that the upload was deliberate and that the subject intended to resign shortly afterward.


---

# Finding 5.10 – Timestamp Investigation (tool limitation, not evidence tampering)
An apparent date/time inconsistency identified during initial review (affecting the Dropbox first-access date and the eBay session in Findings 5.3 and 5.5) was investigated and resolved.

Evidence: Chrome/WebKit stores browser timestamps as microseconds since 1601-01-01, not the Unix epoch. Autopsy's raw SQLite table viewer (File Metadata > Application > Table: urls) does not decode this correctly and renders a garbled six-digit year. Re-extracting the History database directly and decoding it with the query datetime(last_visit_time/1000000-11644473600,'unixepoch') produces timestamps that match the vendor receipts to within seconds.


Interpretation: The discrepancy originated in the examination tool's raw table view, not in the underlying evidence. No time-stomping or anti-forensic activity is indicated; browser activity and receipts are tightly self-consistent throughout the case.

---

# Examiner's Concerns
Most of the evidence in this case was straightforward. A handful of items were not, and each one is documented here rather than dismissed. The principle applied throughout this examination is that an unanswered question about the evidence is not automatically a sign of tampering, but left unexamined, it becomes a legitimate line of cross-examination in any adversarial proceeding. Every item below was investigated to a documented conclusion, whether that conclusion is a resolution or an acknowledged open question.

## File Size Mismatch
the file sizes shown in the Dropbox upload screenshot do not match the sizes of the corresponding files recovered from the USB drive: Client_Master_List_Adinkra.xlsx is shown as 471 KB in the screenshot versus 7,057 bytes (6.9 KB) recovered; AdinkraMFI_Q3_Backup.qbb is shown as 18.0 MB versus 3,145,853 bytes (3.0 MB) recovered.

This is roughly an order of magnitude apart on both files and deserves an explanation rather than dismissal. No definitive cause was established from the artifacts available; it is documented here for follow-up with the exhibit's originator rather than assumed to be immaterial.

...

## Filename Inconsistency
The file recovered from the USB drive is named Adinkra_Client_Master_List.xlsx. The Dropbox upload screenshot and the self-addressed email both reference Client_Master_List_Adinkra.xlsx, the same two words in reversed order. Combined with the size mismatch in 6.1, this warranted further verification.

Verification attempted: hash comparison, the most reliable way to confirm whether two files are identical regardless of naming, could not be performed here, because only the USB-resident file has recoverable byte content. The Dropbox screenshot is an image of a UI, not a file, so there is nothing on that side to hash. The recovered file's hashes are recorded for the file that does exist: Adinkra_Client_Master_List.xlsx, MD5 40f8e5106e0f5ee96b00015070303552, SHA256 22f76a716b123aad7bf39a857456ef58ad0fa7497d39361e1df83837e1771f47.

A naming difference alone does not change a file's hash, hashes depend only on byte content, so the reversed word order by itself proves nothing. However, the size mismatch in 6.1 means these two references cannot be byte-identical regardless of name. This reinforces a general principle rather than resolving this specific case: hashes are the reliable identity check when both files are recoverable; filenames and screenshot-reported sizes are not, and should not be treated as authoritative on their own.

...

## FAT32 vs NTFS
The USB drive is formatted FAT32 (confirmed in Section 2), meaning artifacts such as $MFT, $LOGFILE, $USNJRNL, and Windows registry hives do not exist on this volume.

This is an expected property of the filesystem, not evidence of tampering or an examination gap. Understanding the underlying filesystem before analysis prevents an investigator from chasing artifacts that were never going to be there.

...

## Evidence Labels
The eBay laptop delivery photo (Finding 5.7) is watermarked “FIN-WS-07 - evidence photo”, the same identifier as the workstation under investigation. Read literally, this appears to be a timeline contradiction: how could the workstation's identifier appear on a personal item purchased after the fact?

Verification: every other document and image in the exhibit set carries a similar boilerplate stamp identifying the case, not just this one photo.

The label is most likely an evidence-tracking identifier applied consistently across the synthetic training exhibits, not a literal claim that the ThinkPad and the workstation are the same machine. It is documented here rather than silently corrected, since the underlying convention has not been confirmed directly with the exhibit's originator.

...

## Verify Your Tools
Autopsy's raw SQLite table viewer displayed Chrome's last_visit_time as an unusable six-digit year (see Finding 5.10 for the full technical explanation).

Verification: rather than treating the displayed date as authoritative, the raw History database was extracted and decoded independently using the WebKit epoch formula. A peer independently loaded the same evidence in a separate session and observed the identical rendering issue, supporting that this is a reproducible tool limitation rather than a one-off configuration problem.

This was a display bug in the examination tool, not a defect in the evidence. The exhibit's creator was also contacted and corroborated the observation.
Examiner's note: good forensic work is not just about finding evidence, it is about validating every observation, testing the assumptions behind it, and making sure each conclusion can withstand scrutiny. That is what keeps an unresolved anomaly from becoming an opening for the defense.



---

# Timeline
The timeline below interleaves corrected Web History timestamps with on-disk document and receipt dates. All timestamps are UTC.

Note: the ThinkPad was delivered 3 October 2023 (IMG_20231003_ThinkPad_delivered.jpg), within the eBay receipt's estimated delivery window of 2 to 9 October 2023. This falls outside the 15 to 20 September window above but is included for completeness, as it closes the purchase-to-delivery chain.
## Investigation Timeline

| Timestamp (UTC) | Event | Source Artifact |
|-----------------|-------|-----------------|
| **2023-09-15 10:05:30** | Google search: *"forensic accounting jobs accra"* | Web History |
| **2023-09-15 10:06:10** | `linkedin.com/jobs` visited (via Google search result) | Web History |
| **2023-09-15 10:18:45** | `jobberman.com.gh/` visited (typed directly) | Web History |
| **2023-09-15 10:22:30** | `jobberman.com.gh/jobs?q=accountant` visited | Web History |
| **2023-09-18 14:10:40** | Google search: *"how to export quickbooks company data"* | Web History |
| **2023-09-18 14:30:05** | `dropbox.com/home` revisited and user logged in | Web History |
| **2023-09-18 14:33:20** | `dropbox.com/upload` visited | Web History |
| **2023-09-18 ≈14:34** | Dropbox upload confirmed: `Client_Master_List_Adinkra.xlsx` and `AdinkraMFI_Q3_Backup.qbb` uploaded to `/Personal/ByKA` | `Screenshot_Dropbox_Upload_ClientList.png` |
| **2023-09-18 14:41:07** | Self-addressed email sent with subject **"backup"**, containing the Dropbox link and the statement *"before I hand in notice"* | `Email_Dropbox_ShareLink.pdf` |
| **2023-09-18 14:55:00** | Google search: *"starting an accounting firm in ghana"* | Web History |
| **2023-09-18 (time not recorded)** | RGD business name registration filed for **SANKOFA ACCOUNTING & ADVISORY** (Ref: `RGD/BN/2023/0091273`), **GHS 100** paid | `Receipt_RGD_Business_Registration.png`<br>`RGD_Business_Name_Application.pdf` |
| **2023-09-19 09:55:37** | eBay search: *"laptop for sale"* | Web History |
| **2023-09-19 09:55:43** | eBay item viewed: **Lenovo ThinkPad P50s** (£589.99) | Web History |
| **2023-09-19 09:55:52** | Gmail accessed | Web History |
| **2023-09-19 09:55:58** | Outlook Live visited | Web History |
| **2023-09-19 ≈09:56** | eBay order placed (Order **#12-11834-95027**) for **GBP 634.19** | `Receipt_eBay_ThinkPad_P50s.png` |
| **2023-09-19 09:56:24** | `dropbox.com/` and `dropbox.com/home` revisited | Web History |
| **2023-09-19 09:57:23–09:58:08** | Leisure browsing: Netflix, IMDb, Google, and Formula 1. Final recorded page: **Latest News** at **09:58:08** | Web History |
| **2023-09-20 (time not recorded)** | Resignation letter drafted (not sent), effective resignation date **6 Oct 2023** | `Resignation_Letter_DRAFT.pdf` |

---

# Conclusion
The evidence confirms that the subject conducted job-search and business-formation research on company time, registered a competing business, and drafted a resignation letter within the reviewed window. Data exfiltration is confirmed to a personal Dropbox account and to the USB drive itself: the client spreadsheet and QuickBooks backup are recoverable in both locations, and the self-addressed email, upload screenshot, and Web History entry corroborate one another independently. The apparent timestamp discrepancy identified during initial review is resolved and attributable to a display limitation in Autopsy's raw SQLite table viewer, not to evidence tampering; browser activity, login data, and receipts are consistent with each other throughout to within seconds. Of the five items examined in Section 6, three are resolved (the FAT32 filesystem scope, the FIN-WS-07 evidence label, and the Autopsy timestamp display bug); two remain open and would benefit from clarification with the exhibit's originator: the file-size mismatch and the filename-order inconsistency between the Dropbox-side records and the USB-resident file, for which hash comparison could not be performed because only one side of the comparison has recoverable byte content.

