# Cybersecurity Fundamentals — Threat Vectors, Attacks & Vulnerabilities

This documentation covers the core security concepts commonly tested in MCQ-based IT Manager / AAI exams: **threat vectors, cyberattack types, vulnerabilities, security controls, and exam-oriented distinctions**. A **threat vector** (also called an attack vector) is the path or method an attacker uses to enter a system or network; a **vulnerability** is a weakness that can be exploited; and an **attack** is the actual malicious action. [cloudflare](https://www.cloudflare.com/learning/security/glossary/attack-vector/)

## Core Security Concepts

### CIA Triad

The CIA Triad is the foundational model for information security.

| Concept | Meaning | Example |
|---|---|---|
| **Confidentiality** | Only authorized people can access data | Encrypting customer payment-card data |
| **Integrity** | Data is accurate and not unauthorized-modified | Digital signatures on an order record |
| **Availability** | Systems and data are accessible when needed | DDoS protection for an e-commerce checkout service |

Related principles:

- **Authentication:** Verifies *who* a user or system is, e.g., username/password, MFA, biometrics.
- **Authorization:** Determines *what* an authenticated user may do, e.g., a customer can view only their own orders.
- **Accounting / Auditing:** Records who did what, when, and from where; supports accountability and investigations.
- **Non-repudiation:** Prevents a party from denying an action, typically through digital signatures or audit logs.
- **Least privilege:** Users and processes receive only the minimum permissions required.
- **Defense in depth:** Multiple overlapping controls—network, endpoint, application, identity, monitoring—rather than one single control.

### Threat, Vulnerability, Risk, Exploit

These terms are frequently confused in MCQs.

| Term | Definition | MCQ cue |
|---|---|---|
| **Asset** | Anything valuable: data, server, application, employee, reputation | “What is being protected?” |
| **Threat** | Potential cause of harm | Hacker, insider, malware, natural disaster |
| **Threat actor** | Entity that may cause harm | Cybercriminal, nation-state, insider |
| **Vulnerability** | Weakness in a system, process, control, or implementation that can be exploited | Unpatched software, weak password policy, SQL injection flaw  [csrc.nist](https://csrc.nist.gov/glossary/term/vulnerability) |
| **Exploit** | Code, technique, or tool used to take advantage of a vulnerability | Malicious SQL payload |
| **Risk** | Likelihood that a threat exploits a vulnerability, causing impact | Risk = Threat × Vulnerability × Impact |
| **Impact** | Damage if an incident occurs | Data breach, downtime, financial loss |
| **Countermeasure / control** | Action or safeguard that reduces risk | Patching, MFA, firewall, backup |

**Exam shortcut:**  
- Threat = *who/what could harm you*  
- Vulnerability = *weakness*  
- Exploit = *method/tool used*  
- Risk = *probability and impact*

## Threat Vectors

A threat vector is the **route of entry** into an environment. Common vectors include email, web applications, removable media, compromised credentials, insider access, supply chains, and vulnerable network services. [cloudflare](https://www.cloudflare.com/learning/security/glossary/attack-vector/)

### Major Threat Vectors

| Threat vector | How it works | Example |
|---|---|---|
| **Email / phishing** | Malicious link, attachment, or impersonation | Fake invoice attachment installs malware |
| **Web application** | Exploits input handling, authentication, or configuration flaws | SQL injection through a product-search field |
| **Removable media** | Infected USB drive or peripheral introduces malicious code | Malware spreads from an infected USB device  [nvlpubs.nist](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-61r2.pdf) |
| **Credential theft** | Stolen or guessed username/password grants access | Password reuse after a third-party breach |
| **Social engineering** | Manipulates people rather than systems | Attacker poses as IT support to obtain an OTP |
| **Insider threat** | Authorized person misuses access intentionally or accidentally | Employee downloads customer database before resignation |
| **Supply chain** | Compromise of vendor, library, update, or third-party service | Malicious dependency inserted into software build |
| **Remote access / VPN** | Exploits weak remote-access configuration or credentials | Unpatched VPN appliance exploited |
| **Cloud misconfiguration** | Publicly exposed storage, excessive permissions, weak identity controls | Publicly accessible cloud storage bucket |
| **Wireless network** | Rogue access point, weak Wi-Fi encryption, evil twin | Attacker creates a fake airport Wi-Fi hotspot |
| **Physical access** | Direct access to devices, offices, or network ports | Attacker plugs a device into an unprotected network port |
| **Mobile device** | Malicious app, insecure Wi-Fi, lost device | Employee installs a fake productivity app |
| **IoT device** | Default credentials, unpatched firmware, weak network isolation | Smart camera becomes a botnet node |

NIST classifies common incident attack vectors as **external/removable media, attrition, web, email, impersonation, improper usage, loss/theft of equipment, and other/unclassified**. [nvlpubs.nist](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-61r2.pdf)

## Types of Cyberattacks

### Malware-Based Attacks

**Malware** is malicious software designed to damage, disrupt, or gain unauthorized access to systems. [cisa](https://www.cisa.gov/topics/cyber-threats-and-response/malware-phishing-and-ransomware)

| Malware type | Behavior | Key exam distinction |
|---|---|---|
| **Virus** | Attaches to a file and requires user action to spread | Needs a host file |
| **Worm** | Self-replicates across networks without a host file | Spreads automatically |
| **Trojan** | Disguises itself as legitimate software | Does not self-replicate |
| **Ransomware** | Encrypts files or locks systems and demands payment | Extortion-based malware  [cisa](https://www.cisa.gov/topics/cyber-threats-and-response/malware-phishing-and-ransomware) |
| **Spyware** | Secretly collects information | Focuses on surveillance/data theft |
| **Keylogger** | Records keystrokes | Captures passwords, card numbers, messages |
| **Adware** | Displays unwanted advertisements | May also track behavior |
| **Rootkit** | Hides malicious activity and maintains deep system access | Difficult to detect; may survive reinstall |
| **Bot / botnet** | Infected device controlled remotely | Many bots form a botnet |
| **Cryptominer / cryptojacking** | Uses victim resources to mine cryptocurrency | Causes performance degradation |
| **Fileless malware** | Runs in memory or abuses legitimate tools | Leaves fewer disk artifacts |

### Social Engineering Attacks

Social engineering targets **human trust, urgency, fear, authority, or curiosity**.

| Attack | Description |
|---|---|
| **Phishing** | Mass fraudulent email designed to steal credentials or deliver malware |
| **Spear phishing** | Targeted phishing aimed at a specific person or organization |
| **Whaling** | Phishing targeting senior executives or high-value individuals |
| **Vishing** | Voice-based phishing, such as fake bank or IT-support calls |
| **Smishing** | SMS-based phishing |
| **BEC** | Business Email Compromise; attacker impersonates an executive, vendor, or colleague to redirect payments or data |
| **Pretexting** | Attacker invents a scenario to obtain information |
| **Baiting** | Uses curiosity or greed, e.g., infected USB labeled “Salary Details” |
| **Tailgating / piggybacking** | Following an authorized person through a secure door |
| **Shoulder surfing** | Observing passwords, PINs, or confidential screens |
| **Dumpster diving** | Searching discarded material for sensitive information |
| **Watering-hole attack** | Compromising a website frequently visited by the target group |
| **Quid pro quo** | Offering a service or benefit in exchange for information/access |

**Exam tip:** Phishing is broad; spear phishing is targeted; whaling targets executives; vishing uses voice; smishing uses SMS.

### Network Attacks

| Attack | Description | Prevention |
|---|---|---|
| **DoS** | One source floods a target to make it unavailable | Rate limiting, filtering |
| **DDoS** | Many compromised systems flood a target simultaneously | CDN, scrubbing service, rate limiting, Anycast |
| **MITM** | Attacker intercepts or alters communication between two parties | TLS, certificate validation, VPN |
| **ARP spoofing** | Attacker sends fake ARP messages to intercept LAN traffic | Dynamic ARP inspection, port security |
| **DNS spoofing / poisoning** | Corrupts DNS records to redirect users | DNSSEC, trusted resolvers |
| **DNS tunneling** | Encodes malicious traffic inside DNS queries | DNS monitoring and filtering |
| **Port scanning** | Identifies open ports and services | Firewalls, intrusion prevention |
| **Packet sniffing** | Captures network traffic | Encryption, switched networks |
| **Session hijacking** | Steals or predicts a session token | Secure cookies, TLS, token expiration |
| **IP spoofing** | Forges source IP address | Ingress/egress filtering |
| **Rogue access point** | Unauthorized Wi-Fi access point | Wireless intrusion detection |
| **Evil twin** | Fake Wi-Fi hotspot mimicking a legitimate one | Certificate validation, VPN |
| **Bluejacking** | Sends unsolicited Bluetooth messages | Disable Bluetooth when unnecessary |
| **Bluesnarfing** | Unauthorized Bluetooth access to data | Disable discoverability, patch devices |

### Application Attacks

These attacks exploit flaws in web or software applications. OWASP’s current Top 10 highlights broken access control, security misconfiguration, software supply-chain failures, cryptographic failures, injection, insecure design, authentication failures, integrity failures, logging/alerting failures, and mishandling of exceptional conditions. [top10.owasp](https://top10.owasp.org/2025/)

| Attack | Description | Example |
|---|---|---|
| **SQL injection** | Malicious SQL input manipulates a database query | `' OR 1=1 --` bypasses authentication |
| **XSS** | Injects malicious scripts into pages viewed by other users | Script steals a customer session cookie |
| **CSRF** | Tricks an authenticated browser into sending an unintended request | Forced money transfer or profile change |
| **SSRF** | Tricks server into requesting internal or external resources | Access to internal metadata or admin service |
| **Command injection** | Untrusted input is executed as an OS command | Input appended to a server-side shell command |
| **Path traversal** | Accesses files outside the intended directory | `../../etc/passwd` |
| **Insecure deserialization** | Malicious serialized data leads to code execution or manipulation | Tampered session/order object |
| **Broken access control** | Users access data or functions beyond their permission | Customer edits another customer’s order ID |
| **Authentication failure** | Weak login, session, or credential handling | No MFA, weak password reset |
| **Security misconfiguration** | Insecure defaults, exposed debug endpoints, excessive permissions | Public admin console or verbose error messages |
| **Cryptographic failure** | Weak, missing, or incorrect encryption | Passwords stored in plain text |
| **XXE** | Exploits XML external entity processing | Reads local files or performs SSRF |
| **Race condition / TOCTOU** | Exploits timing between checking and using a resource | Double-spending or duplicate order creation |
| **Zero-day exploit** | Exploits a vulnerability before a patch is available | Unknown browser or VPN flaw |

### Identity and Credential Attacks

| Attack | Description |
|---|---|
| **Brute force** | Tries every possible password combination |
| **Dictionary attack** | Tries common words and known password lists |
| **Password spraying** | Tries a few common passwords across many accounts |
| **Credential stuffing** | Reuses leaked username/password pairs across sites |
| **Pass-the-hash** | Uses stolen password hashes to authenticate without knowing the plaintext password |
| **Pass-the-ticket** | Reuses stolen Kerberos tickets |
| **Kerberoasting** | Requests service tickets and cracks their encryption offline |
| **Golden Ticket** | Forged Kerberos Ticket-Granting Ticket using compromised domain credentials |
| **Privilege escalation** | Gains higher permissions than authorized |
| **Account takeover** | Gains control of a legitimate user account |
| **MFA fatigue** | Repeated MFA prompts pressure the user into approving |
| **SIM swapping** | Transfers victim phone number to attacker-controlled SIM to intercept OTPs |

### Availability and Integrity Attacks

| Attack | Goal |
|---|---|
| **Ransomware** | Deny access to data and extort payment |
| **DDoS** | Disrupt service availability |
| **Data destruction / wiper malware** | Permanently delete or corrupt data |
| **Defacement** | Alter website content to damage reputation |
| **Data manipulation** | Secretly modify records, transactions, or reports |
| **Supply-chain compromise** | Insert malicious code into trusted software or updates |
| **Cryptojacking** | Consume computing resources for attacker profit |

## Common Vulnerabilities

A vulnerability is a weakness in software, hardware, procedure, internal control, or implementation that a threat source can exploit. [csrc.nist](https://csrc.nist.gov/glossary/term/vulnerability)

### Technical Vulnerabilities

- **Unpatched software:** Known flaws remain exploitable until patched.
- **Default or weak credentials:** `admin/admin`, factory passwords, shared accounts.
- **Misconfigured services:** Open database ports, exposed admin panels, permissive firewall rules.
- **Weak encryption:** Outdated algorithms, hardcoded keys, missing TLS.
- **Insecure APIs:** Missing authentication, excessive data exposure, unrestricted resource access.
- **Input-validation flaws:** Enable SQL injection, XSS, command injection, and path traversal.
- **Broken authorization:** IDOR, missing role checks, insecure direct object references.
- **Outdated dependencies:** Vulnerable third-party libraries and frameworks.
- **Insecure deserialization:** Untrusted serialized objects accepted without validation.
- **Insufficient logging:** Incidents cannot be detected, investigated, or reconstructed.
- **Lack of backups / untested recovery:** Ransomware or corruption becomes catastrophic.
- **Weak endpoint protection:** No EDR, outdated antivirus, disabled host firewall.
- **Poor patch management:** No inventory, testing, or deployment process.
- **Excessive privileges:** Users, service accounts, or containers have more access than needed.
- **Shadow IT:** Unauthorized apps, cloud services, or devices outside security oversight.

### Human and Process Vulnerabilities

- Lack of security awareness training.
- Weak password reuse and poor credential hygiene.
- No MFA for email, VPN, privileged accounts, or cloud consoles.
- Inadequate vendor and third-party risk management.
- Poor change management and unreviewed code deployments.
- No segregation of duties for sensitive actions such as payments or production changes.
- Missing incident-response plan, escalation matrix, or tabletop exercises.
- Unclassified data and inconsistent data-retention practices.

## OWASP Top 10 — Exam Reference

The OWASP Top 10 is a widely used awareness document for critical web application security risks; the current released version is **OWASP Top 10:2025**. [top10.owasp](https://top10.owasp.org/2025/)

| Rank | Risk | Meaning |
|---|---|---|
| A01 | **Broken Access Control** | Users can perform actions or access data outside their permissions |
| A02 | **Security Misconfiguration** | Insecure defaults, exposed features, excessive permissions |
| A03 | **Software Supply Chain Failures** | Compromised dependencies, build tools, or update mechanisms |
| A04 | **Cryptographic Failures** | Sensitive data inadequately protected by cryptography |
| A05 | **Injection** | Untrusted input interpreted as code or query |
| A06 | **Insecure Design** | Missing security controls in architecture or business logic |
| A07 | **Authentication Failures** | Weak login, session, credential, or identity management |
| A08 | **Software or Data Integrity Failures** | Unverified updates, pipelines, or critical data |
| A09 | **Security Logging and Alerting Failures** | Insufficient detection, logging, or incident response |
| A10 | **Mishandling of Exceptional Conditions** | Poor error handling exposing sensitive information or unsafe behavior |

## Security Controls and Mitigations

### Preventive Controls

Stop incidents before they occur.

- Patch and vulnerability management.
- MFA and strong password policy.
- Least privilege and role-based access control.
- Firewalls, WAF, IDS/IPS, EDR/XDR.
- Encryption at rest and in transit.
- Secure coding, code review, SAST/DAST/SCA.
- Network segmentation and zero trust.
- Secure configuration baselines.
- Vendor and supply-chain security reviews.

### Detective Controls

Identify incidents during or after occurrence.

- SIEM and centralized logging.
- IDS/IPS alerts.
- File-integrity monitoring.
- Vulnerability scanning.
- Anomalous-login and privilege-escalation detection.
- Data-loss prevention monitoring.
- Regular penetration testing and red-team exercises.

### Corrective Controls

Limit damage and restore operations.

- Incident-response plan.
- Backup and restore procedures.
- Patching after incident containment.
- Account lockout and credential reset.
- System rebuild from known-good images.
- Forensic investigation and root-cause analysis.

### Administrative Controls

Govern people and processes.

- Security policies and standards.
- Security awareness training.
- Background checks.
- Acceptable-use policy.
- Change management.
- Risk assessments.
- Business continuity and disaster-recovery planning.
- Third-party risk management.

### Physical Controls

Protect facilities and hardware.

- Locks, badges, CCTV, security guards.
- Mantraps and access-control vestibules.
- Environmental controls: fire suppression, HVAC, power protection.
- Secure disposal and media sanitization.

## Important Security Frameworks and Terms

| Term | Meaning |
|---|---|
| **CVE** | Common Vulnerabilities and Exposures; public identifier for a known vulnerability |
| **CVSS** | Common Vulnerability Scoring System; severity score for vulnerabilities |
| **CWE** | Common Weakness Enumeration; catalog of software weakness types |
| **Zero-day** | Vulnerability exploited before a vendor patch exists |
| **Patch** | Vendor fix for a vulnerability |
| **Hardening** | Reducing attack surface by removing unnecessary services and tightening configuration |
| **Attack surface** | All points where an unauthorized user could interact with a system |
| **Attack tree** | Diagram of possible paths to achieve an attack objective |
| **Kill chain** | Stages of an intrusion: reconnaissance, weaponization, delivery, exploitation, installation, command and control, actions on objectives |
| **MITRE ATT&CK** | Knowledge base of adversary tactics and techniques |
| **IOC** | Indicator of Compromise; evidence that a system may be breached |
| **IOA** | Indicator of Attack; behavior suggesting an attack is in progress |
| **TTP** | Tactics, Techniques, and Procedures used by threat actors |
| **APT** | Advanced Persistent Threat; long-term, well-resourced, targeted intrusion |
| **Insider threat** | Risk from employees, contractors, or partners with legitimate access |
| **Zero trust** | “Never trust, always verify”; continuous authentication and least-privilege access |
| **Threat intelligence** | Information about existing or emerging threats and threat actors |

## Incident Response Lifecycle

A standard incident-response process includes:

1. **Preparation:** Policies, tools, training, contact lists, playbooks.
2. **Detection and analysis:** Identify alerts, scope, severity, affected systems, and indicators.
3. **Containment:** Isolate hosts, disable accounts, block IPs, segment networks.
4. **Eradication:** Remove malware, close vulnerabilities, revoke compromised credentials.
5. **Recovery:** Restore clean systems, validate integrity, resume operations.
6. **Lessons learned:** Improve controls, documentation, monitoring, and training.

NIST’s incident-handling guidance explicitly associates incidents with common attack vectors such as removable media, attrition, web, email, impersonation, improper usage, and equipment loss or theft. [nvlpubs.nist](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-61r2.pdf)

## MCQ Traps and Quick Comparisons

| Confusing pair | Correct distinction |
|---|---|
| **Threat vs vulnerability** | Threat is a potential danger; vulnerability is a weakness |
| **Risk vs threat** | Risk includes likelihood and impact; threat is only a potential source of harm |
| **Virus vs worm** | Virus needs a host/user action; worm self-propagates |
| **Phishing vs spear phishing** | Phishing is broad; spear phishing is targeted |
| **DoS vs DDoS** | DoS is one source; DDoS uses many sources |
| **Authentication vs authorization** | Authentication proves identity; authorization grants permissions |
| **Encryption vs hashing** | Encryption is reversible with a key; hashing is one-way |
| **IDS vs IPS** | IDS detects and alerts; IPS actively blocks |
| **Symmetric vs asymmetric encryption** | Symmetric uses one shared key; asymmetric uses public/private key pair |
| **XSS vs CSRF** | XSS executes malicious script in a user’s browser; CSRF tricks an authenticated browser into sending a request |
| **SQL injection vs XSS** | SQL injection targets the database/server; XSS targets the user’s browser |
| **Zero-day vs known vulnerability** | Zero-day has no available patch at time of exploitation |
| **Insider threat vs external attacker** | Insider has legitimate access; external attacker does not |
| **CIA vs AAA** | CIA is confidentiality, integrity, availability; AAA is authentication, authorization, accounting |

## High-Yield One-Liners

- **Threat vector:** Path used to gain unauthorized access. [cloudflare](https://www.cloudflare.com/learning/security/glossary/attack-vector/)
- **Vulnerability:** Exploitable weakness. [csrc.nist](https://csrc.nist.gov/glossary/term/vulnerability)
- **Exploit:** Technique or code that uses a vulnerability.
- **Risk:** Likelihood of a threat exploiting a vulnerability times impact.
- **Malware:** Malicious software, including viruses, worms, trojans, ransomware, spyware, and rootkits. [cisa](https://www.cisa.gov/topics/cyber-threats-and-response/malware-phishing-and-ransomware)
- **Phishing:** Deceptive communication used to steal information or deliver malware. [cisa](https://www.cisa.gov/topics/cyber-threats-and-response/malware-phishing-and-ransomware)
- **Ransomware:** Malware that encrypts or locks data and demands payment. [cisa](https://www.cisa.gov/topics/cyber-threats-and-response/malware-phishing-and-ransomware)
- **DDoS:** Distributed attempt to exhaust resources and deny service. [geeksforgeeks](https://www.geeksforgeeks.org/cybersecurity/types-of-cyber-attacks/)
- **MITM:** Secret interception or alteration of communications. [geeksforgeeks](https://www.geeksforgeeks.org/cybersecurity/types-of-cyber-attacks/)
- **SQL injection:** Untrusted input alters a database query.
- **XSS:** Malicious script executes in another user’s browser.
- **Broken access control:** Users access resources or actions beyond their authorization. [top10.owasp](https://top10.owasp.org/2025/)
- **Zero trust:** Continuously verify identity, device, and context before granting access.
- **Defense in depth:** Use multiple security layers so one failure does not cause total compromise.
