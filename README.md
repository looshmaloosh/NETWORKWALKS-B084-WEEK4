# NETWORKWALKS-B084-WEEK4
This project is a black-box penetration test on a target that has given written permission to identify vulnerabilities in their system that can lead to critical data exposure.


# NetworkWalks Cybersecurity Internship (B084)
# Week 4 – Final Penetration Testing Project

**Client:** Mediroza General Hospital  
**Assessment Type:** Black-Box Penetration Testing & Vulnerability Assessment  
**Duration:** 5 Days  
**Testing Environment:** Kali Linux  
**Authorisation:** Written permission provided through NetworkWalks

---

## 1. Executive Summary

This report documents the final penetration testing assessment conducted as part of the NetworkWalks Cybersecurity Internship Program (B084).

The objective was to evaluate the security of the Mediroza General Hospital web application, identify vulnerabilities, demonstrate their potential impact and recommend appropriate remediation measures.

The assessment involved network reconnaissance, directory enumeration, web application testing, SQL injection exploitation, PDF password recovery and sensitive information exposure analysis.

### Key Findings

The assessment identified several security weaknesses:

- **SQL Injection Authentication Bypass:** A vulnerability in the patient login mechanism allowed authentication to be bypassed without valid account credentials.
- **Exposed Database Backup:** A publicly accessible SQL backup exposed confidential staff information, salary records and shareholder details.
- **Directory Indexing:** Public directory listings revealed internal application files and resources.
- **Weak PDF Password Protection:** All three encrypted patient PDF reports were successfully recovered through dictionary-based password attacks.
- **Verbose Database Error Disclosure:** The patient login revealed detailed MySQL syntax errors to unauthenticated users.

The most significant findings were the authentication bypass and exposed SQL database backup, both of which demonstrated serious weaknesses in protecting confidential information.

### Overall Risk Assessment

**Overall Risk: High**

The combination of authentication weaknesses and sensitive information exposure presents a significant confidentiality risk.

Successful exploitation demonstrated unauthorised access to patient report resources and exposure of confidential employee and organisational information.

Immediate remediation of the authentication vulnerability and publicly exposed database backup is recommended.

## 2. Scope and Methodology

### 2.1 Scope of Assessment

The engagement involved an authorised black-box penetration test against the Mediroza General Hospital web application.

Testing was conducted without access to the application's source code, internal architecture or administrator credentials.

The assessment consisted of four milestones:

| Milestone | Objective | Result |
|---|---|---|
| M1 – Initial Access | Retrieve three encrypted patient PDF reports | Completed |
| M2 – Data Extraction | Recover the contents of all three PDFs | Completed |
| M3 – Critical Data Exposure | Identify staff salaries and shareholder details | Completed |
| M4 – Reporting | Document findings, evidence, risks and remediation | Completed |

### 2.2 Rules of Engagement

Testing was limited to the authorised target domain.
The following restrictions applied:

- No denial-of-service or disruptive testing.
- No social engineering.
- No testing outside the agreed scope.
- No intentional modification or destruction of server data.
- Testing was conducted for educational and authorised security assessment purposes.

### 2.3 Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux | Penetration testing environment |
| WHOIS / Nslookup | Domain and DNS reconnaissance |
| DNSRecon | DNS record enumeration |
| Nmap | Network service and version detection |
| WhatWeb | Web application fingerprinting |
| Wafw00f | Web Application Firewall identification |
| cURL | HTTP response header inspection |
| DirBuster | Web directory enumeration |
| Burp Suite | HTTP request and response analysis |
| sqlmap | Automated SQL injection testing |
| pdf2john | PDF password hash extraction |
| John the Ripper | Dictionary-based password recovery |
| Hashcat | Alternative password-auditing tool |

### 2.4 Reconnaissance

#### Domain and DNS Enumeration

WHOIS and Nslookup were used to identify domain registration information, hosting infrastructure and the target's resolved IP address.

DNSRecon revealed publicly available DNS records, including A, NS, MX, TXT and SRV records.

The scan also reported that DNSSEC was not configured and that the domain's DMARC policy was set to `p=none`.

These observations were documented as security hardening opportunities rather than confirmed exploitable vulnerabilities.


#### Network Service Enumeration

Nmap was used to identify exposed network services associated with the target's resolved IP address.

The scan identified FTP, SMTP, DNS, HTTP, HTTPS, POP3 and IMAP services.

Service fingerprinting identified technologies including Pure-FTPd, Exim, Dovecot, HAProxy and LiteSpeed.

The server appeared to use shared hosting infrastructure, so these findings were treated as infrastructure observations rather than proof of vulnerabilities belonging exclusively to the hospital.

#### Web Application Fingerprinting

WhatWeb and cURL identified LiteSpeed as the web server technology.

Wafw00f also identified a LiteSpeed-associated Web Application Firewall.

Although the web server technology was identified, the exact LiteSpeed version was not disclosed, preventing confirmation of version-specific vulnerabilities.


#### Directory Enumeration

Directory enumeration was conducted to identify unlinked resources and potentially exposed application directories.

Two notable directories were discovered:

- `/patient/` – Exposed internal application filenames and directory structure.
- `/old/` – Contained a publicly accessible SQL database backup.

The findings indicated that directory indexing was enabled and that sensitive files were not consistently protected from public access.

These discoveries were investigated further during the exploitation and data exposure milestones.

### 2.5 Testing Limitations

The assessment was conducted externally without access to the application's source code or internal database administration.

Some automated tools, including sqlmap, did not successfully identify vulnerabilities later demonstrated through manual testing.

The engagement excluded denial-of-service testing, social engineering and activities outside the authorised domain.

Consequently, the findings represent vulnerabilities demonstrated within the permitted testing scope rather than a complete assessment of all underlying hosting infrastructure.
