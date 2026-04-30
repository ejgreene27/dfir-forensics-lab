DFIR Case Report: M57-Jean — Corporate Data Exfiltration
Investigator: Evan Greene
Case: M57-Jean
Date of Analysis: April 13–30, 2026
Status: Complete

Table of Contents

Case Overview
Evidence & Integrity Verification
Lab Environment & Tools
Methodology
Key Findings
Timeline of Events
Conclusion
Investigative Notes & Limitations
Appendix: Commands Reference


1. Case Overview
Subject: Jean (user account: Jean, email: jean@m57.biz)
Organization: M57 (fictional company, NIST forensic dataset)
Dataset: M57-Jean forensic disk image — NIST / Digital Corpora public dataset
Image Files: nps-2008-jean.E01 / nps-2008-jean.E02 (~2.8 GB total)
Summary
This case investigates a suspected insider data exfiltration incident at M57, a small technology startup. A sensitive HR spreadsheet containing employee names, salaries, and Social Security Numbers (SSNs) — m57biz.xls — was found on Jean's desktop, saved at 1:28 AM on a Sunday. Analysis of disk artifacts including browser history, AIM chat logs, and Outlook email revealed that Jean emailed the file to her supervisor Alison (alison@m57.biz) in response to a request sent from a personal Gmail account (tuckgorge@gmail.com). Shortly after, multiple employees reported their personal information had been posted publicly on the internet.
This report documents the full investigative process, tools used, findings, and conclusions drawn from the forensic evidence.

2. Evidence & Integrity Verification
Before any analysis, the forensic image was verified to ensure it had not been altered or corrupted. This step is mandatory in any forensic investigation to establish chain of custody and evidentiary integrity.
Tool: ewfverify
Command:
bashewfverify ~/nps-2008-jean.E01
Result:
MD5 stored in file:       78a52b5bac78f4e711607707ac0e3f93
MD5 calculated over data: 78a52b5bac78f4e711607707ac0e3f93
ewfverify: SUCCESS
Both hashes matched — the image is forensically sound and unmodified. All subsequent analysis was performed on a read-only mount of this verified image.

3. Lab Environment & Tools
Environment
ComponentDetailsHost MachineMacBook Air M3, 16GB RAMVirtualizationUTM (x86_64 emulation)Forensic VMSIFT Workstation 2026.03.24VM StorageSamsung T7 1TB external SSD
Tools Used
ToolPurposeewfverifyForensic image integrity verificationewfmountMount EWF (Expert Witness Format) disk imagemmls (Sleuth Kit)Parse partition table and identify partition offsetsmount (ntfs-3g)Mount NTFS partition read-only at calculated byte offsetfls (Sleuth Kit)Enumerate filesystem files and directories by inodeicat (Sleuth Kit)Extract file contents by inode numberfileIdentify file types from magic bytesstringsExtract human-readable strings from binary filesfindSearch filesystem for files matching path/name patternsgrepSearch file contents for patternsreadpstExtract and convert Outlook PST email archives to mbox formatManual log analysisParse AIM HTML chat logs and browser history SQLite/text files
Why CLI-Based Analysis?
Autopsy 4.21.0 was initially attempted but encountered a fatal JNI library incompatibility on the SIFT Linux environment — the bundled sleuthkit.jar contained only Windows DLLs with no Linux shared object. After attempting to manually compile and inject libtsk_jni.so, persistent JVM crashes (SIGSEGV) led to a decision to pivot to CLI-based analysis using Sleuth Kit tools directly.
This turned out to be a valuable learning experience — CLI tools provide full transparency into every step of the forensic process and are widely used by practitioners in the field.

4. Methodology
4.1 Image Mounting
The EWF image was mounted using ewfmount, which exposes it as a raw block device (ewf1). The partition table was then parsed using mmls to identify the NTFS partition offset:
bashsudo ewfmount ~/nps-2008-jean.E01 ~/mnt/jean/
mmls ~/mnt/jean/ewf1
mmls revealed a DOS partition table with a single NTFS partition starting at sector 63. Converting to a byte offset:
63 sectors × 512 bytes/sector = 32,256 bytes
The partition was then mounted read-only:
bashmkdir -p ~/mnt/ntfs
sudo mount -t ntfs-3g -o ro,offset=$((63*512)) ~/mnt/jean/ewf1 ~/mnt/ntfs/
4.2 Filesystem Enumeration
With the partition mounted, standard Linux tools were used to navigate the filesystem. Key directories examined:

C:\Documents and Settings\Jean\Desktop\
C:\Documents and Settings\Jean\My Documents\
C:\Documents and Settings\Jean\Application Data\
C:\Documents and Settings\Jean\Local Settings\
C:\Documents and Settings\Devon\
C:\RECYCLER\
C:\ (root — for installer logs and system artifacts)

4.3 Artifact Extraction
Files of interest were examined using file for type identification and strings for content extraction. The icat tool was used where direct filesystem access was needed by inode number. readpst was used to convert the Outlook PST database to a searchable mbox format.

5. Key Findings
5.1 m57biz.xls — Sensitive HR Spreadsheet
Location: C:\Documents and Settings\Jean\Desktop\m57biz.xls
Inode: 32712
Identified via: fls filesystem enumeration, extracted with icat
The file is a Microsoft Excel spreadsheet containing sensitive HR and payroll data including employee names, salaries, and Social Security Numbers. Metadata extracted via the file command revealed:

Author: Alison Smith (original creator)
Last Saved By: Jean User
Last Save Timestamp: Sunday, July 20, 2008 at 01:28 AM

The timestamp anomaly — a file saved at 1:28 AM on a Sunday — was the initial indicator of suspicious activity.
5.2 IPH.PH — AIM Installer Log
Location: C:\IPH.PH
This file is the installation log for AOL Instant Messenger (AIM) version 6.8.10.1. Key findings:

AIM was installed on July 18, 2008 at 04:26 AM — in the early morning hours
Installation completed successfully (ed=1 in both Cogsworth sections)
OS confirmed: Windows XP Service Pack 3
Machine ID: AA61E407-122B-4458-B125-BDB2166556FA

The late-night installation timestamp is consistent with the broader pattern of after-hours activity on this machine.
5.3 Browser History — AIM Download & File Access
Browser history artifacts confirmed the following activity:
July 18–19, 2008:

Jean downloaded AIM installer from download.newaol.com/aim/win95/Install_AIM.exe
Jean visited dashboard.aim.com — logged into AIM after installation
General personal browsing: MySpace, eBay, leather backpack shopping

July 20, 2008:

file:///C:/Documents%20and%20Settings/Jean/Desktop/m57biz.xls — Jean opened the spreadsheet directly in Internet Explorer from her Desktop
Immediately after: visits to Microsoft Office update pages, suggesting she may have been checking Excel compatibility

5.4 AIM Chat Logs — Jean and Alison
Location: C:\Documents and Settings\Jean\My Documents\AIMLogger\m57jean\IM Logs\
Jean's screen name: m57jean
Alison's screen name: alisonm57
Two sessions were logged:
July 18, 2008 (6:05–6:13 AM):

Casual work conversation between Jean and Alison
Alison confirms she is Jean's direct supervisor
Jean makes an offhand remark about a "second job" — Alison shuts it down
Discussion of company finances — Jean mentions ~$5K discretionary budget

July 21, 2008 (12:50–1:46 AM):

Jean tells Alison: "just replied" — indicating she sent something via another channel
Jean references an email: "check out whatever I was babbling about on email"
Alison acknowledges: "I'll check it out"
Session continues until 1:46 AM — overlapping with the m57biz.xls save timestamp window

5.5 Outlook PST — Email Exfiltration Confirmed
PST Location: C:\Documents and Settings\Jean\Local Settings\Application Data\Microsoft\Outlook\outlook.pst
Extracted using: readpst
Output: Inbox.mbox, Outbox.mbox, Sent Items.mbox
Analysis of Sent Items.mbox revealed the following critical email:
Subject: RE: Please send me the information now
From: jean@m57.biz
To: alison@m57.biz
Date: Sunday, July 20, 2008 at 01:28:47 UTC
Attachment: m57biz.xls (confirmed — base64 encoded attachment block present)
Body: "I've attached the information that you have requested to this email message."
The original request email from Alison contained a critical anomaly in its headers:
From: alison@m57.biz [mailto:tuckgorge@gmail.com]
Alison sent the data request from a personal Gmail account (tuckgorge@gmail.com) while displaying her work address as the sender. This is a significant finding — the request for sensitive employee data originated from outside the corporate mail infrastructure.
The email chain context (from quoted headers in the reply) shows Alison wrote:

"I'm sorry to bother you, but I really need that information now — this VC guy is being very insistent. Can you please reply to this email with the information I requested — the names, salaries, and social security numbers (SSNs) of all our current employees and intended hires?"

A follow-up email — "RE: Thanks!" sent at 5:07 AM on July 20 — confirms Alison received and acknowledged the file.
5.6 Devon's Profile — No Relevant Activity
Devon's user profile was examined as part of due diligence:

Desktop: empty
Recent files: only Desktop.ini (system file, not user activity)
No AIM logs, no relevant documents found

Devon does not appear to be involved in this incident.
5.7 RECYCLER — Deleted File
RECYCLER SID: S-1-5-21-484763869-796845957-839522115-1004
SID confirmed to Jean's account via strings on NTUSER.DAT
One deleted file was found:
FieldValueRecycle Bin filenameDc1.jpgOriginal pathC:\Documents and Settings\Jean\Desktop\tag-cloud.jpgFile typeJPEG image (JFIF 1.00, 516×373px)
A JPEG image named tag-cloud.jpg was deleted from Jean's Desktop. While not directly tied to the exfiltration, it demonstrates Jean deleted at least one file from her Desktop during this period.

6. Timeline of Events
Date/Time (UTC)EventJul 18, 2008 — 04:26 AMAIM 6.8.10.1 installed on Jean's machineJul 18, 2008 — 06:05 AMJean and Alison chat on AIM — Alison confirms she is Jean's bossJul 18–19, 2008Jean downloads AIM, logs in, browses normally (MySpace, eBay, shopping)Jul 19, 2008 — 11:31 PMJean emails Alison asking "which email address are you using?"Jul 20, 2008 — 01:28 AMJean saves m57biz.xls (Last Saved By: Jean User)Jul 20, 2008 — 01:28 AMJean sends m57biz.xls to alison@m57.biz via email — exfiltration eventJul 20, 2008 — 05:07 AMAlison sends "Thanks!" — confirms receipt of fileJul 20, 2008 — daytimeJean opens m57biz.xls directly in Internet Explorer from DesktopJul 20–21, 2008Employees begin reporting SSNs posted publicly on the internetJul 21, 2008 — 12:50 AMJean and Alison on AIM — Jean references email she just sentJul 21, 2008 — 00:04 AMJean replies to Bob — acknowledges her own SSN and salary are exposed

7. Conclusion
The forensic evidence establishes a clear chain of events: Jean, acting on a request from her supervisor Alison, emailed a sensitive HR spreadsheet (m57biz.xls) containing employee names, salaries, and SSNs to alison@m57.biz at 1:28 AM on July 20, 2008. The file was subsequently exposed publicly, resulting in employee PII being posted on the internet.
Key conclusions:

Exfiltration is confirmed. The email with m57biz.xls attached is present in Jean's Sent Items with a confirmed base64-encoded attachment and explicit body text confirming transmission.
Jean acted on an authoritative request. The email requesting the data was sent by Alison, Jean's direct supervisor, and cited a legitimate-sounding business reason (VC investor). Jean had no obvious reason to refuse.
The request originated from a personal Gmail account. Alison used tuckgorge@gmail.com to send the request while spoofing her work address. This raises the question of whether Alison's work account was compromised, whether she was acting outside of authorized channels, or whether this was intentional to avoid corporate mail logging.
Jean's after-hours activity is suspicious but explainable. The 1:28 AM timestamp is unusual, but Jean and Alison were actively communicating on AIM during the same window, suggesting Jean was awake and working at that hour.
Devon is not involved. No evidence of Devon's participation in any relevant activity was found.

The central unresolved question is whether Alison's use of a personal Gmail was authorized, accidental, or itself part of a social engineering or insider threat scenario. That determination would require investigation of Alison's accounts and devices, which are outside the scope of this disk image.

8. Investigative Notes & Limitations

Autopsy GUI was not used. After extensive troubleshooting of JNI library incompatibilities on SIFT Linux, analysis was conducted entirely via CLI using Sleuth Kit and standard Linux tools. This produced equivalent results with full command-line reproducibility.
No memory dump was available. A RAM capture could have revealed running processes, network connections, and additional artifacts at the time of the incident.
Email content is one-sided. Only Jean's PST was analyzed. Alison's mail server or device was not available for examination.
The tuckgorge@gmail.com account was not investigated. Determining ownership and login history of this account would require legal process (subpoena to Google).
tag-cloud.jpg was not analyzed for content. The deleted JPEG from Jean's Desktop was recovered but its relevance to the incident is unclear without viewing its contents.
This is a public training dataset. The M57-Jean image is a freely available forensic training corpus from NIST/Digital Corpora. All findings reflect the actual contents of the image and were produced through original analysis.


9. Appendix: Commands Reference
Image Verification & Mounting
bash# Verify image integrity
ewfverify ~/nps-2008-jean.E01

# Mount EWF image
sudo ewfmount ~/nps-2008-jean.E01 ~/mnt/jean/

# Parse partition table
mmls ~/mnt/jean/ewf1

# Mount NTFS partition at sector 63 offset
mkdir -p ~/mnt/ntfs
sudo mount -t ntfs-3g -o ro,offset=$((63*512)) ~/mnt/jean/ewf1 ~/mnt/ntfs/
Filesystem Enumeration
bash# List files recursively with full paths
fls -o 63 -r -p ~/nps-2008-jean.E01

# Extract file by inode
icat -o 63 ~/nps-2008-jean.E01 32712 > m57biz.xls

# Search for AIM artifacts
find ~/mnt/ntfs/Documents\ and\ Settings/Jean/ -type d -iname "AIM*"
find ~/mnt/ntfs/Documents\ and\ Settings/Jean/ -type d -iname "AOL*"
Artifact Analysis
bash# Identify file type
file ~/mnt/ntfs/path/to/file

# Extract readable strings from binary
strings ~/mnt/ntfs/path/to/file

# Search file contents
grep -E "^From |^Date:|^To:|^Subject:" ~/cases/M57-Jean/email-export/Sent\ Items.mbox
Email Extraction
bash# Extract PST to mbox format
mkdir -p ~/cases/M57-Jean/email-export
readpst -o ~/cases/M57-Jean/email-export \
  ~/mnt/ntfs/Documents\ and\ Settings/Jean/Local\ Settings/Application\ Data/Microsoft/Outlook/outlook.pst

# View sent items
cat ~/cases/M57-Jean/email-export/Sent\ Items.mbox | less
RECYCLER Analysis
bash# List recycle bin contents
ls ~/mnt/ntfs/RECYCLER/S-1-5-21-484763869-796845957-839522115-1004/

# Recover original filename from INFO2
strings ~/mnt/ntfs/RECYCLER/S-1-5-21-484763869-796845957-839522115-1004/INFO2

# Confirm SID ownership
strings ~/mnt/ntfs/Documents\ and\ Settings/Jean/NTUSER.DAT | grep -i "S-1-5-21-484763869"

Report prepared by Evan Greene as part of a digital forensics portfolio project. Analysis performed on SIFT Workstation using public forensic training data from the NIST / Digital Corpora M57-Jean dataset
