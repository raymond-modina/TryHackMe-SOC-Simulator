# TryHackMe SOC Simulator — Phishing Alert Investigation & Case Classification

> **SOC Analyst Portfolio Project:** Investigating security alerts, validating indicators, correlating SIEM/firewall evidence, and classifying alerts as **True Positive** or **False Positive**.

## Overview

This project documents a hands-on SOC investigation using the **TryHackMe SOC Simulator**. The objective was to investigate suspicious email and URL alerts, validate the indicators with the provided URL analysis capability, review SIEM/firewall evidence where required, determine whether the alert represented a genuine security incident, and produce a concise case report using the **5Ws**.

The cases are presented in the requested investigation order:

1. **Case 8817** — Microsoft account phishing — **True Positive / Escalate**
2. **Case 8815** — Amazon delivery phishing — **True Positive / No escalation**
3. **Case 8816** — Blacklisted URL blocked by firewall — **True Positive / No escalation**
4. **Case 8814** — Legitimate HR onboarding link — **False Positive**

---

## SOC Investigation Workflow

The investigations followed this general workflow:

```text
Alert
  │
  ├── Review alert details
  │
  ├── Identify sender, recipient, URL/IP and timestamp
  │
  ├── Analyze suspicious URL
  │
  ├── Check firewall / proxy / SIEM evidence
  │
  ├── Determine whether the activity was allowed or blocked
  │
  ├── Classify the alert
  │      ├── True Positive
  │      └── False Positive
  │
  └── Document 5Ws + escalation decision + remediation
```

---

# Case 8817 — Microsoft Account Phishing

### Alert Summary

| Field | Details |
|---|---|
| **Case ID** | 8817 |
| **Alert Rule** | Inbound Email Containing Suspicious External Link |
| **Severity** | Medium |
| **Incident Type** | Phishing |
| **Date & Time** | 4 Oct 2026 12:18:19.628 |
| **Datasource** | Email |
| **Sender** | `no-reply@m1crosoftsupport.co` |
| **Recipient** | `c.allen@thetrydaily.thm` |
| **URL** | `https://m1crosoftsupport.co/login` |

### 1. Alert Details

The alert was triggered by an inbound email containing an external link with suspicious characteristics. The investigation required checking firewall/proxy evidence to determine whether an endpoint attempted to access the URL and whether the connection was allowed or blocked.

The email claimed that an unusual sign-in attempt had been detected on the recipient's Microsoft account and instructed the recipient to secure the account using the supplied link.

![Case 8817 alert details](screenshots/image9.png)

### 2. URL Analysis

The URL was submitted to the TryHackMe URL Analysis Tool.

The analysis determined that:

**`https://m1crosoftsupport.co/login` → MALICIOUS**

![Case 8817 URL analysis](screenshots/image10.png)

### 3. SIEM Investigation

The malicious URL was searched for in **Splunk Enterprise**. The investigation showed that the outbound request was **allowed**.

![Case 8817 Splunk evidence](screenshots/image11.png)

### 4. 5Ws Case Report

**What?**  
Unusual Sign-In Activity on Microsoft Account.

**Who?**  
Recipient: `c.allen@thetrydaily.thm`  
Source IP: `10.20.2.25`  
Source Port: `32653`

**When?**  
4 Oct 2026 12:18:19.628

**Where?**  
Sender: `no-reply@m1crosoftsupport.co`  
Destination IP: `45.148.10.131`  
Destination Port: `443`

**Why?**  
The phishing alert was triggered by an inbound email containing a suspicious external link: `https://m1crosoftsupport.co/login`.

### Escalation

**Escalation required.**

The recipient clicked the malicious link and the firewall **allowed** the outbound request. The source case report states that data may have been breached/exfiltrated or malware may have been delivered.

### Recommended Remediation

- Reset the user's credentials.
- Block the malicious domain/IP.
- Provide user awareness training regarding suspicious links and phishing emails.

### Final Classification

> 🔴 **TRUE POSITIVE — ESCALATE**

---

# Case 8815 — Amazon Delivery Phishing

### Alert Summary

| Field | Details |
|---|---|
| **Case ID** | 8815 |
| **Alert Rule** | Inbound Email Containing Suspicious External Link |
| **Severity** | Medium |
| **Incident Type** | Phishing |
| **Date & Time** | 4 Oct 2026 12:16:01.628 |
| **Datasource** | Email |
| **Sender** | `urgents@amazon.biz` |
| **Recipient** | `h.harris@thetrydaily.thm` |
| **URL** | `http://bit.ly/3sHkX3da12340` |
| **Attachment** | None |

### 1. Alert Details

The email claimed that an Amazon package could not be delivered because of an incomplete address and urged the recipient to click a link to confirm shipping information.

The urgency of the message was a significant phishing indicator.

![Case 8815 alert details](screenshots/image7.png)

### 2. URL Analysis

The same suspicious URL was analyzed with the TryHackMe URL Analysis Tool.

The result was:

**`http://bit.ly/3sHkX3da12340` → MALICIOUS**

![Case 8815 URL analysis](screenshots/image5.png)

### 3. SIEM Investigation

Splunk evidence confirmed that the firewall blocked the recipient's attempted access to the malicious URL.

![Case 8815 Splunk evidence](screenshots/image8.png)

### 4. 5Ws Case Report

**What?**  
Inbound Email Containing Suspicious External Link — Medium Severity. The firewall blocked the recipient when he attempted to access the malicious URL, and no data was compromised or exfiltrated.

**Who?**  
Recipient: `h.harris@thetrydaily.thm`  
Source IP: `10.20.2.17`  
Source Port: `34257`  
Sender: `urgents@amazon.biz`

**When?**  
4 Oct 2026 12:16:01.628

**Where?**  
Firewall and email account of recipient `h.harris@thetrydaily.thm`  
Source IP: `10.20.2.17`  
Destination IP: `67.199.248.11`  
Destination Port: `80`

**Why?**  
Phishing email attempting to persuade the recipient to access a malicious URL.

### Escalation

**No escalation required.**

The firewall blocked the outbound request and the case report states that no data was breached or exfiltrated.

### Recommended Remediation

User awareness training is recommended so the recipient is less likely to click suspicious links in future phishing emails.

### Final Classification

> 🟠 **TRUE POSITIVE — NO ESCALATION**

---

# Case 8816 — Blacklisted URL Blocked by Firewall

### Alert Summary

| Field | Details |
|---|---|
| **Case ID** | 8816 |
| **Alert Rule** | Access to Blacklisted External URL Blocked by Firewall |
| **Severity** | High |
| **Type** | Firewall |
| **Date & Time** | 4 Oct 2026 12:17:15.628 |
| **Action** | Blocked |
| **Source IP** | `10.20.2.17` |
| **Source Port** | `34257` |
| **Destination IP** | `67.199.248.11` |
| **Destination Port** | `80` |
| **URL** | `http://bit.ly/3sHkX3da12340` |
| **Application** | web-browsing |
| **Protocol** | TCP |
| **Rule** | Blocked Websites |

### 1. Alert Details

The alert was triggered when a user attempted to access an external URL listed in the organization's blacklist or threat intelligence feeds.

The firewall successfully blocked the outbound request.

![Case 8816 alert details](screenshots/image4.png)

### 2. URL Analysis

The URL was analyzed using the TryHackMe URL Analysis Tool.

The result was:

**`http://bit.ly/3sHkX3da12340` → MALICIOUS**

![Case 8816 URL analysis](screenshots/image5.png)

### 3. SIEM Investigation

Splunk Enterprise was checked to confirm whether access to the malicious URL was blocked.

The investigation confirmed that access was blocked.

![Case 8816 Splunk evidence](screenshots/image6.png)

### 4. 5Ws Case Report

**What?**  
Access to a Blacklisted External URL Blocked by Firewall — High Severity. The user attempted to access a URL listed in the organization's blacklist/threat intelligence feeds, but the firewall blocked the request.

**Who?**  
Recipient: `h.harris@thetrydaily.thm`  
Source IP: `10.20.2.17`  
Source Port: `34257`

**When?**  
4 Oct 2026 12:17:15.628

**Where?**  
Destination IP: `67.199.248.11`  
Destination Port: `80`  
Sender: `urgents@amazon.biz`

**Why?**  
The alert was triggered because the user attempted to access an external URL listed as malicious/blacklisted.

### Escalation

**No escalation required.**

The firewall blocked the outbound request and the source case report states that no data was breached/exfiltrated and no malware was delivered.

### Recommended Remediation

**No remediation required.**

### Final Classification

> 🟠 **TRUE POSITIVE — NO ESCALATION**

---

# Case 8814 — Legitimate HR Onboarding Link

### Alert Summary

| Field | Details |
|---|---|
| **Case ID** | 8814 |
| **Alert Rule** | Inbound Email Containing Suspicious External Link |
| **Severity** | Medium |
| **Type** | Phishing |
| **Date & Time** | 4 Oct 2026 at 12:14 |
| **Sender** | `onboarding@hrconnex.thm` |
| **Recipient** | `j.garcia@thetrydaily.thm` |
| **URL** | `https://hrconnex.thm/onboarding/15400654060/j.garcia` |

### 1. Alert Details

The alert was triggered by an inbound email containing an external link with potentially suspicious characteristics.

The email subject was:

**Action Required – Finalize Your Onboarding Profile**

The message instructed Ms. Garcia to complete her final onboarding profile process.

![Case 8814 alert queue](screenshots/image1.png)

![Case 8814 alert details](screenshots/image2.png)

### 2. URL Analysis

The link was checked using the TryHackMe Secure File and URL Analysis Tool.

The result was:

**`https://hrconnex.thm/onboarding/15400654060/j.garcia` → CLEAN**

![Case 8814 URL analysis](screenshots/image3.png)

Based on the source case investigation, the alert was classified as a false positive and there was no requirement to continue to SIEM investigation based on the clean URL result.

### 3. 5Ws Case Report

**What?**  
Phishing alert triggered by an inbound email containing an external link.

**Who?**  
Recipient: Ms. Garcia  
Sender: HR Onboarding Team

**When?**  
4 Oct 2026 at 15:15

**Where?**  
Email account: `j.garcia@thetrydaily.thm`

**Why?**  
Final onboarding process for Ms. Garcia.

### Final Classification

> 🟢 **FALSE POSITIVE**

---

# Investigation Summary

| Case | Alert / Incident | Classification | Key Finding | Escalation |
|---|---|---|---|---|
| **8817** | Microsoft account phishing | 🔴 True Positive | Malicious URL was **allowed** by the firewall | **Required** |
| **8815** | Amazon delivery phishing | 🟠 True Positive | Malicious URL was **blocked** | **Not required** |
| **8816** | Blacklisted URL access | 🟠 True Positive | Firewall blocked the blacklisted URL | **Not required** |
| **8814** | HR onboarding link | 🟢 False Positive | URL analysis returned **clean** | **Not required** |

---

## Key SOC Skills Demonstrated

- Alert triage
- Phishing investigation
- True Positive / False Positive classification
- URL and indicator analysis
- SIEM investigation using Splunk Enterprise
- Firewall event analysis
- Source/destination IP investigation
- Timestamp correlation
- Incident escalation decisions
- 5W case documentation
- Remediation recommendations
- User security awareness considerations

## Investigation Takeaways

### 1. A suspicious alert is not automatically a confirmed incident

The alert needs to be validated against additional evidence. Case 8814 demonstrates how a suspicious-link alert can ultimately be classified as a **False Positive** after URL analysis.

### 2. Blocking a malicious URL changes the response decision

Cases 8815 and 8816 show malicious activity being detected and blocked. The investigation therefore determined that escalation was not required according to the source case reports.

### 3. Allowed malicious traffic requires immediate attention

Case 8817 is the most serious scenario in this project because the recipient clicked the malicious link and the firewall allowed the outbound request. This changes the investigation from simple detection to potential compromise assessment.

### 4. Documentation is part of SOC operations

Each case was documented using the **5Ws**, followed by an escalation decision and recommended remediation where applicable.

---

## Tools & Technologies

- **TryHackMe SOC Simulator**
- **TryHackMe URL / IP Analysis Tool**
- **Splunk Enterprise**
- **Firewall / Proxy Logs**
- **SIEM Investigation**
- **Phishing Analysis**

---

## Project Objective

The purpose of this project is to demonstrate a practical SOC workflow:

**Detect → Validate → Investigate → Correlate → Classify → Escalate → Remediate → Document**

This repository is intended as a cybersecurity portfolio project demonstrating practical exposure to security alert triage and SOC investigation methodology.

---

## Disclaimer

This project is based on the TryHackMe SOC Simulator exercise and the investigation evidence documented in the accompanying case report. The findings, classifications, and remediation decisions in this README reflect the source material.
