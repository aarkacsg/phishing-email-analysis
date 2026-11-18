# Payroll Phishing Campaign Investigation

## Overview

This project documents a simulated Security Operations Center investigation into a payroll-themed phishing campaign targeting users at the fictional company Cloudora.

The investigation combined email header analysis, message-trace telemetry, KQL queries in Azure Data Explorer, and identity sign-in logs to identify campaign infrastructure, affected users, credential submissions, and confirmed account compromise.

The analysis identified two compromised user accounts and linked both to the same attacker-controlled sign-in infrastructure.

## Scenario

A payroll-themed phishing campaign impersonated Cloudora HR and attempted to direct employees to a fake payroll portal.

The investigation focused on determining:

- whether the emails were malicious
- which users received and interacted with them
- whether credentials were submitted
- whether stolen credentials were later used
- which infrastructure was associated with the campaign
- what containment and detection improvements should be recommended

## Tools Used

- Azure Data Explorer
- Kusto Query Language - KQL
- Email header analysis
- Message trace telemetry
- Identity sign-in logs
- MITRE ATT&CK
- Notepad / text editor for raw `.eml` analysis

## Investigation Process

### 1. Email Header Analysis

The first phishing variant impersonated `payroll@cloudora.io`.

Analysis identified:

- SPF - fail
- DKIM - fail
- DMARC - fail
- Reply-To redirected to a lookalike domain
- sending infrastructure associated with `cloudora-hr-portal[.]example`

The second phishing variant passed SPF, DKIM and DMARC because the attacker controlled the lookalike domain.

This demonstrated that successful email authentication does not necessarily mean a message is legitimate.

### 2. URL and Infrastructure Analysis

The campaign used lookalike payroll URLs including:

- `hxxps://cloudora-hr-portal[.]example/payroll/login`
- `hxxps://login[.]cloudora-hr-portal[.]example/verify`

The legitimate Cloudora payroll portal used the `cloudora.io` domain.

The campaign was associated with multiple phishing sender IP addresses and a separate IP used for successful account access.

### 3. False Positive Analysis

A legitimate Cloudora newsletter sent through authorised Mailchimp infrastructure was analysed for comparison.

The message passed SPF, DKIM and DMARC for the legitimate `cloudora.io` domain.

This helped distinguish legitimate authenticated email from attacker-controlled lookalike-domain email.

### 4. Campaign Scoping with KQL

Message-trace data was ingested into Azure Data Explorer and queried using KQL.

The analysis identified:

- two phishing campaign variants
- delivered and quarantined messages
- users who clicked phishing links
- users who submitted credentials
- users who received the email but did not interact with it

### 5. Credential Compromise Investigation

Six users clicked a phishing link.

Two users submitted credentials:

- `freya.lynn@cloudora.io`
- `ryan.boyd@cloudora.io`

Identity sign-in telemetry was then analysed to determine whether the stolen credentials were used.

Both accounts later showed successful authentication from:

`198.18.7.200`

The sign-ins originated from Amsterdam, Netherlands and used Windows 11 with Chrome.

### 6. Attacker Infrastructure Pivot

Pivoting on the attacker IP identified successful access to both compromised accounts.

Observed activity included:

- Microsoft 365
- Outlook Web App
- SharePoint Online

This correlation confirmed that the phishing campaign progressed from credential harvesting to successful account access.

## Key Findings

1. Two related payroll phishing variants targeted Cloudora users.

2. PayrollPhish-A spoofed the legitimate Cloudora domain and failed SPF, DKIM and DMARC.

3. PayrollPhish-B passed email authentication because it used an attacker-controlled lookalike domain.

4. Six users clicked phishing links and two submitted credentials.

5. Freya Lynn and Ryan Boyd were confirmed compromised after successful sign-ins from the same attacker infrastructure.

6. A legitimate Cloudora newsletter was investigated and cleared as a false positive.

## Indicators of Compromise

| Type | Indicator | Context |
|---|---|---|
| Domain | `cloudora-hr-portal[.]example` | Attacker-controlled lookalike payroll domain |
| Subdomain | `login[.]cloudora-hr-portal[.]example` | Credential-harvesting subdomain |
| URL | `hxxps://cloudora-hr-portal[.]example/payroll/login` | Credential-phishing URL |
| URL | `hxxps://login[.]cloudora-hr-portal[.]example/verify` | Credential-phishing URL |
| IP | `198.18.44.10` | PayrollPhish-A sending infrastructure |
| IP | `198.18.44.23` | PayrollPhish-A sending infrastructure |
| IP | `198.18.51.7` | PayrollPhish-B sending infrastructure |
| IP | `198.18.7.200` | Successful attacker sign-in infrastructure |

All indicators are synthetic training data and use reserved infrastructure.

## MITRE ATT&CK Mapping

| Technique ID | Technique | Evidence |
|---|---|---|
| T1566.002 | Phishing - Spearphishing Link | Payroll phishing messages contained credential-harvesting links |
| T1078 | Valid Accounts | Stolen credentials were used successfully to access Microsoft 365 services |

## Containment Recommendations

Recommended response actions include:

- revoke active sessions and refresh tokens for compromised accounts
- force password resets
- require MFA re-registration
- block phishing domains and associated IP addresses
- purge remaining phishing emails
- monitor users who clicked but did not submit credentials
- review subsequent identity logs to verify that malicious access has stopped

## Detection Improvement

A KQL detection was developed to correlate phishing-link clicks with successful sign-ins occurring shortly afterwards.

This helps identify users who may have submitted credentials and then experienced account compromise.

## Incident Report

The full incident report is available in the `incident-report` folder.

## Screenshots

The `screenshots` folder contains investigation evidence including:

- email header analysis
- phishing URL evidence
- legitimate email authentication
- data ingestion validation
- campaign scope
- phishing click analysis
- compromised account timeline
- attacker IP pivot

## Lessons Learned

This investigation reinforced several important SOC concepts:

- email authentication must be interpreted together with domain legitimacy
- phishing investigation should extend beyond the email itself into identity telemetry
- message-trace data can identify exposure and user interaction
- identity logs can confirm whether stolen credentials were actually used
- pivoting on attacker infrastructure can reveal additional compromised accounts
- containment should be verified using follow-up telemetry rather than assumed complete

## Disclaimer

This project is a simulated client engagement completed as part of MyFirstHack cybersecurity training.

Cloudora is a fictional company and all email, user, domain, IP and authentication data is synthetic.
