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


## 3. Reconnaissance and Enumeration

Reconnaissance was conducted to identify publicly available information, exposed network services, web technologies and potentially accessible application resources associated with Mediroza General Hospital.

Both passive and active reconnaissance techniques were used to understand the target's external attack surface before conducting vulnerability testing.

### 3.1 WHOIS Enumeration

WHOIS was used to identify the target domain's registration information and associated nameservers.

The enumeration identified the following:

- **Domain:** medirozahospital.com
- **Registrar:** Namecheap, Inc.
- **Registration Date:** 14 August 2026
- **Expiration Date:** 14 August 2027
- **Nameservers:** dns1.namecheaphosting.com and dns2.namecheaphosting.com
- **DNSSEC Status:** Unsigned

The results provided background information about the domain's registration and DNS infrastructure.


### 3.2 DNS Lookup Using Nslookup

Nslookup was used to resolve the target domain to its associated IPv4 address.

The domain resolved to:

`199.188.201.16`

This information was used during subsequent network reconnaissance.



### 3.3 DNS Enumeration Using DNSRecon

DNSRecon was used to enumerate publicly available DNS records associated with the target domain.

The scan identified:

- A records identifying the website's IPv4 address.
- NS records identifying the authoritative nameservers.
- MX records associated with the domain's email services.
- TXT records containing SPF and DMARC configuration.
- SRV records advertising calendar, contact and email-related services.

Two notable security observations were identified:

**DNSSEC:** DNSRecon reported that DNSSEC was not configured.

**DMARC:** The domain's published DMARC policy was configured as `p=none`, indicating a monitoring-only policy rather than requesting quarantine or rejection of failing messages.

These observations were documented as potential security hardening opportunities rather than confirmed exploitable vulnerabilities.


### 3.4 Network Enumeration Using Nmap

Nmap was used to identify exposed network services and attempt service version detection.

The initial scan was performed using:

`nmap -sV 199.188.201.16`

The scan identified several open TCP ports associated with the target's resolved hosting IP address.

| Port | Service | Identified Technology |
|---|---|---|
| 21 | FTP | Pure-FTPd |
| 26 | SMTP | Exim 4.100.1 |
| 53 | DNS | BIND |
| 80 | HTTP | HAProxy / LiteSpeed |
| 110 | POP3 | Dovecot |
| 143 | IMAP | Dovecot |
| 443 | HTTPS | HAProxy / LiteSpeed |
| 465 | SMTPS | Exim 4.100.1 |
| 587 | SMTP | Exim 4.100.1 |
| 993 | IMAPS | Dovecot |
| 995 | POP3S | Dovecot |

The scan also identified the hostname `server274-1.web-hosting.com`, suggesting that the IP address was associated with shared hosting infrastructure.

Although multiple services were exposed, their presence did not independently establish exploitable vulnerabilities.


Further inspection of the FTP service identified Pure-FTPd with anonymous authentication disabled and TLS support advertised.

A separate MySQL service check reported TCP port 3306 as filtered, preventing direct database version identification.

These results were recorded as reconnaissance observations rather than confirmed vulnerabilities.

### 3.5 Web Application Fingerprinting Using WhatWeb

WhatWeb was used to identify the technologies associated with the target web application.

The scan identified LiteSpeed as the web server technology.

The initial HTTP request returned a `301 Moved Permanently` response, redirecting traffic to HTTPS.

The HTTPS request returned a `403 Forbidden` response during the WhatWeb scan.

However, the exact LiteSpeed version was not disclosed, preventing reliable identification of version-specific vulnerabilities.


### 3.6 WAF Identification Using Wafw00f

Wafw00f was used to determine whether the target was protected by a Web Application Firewall (WAF).

The tool identified a LiteSpeed-associated WAF.

This indicated that an application-layer security mechanism was potentially present.

However, the presence of a WAF did not prevent the SQL injection authentication bypass demonstrated later in the assessment.


### 3.7 HTTP Header Inspection Using cURL

cURL was used to inspect the HTTP response headers returned by the target website.

The command used was:

`curl -I https://medirozahospital.com`

The server returned:

`HTTP/2 200`

The response headers also identified:

- **Server:** LiteSpeed
- **Content-Type:** text/html
- **Content-Length:** 5445
- **Accept-Ranges:** bytes
- **X-Turbo-Charged-By:** LiteSpeed

These findings confirmed the web server technology previously identified through WhatWeb.

No exact LiteSpeed version was disclosed.

![HTTP Header Inspection](screenshots/curl.png)


DirBuster was used to identify unlinked directories and potentially exposed application resources.

The enumeration identified several directories returning HTTP 301 redirects.

Further manual inspection revealed two important directories:

**1. `/old/`**

The directory had indexing enabled and exposed a historical SQL database backup:

`mediroza_db_backup_2019.sql`

The backup was accessible without authentication and contained confidential staff and shareholder information.

**2. `/patient/`**

The patient directory also had indexing enabled, revealing resources such as:

- `login.php`
- `portal.php`
- `download.php`
- `logout.php`
- `reports/`
- `error_log`

Direct access to the reports directory and error log returned HTTP 403 responses during testing.

However, the directory listing exposed the application's file structure and helped identify the authentication and document download endpoints.


### 3.9 Reconnaissance Summary

The reconnaissance phase identified several important characteristics of the target environment.

| Tool | Key Finding |
|---|---|
| WHOIS | Domain registration and nameserver information |
| Nslookup | Target domain resolved to 199.188.201.16 |
| DNSRecon | DNS records, missing DNSSEC and monitoring-only DMARC policy |
| Nmap | Exposed network services and hosting infrastructure |
| WhatWeb | LiteSpeed web server identified |
| Wafw00f | LiteSpeed-associated WAF detected |
| cURL | HTTP response headers and server identification |
| DirBuster | Exposed directory listings and a publicly accessible SQL backup |

The most significant reconnaissance findings were the discovery of the `/old/` database backup and the exposed `/patient/` directory structure.

These discoveries provided the foundation for the subsequent vulnerability assessment, including SQL injection authentication bypass, encrypted PDF recovery and sensitive database information exposure.

Further details of these vulnerabilities and their demonstrated impact are presented in the following section.

