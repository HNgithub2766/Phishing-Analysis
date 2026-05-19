# SOC146 - Phishing Mail Detected  - Excel 4.0 Macros 

**Tags:** Phishing | Malware | Excel Macros | Regsvr32 | C2 Communication | SOC Analysis

## Overview
This investigation documents the analysis of a phishing email alert involving a malicious Excel attachment containing Excel 4.0 macros. The alert was generated in the LetsDefend SOC environment after suspicious activity was detected on an endpoint communicating with malicious infrastructure.

## Alert Details 

| Field | Value |
|---|---|
| Event ID | 93 |
| Alert Rule | SOC146 - Phishing Mail Detected - Excel 4.0 Macros |
| Event Time | Jun 13, 2021 - 02:13 PM |
| Severity | High |
| Source Address | trenton@tritowncomputers.com |
| Destination Address | lars@letsdefend.io |
| SMTP IP | 24.213.228.54 |
| Subject | RE: Meeting Notes |
| Device Action | Allowed |


## Tools Used

- LetsDefend SIEM Platform
- Email Security Portal
- Endpoint Detection & Response (EDR)
- VirusTotal
- Sandbox Analysis Environment
- Log Management System

## Initial Analysis 

Step 1: Took ownership of the incident and created a case to begin investigation. 
Step 2: Obtained the information requested in the Playbook, before stating analysis on the incoming email. 
To find this information the email security page was checked on LetsDefend and I searched for the email using the sender address. Here I viewed the email content such as email body and attachments and could answer the below questions. 
- When was it sent? Jun 13, 2021 - 02:13 PM
- What is the email's SMTP address? 
- What is the sender address? 24.213.228.54
- What is the recipient address? lars@letsdefend.io
- Is the mail content suspicious? Yes - due to external sender address, suspicious attachment name and presence of excel 4.0 macros detected in the attachment. 
- Are there any attachment? Yes
  
**Please see Image2 for screenshot of findings. 

## Attachment Analysis:
Step 3: Are there attahcments or URLs in the email? I answered Yes as per the my above findings from the email log. 
Step 4: Analyse the Attachment and determine if it's malicious or non-malicious? I downloaded the attachment and extracted it in a sandbox environment. I found the zip folder contained 3 files; iroto1.dll, iroto.dll, research-1646684671.xls (please see Image4 for details). Next, I uploaded these files to VirusTotal to analyse them - please see Image5 for VirusTotal scan results. Looking at the files in VT I could see the Excel file and DLL payloads were flagged as malicious by multiple security vendors. Thus, I answered Malicious on the Playbook. 

## Mail Delivery and Link Analysis
Step 5: Check if Mail Delivered to user? Yes, this was displayed in the Alert under Device Action = Allowed. 
Step 6: Check if someone opened the Malicious File/URL? Yes it was opened, this was checked using the C2 addresses of the malicious files. As per Image6 we can see that the C2 infrastructure identified in this case includes the following domains and IP addresses. 
Malicious Domains: 
royalpalm.sparkblue.lk
nws.visionconsulting.ro
External IP Addresses:
188.213.19.81
192.232.219.67
These were identified in VirusTotal under the contacted domains and contacted ip addresses of the xls file. The ip addresses were further searched within the endpoint monitoring tool and correlated with the host: LarsPRD. Upon invesitigating the browser history I found the contacted URLs of the malcious macros file and within command history the command `regsvr32.exe -s` was executed to silently register the malicious DLLs. An additional detection opportunity was identified involving Microsoft Excel spawning the `regsvr32.exe` process to register malicious DLL files.

This behaviour is suspicious because:
- Microsoft Excel normally should not spawn `regsvr32.exe`
- Attackers commonly abuse `regsvr32.exe` as a LOLBin (Living Off the Land Binary)
- Excel 4.0 macros can be used to execute malicious commands and load payloads

See Image 7, 8 and 9 for screen captures of these logs. 


## Containment
Step 7: Deleted email from recipient
Step 8: Containtment Outcome - The affected host (LarsPRD) was successfully contained and no lateral movement was observed in the environment.

## Artifacts

### Malicious Domains/URL Addresses
hxxps://royalpalm.sparkblue.lk/vCNhYrq3Yg8/dot.html
hxxps://nws.visionconsulting.ro/N1G1KCXA/dot.html

### MD5 Hashes
8e6fbefcbac2a1967941fa692c82c3ca (malicious dll hash)
e03bde4862d4d93ac2ceed85abf50b18 (malicious dll hash)
b775cd8be83696ca37b2fe00bcb40574 (malicious macro file hash)

### Malicious IP Addresses
188.213.19.81 
192.232.219.67

### Suspicious Processes
`regsvr32.exe`

## Final Analysis Summary

Based on email analysis, attachment detonation, and endpoint investigation, the phishing email was confirmed to contain a malicious Excel 4.0 macro payload. Execution of the attachment resulted in `regsvr32.exe` being spawned from Microsoft Excel, leading to malicious DLL registration and outbound communication to known command-and-control (C2) infrastructure.

Correlation of sandbox results, VirusTotal indicators, and endpoint telemetry confirmed that the payload was successfully executed on the host (LarsPRD). Evidence of external network communication and suspicious process execution further validates malicious activity on the endpoint.

No evidence suggests false positive behavior or benign execution.

---

## Verdict

**True Positive**

The alert is confirmed as a legitimate phishing incident involving a malicious Excel macro attachment that resulted in endpoint compromise and C2 communication.





