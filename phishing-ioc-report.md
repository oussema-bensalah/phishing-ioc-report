# Phishing URL Forensics — IOC Report

**Analyst:** Oussema Bensalah  
**Date:** June 11, 2026  
**Source:** PhishTank (Submission #9450463)  
**Status:** VALID PHISH — ONLINE  

---

## 1. Executive Summary

A phishing URL was identified and analyzed from PhishTank. The attacker abused Amazon Web Services (AWS) S3 cloud storage to host a credential harvesting page, disguising it under the legitimate `amazonaws.com` domain to evade detection. The phishing page targets German email service users and pre-fills the victim's email address via a URL parameter to increase credibility. As of the analysis date, the page remains active and is not flagged by Google Safe Browsing.

---

## 2. Attack Details

| Field | Value |
|-------|-------|
| Phishing URL | `https://530-verbena-calyx-hook-nr.s3.ca-central-1.amazonaws.com/JRXGRsi1y0c6N?email=redacted@abuse.ionos.com` |
| PhishTank ID | 9450463 |
| Submitted | June 11, 2026 — 7:43 PM UTC |
| Verified by | 4 PhishTank community members (100% phish) |
| File Created | June 9, 2026 |
| File Size | 2,728 bytes |
| Content Type | text/html |

---

## 3. Indicators of Compromise (IOCs)

| Type | Indicator | Details |
|------|-----------|---------|
| URL | `https://530-verbena-calyx-hook-nr.s3.ca-central-1.amazonaws.com/JRXGRsi1y0c6N` | Phishing landing page hosted on AWS S3 |
| IP Address | `3.5.253.254` | AWS serving IP (VirusTotal) |
| IP Address | `52.95.145.24` | AWS serving IP (URLVoid) |
| S3 Bucket | `530-verbena-calyx-hook-nr` | Attacker-controlled S3 bucket, region ca-central-1 |
| File Hash (SHA-256) | `1bc1cc735317692345a9d0b6ba251a4d2764d0184b7a2e5c2ff8f5a2b0c250e9` | Fingerprint of phishing HTML page |
| ASN | AS16509 — Amazon.com Inc. | Hosting infrastructure |

---

## 4. Technical Analysis

### 4.1 Infrastructure Abuse
The attacker created an AWS S3 bucket with a randomly generated name (`530-verbena-calyx-hook-nr`) in the `ca-central-1` (Montreal, Canada) region. By hosting the phishing page on AWS infrastructure, the attacker leverages the trusted reputation of `amazonaws.com` to bypass URL reputation filters and email security gateways.

### 4.2 Cloaking Technique
The phishing page uses fake meta tags to disguise itself from web crawlers and security scanners:

- **Fake title:** *"The 10 Most Popular Email Providers in Germany | Shuttlecloud Blog"*
- **Fake description:** *"Here are the top email providers in Germany..."*

This makes the page appear to be a legitimate blog post to automated scanners. The actual content shown to victims was not directly inspected (safe analysis only), but the combination of email pre-filling, confirmed phishing verdict, and fake metadata strongly indicates a credential harvesting form.

### 4.3 Email Pre-filling
The URL contains a `?email=` parameter that pre-fills the victim's email address into the phishing form. This increases the perceived legitimacy of the page — the victim sees their own email already entered and is more likely to type their password without suspicion.

### 4.4 Redirect Chain
No redirect chain detected. The URL resolves directly to the phishing page with HTTP status 200.

### 4.5 Encryption
AWS server-side encryption (AES256) is applied — this is standard AWS infrastructure encryption and is not attacker-controlled. It does not indicate any special effort by the attacker.

---

## 5. Detection Results

| Tool | Result |
|------|--------|
| PhishTank |  100% Valid Phish — ONLINE |
| VirusTotal |  8/92 vendors flagged as Phishing (BitDefender, ESET, Fortinet, G-Data, Lionic, PhishTank, Sophos, VIPRE) |
| URLVoid |  1/35 engines detected (SURBL) |
| Google Safe Browsing |  Not flagged — no warning shown to victims in browser |

> **Key finding:** Google Safe Browsing has not flagged this URL. Victims visiting this page in Chrome or Firefox will receive no browser warning, making it actively dangerous.

---

## 6. Attack Infrastructure Map

```
Victim (receives phishing email)
        |
        v
Phishing URL (amazonaws.com S3 bucket)
        |
        v
AWS S3 Bucket — ca-central-1 (Montreal, Canada)
IP: 3.5.253.254 / 52.95.145.24
        |
        v
Fake HTML login page (2,728 bytes)
Pre-filled with victim email via ?email= parameter
        |
        v
Credentials harvested → sent to attacker
```

---

## 7. Recommendations

- **Block** the S3 bucket domain `530-verbena-calyx-hook-nr.s3.ca-central-1.amazonaws.com` at the firewall/proxy level
- **Block** IPs `3.5.253.254` and `52.95.145.24` if AWS traffic is not required
- **Import** the SHA-256 hash into EDR/SIEM for file-based detection
- **Report** the S3 bucket to AWS abuse team at `abuse@amazonaws.com` for takedown
- **Educate** users that legitimate-looking domains (amazonaws.com, wixstudio.com) can host phishing pages
- **Monitor** for similar S3 bucket patterns in email links

---

## 8. Tools Used

| Tool | Purpose |
|------|---------|
| PhishTank | Phishing sample source and verification |
| VirusTotal | URL reputation, detections, HTTP response analysis |
| URLVoid | Secondary reputation check, geolocation, ASN |
| Google Safe Browsing | Browser-level detection status |

---

*Report written as part of a SOC Analyst / Threat Analyst portfolio project.*  
*All analysis was performed in a safe environment. No phishing links were visited directly.*
