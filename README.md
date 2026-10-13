# Phishing Email Analysis — TryHackMe "Phishing Emails in Action"

**Platform:** [TryHackMe](https://tryhackme.com/room/phishingemails2rytmuv) · **Difficulty:** Easy · **Status:** ✅ [Completed (8/8 tasks)](https://tryhackme.com/room/phishingemails2rytmuv?utm_campaign=social_share&utm_medium=social&utm_content=share-completed-room&utm_source=copy&sharerId=6aa0c90756d5436e0604aef9)
**Focus:** Incident response (first responder), phishing triage, indicator extraction
**Framework mapping:** NIST CSF 2.0 — Detect (DE.AE), Respond (RS.AN), Protect (PR.AT)

---

## Overview

I analysed six real-world phishing emails impersonating PayPal, a parcel courier, Microsoft Outlook, Netflix, Apple and DHL. For each one I examined the sender details, raw HTML source, links and attachments, then identified the technique and a practical mitigation.

All analysis was done from the supplied samples. No live URL or attachment was opened, and every indicator below is **defanged**.

## Triage checklist I used

1. **Header** – display name vs real sender address/domain, To/BCC, urgency in the subject
2. **Body & source** – visible link text vs real `href`, hidden 0×0 images (tracking pixels), branding and grammar
3. **Links** – root domain, URL shorteners, open redirects (expand with a lookup tool, never click)
4. **Attachments** – does the file type make sense? Embedded links, "Enable Editing"/macro lures, payloads
5. **Verdict** – record defanged IOCs, technique and mitigation

## Findings

| Sample | Lure | Key indicators (defanged) | Technique | Mitigation |
|---|---|---|---|---|
| 1 | PayPal order receipt | Button → `is[.]gd` short link | URL shortener hides destination | Expand with WhereGoes; filter shorteners |
| 2 | Parcel tracking | Tracking number text → `devret[.]xyz`; hidden 0×0 image | Link text ≠ href; tracking pixel | Hover before clicking; block remote images |
| 3 | Microsoft Outlook login | Login page on `bdkmotorsport[.]com/wp-duua`; always "invalid credentials" | Credential harvesting on a compromised site | MFA; check the domain before typing a password |
| 4 | Netflix billing | "Netllx billing" from `z99@musacombi[.]online`; link inside a PDF | Display-name spoofing, look-alike spelling | Show full sender address; SPF/DKIM/DMARC |
| 5 | Apple App Store purchase | Sender `@sumpremed[.]com`; victim BCC'd; `.dot` attachment; tumblr redirect → `mansiiea[.]com` | Urgency, mass BCC, open redirect, odd file type | Block risky attachment types; verify via the official app |
| 6 | DHL shipping notice | Sender `info@glamcarcompany[.]de`; `.xlsx` asks to "Enable Editing"; runs `regasms.exe` from AppData | Malware delivery; masquerading as `RegAsm.exe` | Keep Protected View on; disable macros; allow-listing/EDR |

## Key takeaways

- **The display name and branding lied every time; the sending domain gave every sample away.**
- Attackers hide the malicious step one layer deep (PDF, Word template, redirect, spreadsheet) to slip past email scanners.
- The most dangerous sample needed no password at all, only one click on "Enable Editing".

## Why this matters for finance teams

Fake receipts and shipping notices are the same playbook as fake supplier invoices, and a harvested Microsoft 365 login is how most business email compromise starts. Checking the real sender domain, inspecting links and questioning unexpected attachments are simple controls that help prevent payment fraud.

## Next steps

- Complete *Phishing Analysis Fundamentals* and *Phishing Analysis Tools* (header analysis: SPF, DKIM, DMARC)
- Check indicators independently with reputation tools such as VirusTotal
