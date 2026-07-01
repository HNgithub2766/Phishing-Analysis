# SOC282 - Phishing Alert: Deceptive Mail Detected

## Alert Summary

| Field | Details |
|---|---|
| **Event ID** | 257 |
| **Event Time** | May 13, 2024, 09:22 AM |
| **Rule** | SOC282 - Phishing Alert - Deceptive Mail Detected |
| **Level** | Security Analyst |
| **SMTP Address** | 103.80.134.63 |
| **Source Address** | free@coffeeshooop.com |
| **Destination Address** | Felix@letsdefend.io |
| **Email Subject** | Free Coffee Voucher |
| **Device Action** | Allowed |

## Incident Overview

On May 13, 2024, a phishing email was sent from external IP address `103.80.134.63` to internal user **Felix** (`172.16.20.151`). The email, disguised as a "Free Coffee Voucher," was allowed through the email gateway and delivered to the user's inbox.

## Investigation

Analysis of Felix's endpoint confirmed the phishing attempt was successful:

- The malicious URL embedded in the email was **visited** by the user.
- This resulted in the execution of a highly malicious process: **`Coffee.exe`**.
- Additional **suspicious commands** were identified on the endpoint, indicating further post-execution activity consistent with compromise.

Given the domain `coffeeshooop.com` bears no legitimate business relevance and closely mimics a trusted brand name (typosquatting), this was assessed as a deliberate phishing/malware delivery campaign.

## Indicators of Compromise (IOCs)

| Type | Indicator |
|---|---|
| Sending Mail Server IP | 103.80.134.63 |
| Sender Email | free@coffeeshooop.com |
| Malicious Domain | coffeeshooop.com |
| Malicious File | Coffee.exe |
| Affected Host | 172.16.20.151 (Felix) |

## Containment Actions Taken

- The phishing email was **deleted** from the user's mailbox.
- The affected endpoint was **quarantined**.

## Recommendations

1. **Block the domain** `coffeeshooop.com` at the email gateway — it serves no legitimate business purpose internally or externally and poses continued phishing risk.
2. **Block `Coffee.exe`** by its SHA256 hash within the EDR platform to prevent execution on other endpoints across the environment.
3. **Block the malicious URL** at the network perimeter (firewall/proxy) to prevent any further callback or compromise attempts.
4. Consider **user awareness training** reinforcing caution with unsolicited "free offer" style phishing lures.



---
*Report prepared as part of SOC282 alert triage and incident documentation.*
