# Cybersecurity Fundamentals — Advanced Exam Notes

Cybersecurity protects the **confidentiality, integrity, and availability** of airport, airline, passenger, and payment systems against threat actors, attack vectors, vulnerabilities, and operational failures. Core frameworks include the **CIA triad**, **NIST Cybersecurity Framework**, **OWASP Top 10**, and **MITRE ATT&CK**; NIST CSF’s core functions are Identify, Protect, Detect, Respond, and Recover. [nist](https://www.nist.gov/blogs/taking-measure/identify-protect-detect-respond-and-recover-nist-cybersecurity-framework)

## 1. Core Security Concepts

### CIA triad

| Principle | Meaning | Airport/airline example |
|---|---|---|
| Confidentiality | Only authorized users can access data | Passenger PII, payment-card data, security manifests |
| Integrity | Data is accurate and unmodified | Flight status, baggage routing, fare data |
| Availability | Systems are usable when needed | Check-in, flight-ops, baggage-handling systems |
| Authenticity | Data/user identity is genuine | Verified airline partner API request |
| Non-repudiation | Actor cannot deny an action | Signed booking/cancellation audit trail |
| Accountability | Actions can be traced to an identity | Admin change linked to employee ID |

### Security vs safety

| Term | Focus | Example |
|---|---|---|
| Security | Protecting systems/data from malicious acts | Preventing unauthorized access to booking API |
| Safety | Preventing accidental physical/operational harm | Preventing runway-conflict alerts from failing |
| Privacy | Protecting personal data rights | Passenger travel history and ID data |
| Reliability | System performs consistently | Check-in kiosk remains operational |

### Risk formula

\[
Risk = Threat \times Vulnerability \times Impact
\]

| Term | Meaning | Airline example |
|---|---|---|
| Asset | Anything valuable to protect | Booking database, flight-ops system |
| Threat | Potential cause of harm | Ransomware group, insider, nation-state actor |
| Vulnerability | Weakness that can be exploited | Unpatched API gateway, weak password policy |
| Exploit | Technique/code that uses vulnerability | SQL-injection payload |
| Impact | Consequence if exploited | Flight delays, PII breach, financial fraud |
| Likelihood | Probability of exploitation | Internet-exposed legacy API has higher likelihood |
| Risk acceptance | Knowingly accepting residual risk | Low-risk internal reporting tool |
| Risk mitigation | Reduce likelihood/impact | Patch, MFA, WAF |
| Risk transfer | Shift risk to third party | Cyber insurance |
| Risk avoidance | Eliminate risky activity | Disable insecure legacy FTP service |

### Threat actor types

| Actor | Motivation | Airline example |
|---|---|---|
| Script kiddie | Curiosity, easy attacks | Scanning exposed airport APIs |
| Hacktivist | Ideological/political cause | Defacing airline website |
| Cybercriminal | Financial gain | Ransomware, ticket fraud, card theft |
| Insider | Malice, negligence, coercion | Employee leaks passenger manifest |
| Nation-state/APT | Espionage, disruption | Targeting aviation infrastructure |
| Competitor | Business advantage | Stealing pricing or route strategy |
| Supply-chain attacker | Compromise trusted vendor/software | Malicious update to airport software |

**APT** = Advanced Persistent Threat: a well-resourced, long-duration, targeted attacker, often nation-state linked.

## 2. Threat Vectors and Attack Surfaces

A **threat vector** is the path an attacker uses to reach a target. An **attack surface** is the collection of points where an unauthorized actor can interact with a system.

| Threat vector | Description | Airline example |
|---|---|---|
| Phishing email | Deceptive email tricks user | Fake “flight schedule change” attachment |
| Spear phishing | Targeted phishing | Email aimed at airline finance employee |
| Whaling | Phishing senior executives | Fake CFO invoice request |
| Smishing | SMS phishing | Fake refund-link SMS |
| Vishing | Voice phishing | Fake airline support call asking OTP |
| Malicious website | Drive-by download/credential theft | Fake airline refund portal |
| Malicious USB | Infected removable media | Infected USB inserted into ground-system PC |
| Compromised credentials | Stolen username/password | Leaked employee VPN credentials |
| Public API abuse | API scanning, brute force, scraping | Automated booking attempts |
| Third-party compromise | Vendor/supply-chain breach | Compromised baggage-system vendor |
| Insider access | Authorized user misuses access | Employee exports passenger data |
| Physical access | Direct hardware/device access | Unattended airport operations terminal |
| Social engineering | Human manipulation | Fake IT technician requests credentials |
| Supply chain | Malicious dependency/update | Compromised open-source library |
| Cloud misconfiguration | Public storage/over-permissive IAM | Public S3 bucket with passenger records |

### Attack surface examples

- Airline mobile app and web portal
- Public booking/check-in APIs
- Airport Wi-Fi and passenger kiosks
- Baggage-handling and flight-operations networks
- Third-party travel-agent integrations
- Cloud storage and identity systems
- Employee VPN, email, and endpoints
- Physical badge readers and maintenance laptops

## 3. Common Cyberattacks

### Malware

| Type | Behavior | Airline example |
|---|---|---|
| Virus | Attaches to files; needs host execution | Infected spreadsheet on ops machine |
| Worm | Self-replicates across network | Spreads through unpatched internal servers |
| Trojan | Disguised as legitimate software | Fake “flight-delay checker” utility |
| Ransomware | Encrypts data and demands ransom | Locks baggage/revenue-management systems |
| Spyware | Secretly collects information | Captures agent credentials |
| Keylogger | Records keystrokes | Captures booking-system passwords |
| Rootkit | Hides malicious activity at system level | Conceals backdoor in ground-system server |
| Botnet | Infected devices controlled remotely | DDoS against airline website |
| Cryptominer | Uses resources to mine crypto | Slows airport kiosk/backend servers |
| Fileless malware | Runs in memory, avoids files | PowerShell-based attack |

### Social engineering

| Attack | Description | Airline example |
|---|---|---|
| Phishing | Mass fraudulent email | Fake booking-confirmation link |
| Spear phishing | Targeted email | Fraudulent invoice to airline procurement |
| Whaling | Targets executives | CEO fraud/payment request |
| Vishing | Voice-based fraud | Fake support asks for MFA code |
| Smishing | SMS fraud | Fake refund/refund-link message |
| Pretexting | Fabricated scenario | Attacker poses as airport IT contractor |
| Baiting | Offers something tempting | Infected USB labeled “Flight Crew Roster” |
| Tailgating/piggybacking | Follows authorized person through door | Attacker enters secure airport area |
| Shoulder surfing | Observes secrets | Attacker sees check-in agent password |
| MFA fatigue | Repeated MFA prompts | Victim approves attacker login |
| Business Email Compromise | Impersonates executive/vendor | Fraudulent payment-account change |

### Network attacks

| Attack | Description | Airline example |
|---|---|---|
| DDoS | Floods service to make it unavailable | Airline booking site taken offline |
| Man-in-the-Middle | Intercepts communication | Captures credentials on fake airport Wi-Fi |
| ARP spoofing | Attacker impersonates another host on LAN | Intercepts internal traffic |
| DNS spoofing/poisoning | Redirects domain to attacker | `airline.com` resolves to fake site |
| DNS tunneling | Hides data in DNS traffic | Exfiltrates data from restricted network |
| Port scanning | Discovers open services | Finds exposed legacy flight API |
| Packet sniffing | Captures network packets | Reads unencrypted traffic |
| IP spoofing | Forges source IP | Bypasses weak IP-based controls |
| MAC flooding | Overloads switch CAM table | Forces traffic through attacker |
| Evil twin | Rogue Wi-Fi access point | Fake “Airport-Free-WiFi” hotspot |
| Session hijacking | Steals/reuses session token | Takes over agent portal session |
| Replay attack | Reuses captured valid request | Replays payment/boarding request |
| BGP hijacking | Redirects internet routing | Traffic to airline domain diverted |

### Application attacks

| Attack | Description | Airline example |
|---|---|---|
| SQL injection | Malicious SQL through input | Extracts passenger records |
| NoSQL injection | Manipulates NoSQL queries | Bypasses booking lookup filter |
| Command injection | Executes OS commands | Runs commands on booking server |
| XSS | Injects browser scripts | Steals agent session cookie |
| CSRF | Forces authenticated browser action | Changes passenger profile |
| SSRF | Server fetches attacker-controlled URL | Accesses internal metadata service |
| XXE | Exploits XML external entities | Reads server files via SOAP/XML |
| Directory traversal | Accesses files outside allowed path | Reads configuration files |
| Insecure deserialization | Exploits object deserialization | Remote code execution |
| Broken access control | Accesses resources without permission | Views another passenger’s booking |
| IDOR | Changes object ID to access another object | `/bookings/1002` instead of `/bookings/1001` |
| API abuse | Automated scraping/brute force | Mass seat-availability checks |
| Business-logic abuse | Misuses valid functionality | Exploiting refund/coupon workflow |

### Authentication attacks

| Attack | Description | Airline example |
|---|---|---|
| Brute force | Tries all password combinations | Guessing agent portal password |
| Credential stuffing | Reuses breached credentials | Tests leaked passwords on airline portal |
| Password spraying | One common password across many accounts | `Welcome@123` across staff accounts |
| Pass-the-hash | Reuses stolen password hash | Moves laterally in Windows environment |
| Pass-the-ticket | Reuses Kerberos ticket | Accesses internal airline services |
| Token theft | Steals JWT/session token | Hijacks mobile-app session |
| MFA fatigue | Floods push approvals | Passenger approves fraudulent login |
| SIM swapping | Transfers victim phone number | Receives passenger OTP |
| Credential harvesting | Fake login page | Fake airline SSO portal |

## 4. Common Vulnerabilities

A **vulnerability** is a weakness in software, configuration, process, or human behavior that can be exploited.

### OWASP Top 10: 2021

OWASP Top 10 is a widely used awareness document listing critical web-application security risks. [top10.owasp](https://top10.owasp.org/2021/index.html)

| Rank | Risk | Airline example | Mitigation |
|---:|---|---|---|
| A01 | Broken Access Control | Passenger accesses another booking | Deny-by-default authorization, ownership checks |
| A02 | Cryptographic Failures | Passwords/card data stored unencrypted | TLS, AES encryption, strong hashing |
| A03 | Injection | SQL injection in flight search | Parameterized queries, input validation |
| A04 | Insecure Design | Refund flow can be abused | Threat modeling, secure design patterns |
| A05 | Security Misconfiguration | Debug endpoint enabled in production | Hardening, IaC scanning, least privilege |
| A06 | Vulnerable/Outdated Components | Old log4j or framework version | SBOM, dependency scanning, patching |
| A07 | Identification and Authentication Failures | Weak OTP/session handling | MFA, secure session management |
| A08 | Software and Data Integrity Failures | Malicious dependency/update | Signed artifacts, CI/CD integrity controls |
| A09 | Security Logging and Monitoring Failures | Brute-force attacks unnoticed | Central logs, alerts, SIEM |
| A10 | SSRF | API fetches internal metadata URL | URL allowlist, egress controls |

Broken Access Control is ranked first in OWASP Top 10:2021, while Cryptographic Failures is second and Injection is third. [top10.owasp](https://top10.owasp.org/2021/index.html)

### CWE and CVE

| Term | Full form | Meaning |
|---|---|---|
| CVE | Common Vulnerabilities and Exposures | Public identifier for a known vulnerability |
| CWE | Common Weakness Enumeration | Category/type of software weakness |
| CVSS | Common Vulnerability Scoring System | Severity score for vulnerability |
| NVD | National Vulnerability Database | US database of CVEs |
| Exploit | Code/technique using vulnerability | Proof-of-concept or weaponized code |
| Patch | Fix for vulnerability | Vendor security update |
| Zero-day | Vulnerability exploited before patch exists | Unknown flaw in airport software |
| N-day | Known vulnerability after patch/disclosure | Known CVE awaiting patch |

**CVSS severity ranges:**

| Score | Severity |
|---:|---|
| 0.1–3.9 | Low |
| 4.0–6.9 | Medium |
| 7.0–8.9 | High |
| 9.0–10.0 | Critical |

### Vulnerability examples

| Vulnerability | Description | Airline impact |
|---|---|---|
| SQL injection | Untrusted input changes SQL query | Passenger-data breach |
| XSS | Script injection in browser | Session theft |
| CSRF | Unauthorized action from browser | Booking modification |
| SSRF | Server requests attacker URL | Cloud metadata credential theft |
| IDOR | Direct object reference abuse | Access another passenger’s ticket |
| Missing MFA | Only password protects account | Account takeover |
| Weak cryptography | MD5/SHA-1, weak keys | Password/manifest compromise |
| Default credentials | Vendor default passwords | Airport device compromise |
| Open redirect | Redirects to attacker site | Phishing |
| Race condition | Concurrent requests exploit timing | Double refund/seat overbooking |
| Privilege escalation | User gains higher privileges | Agent becomes admin |
| Insecure deserialization | Malicious serialized object | Remote code execution |
| Misconfigured CORS | Any origin can call API | Token/data theft |
| Verbose errors | Stack traces expose internals | Helps attacker map system |

## 5. Cryptography Fundamentals

### Encryption types

| Type | Key usage | Use case | Airline example |
|---|---|---|---|
| Symmetric | Same key encrypts/decrypts | Fast bulk encryption | AES encrypts passenger database |
| Asymmetric | Public key encrypts; private key decrypts | Key exchange/signatures | TLS certificate |
| Hashing | One-way transformation | Integrity/password storage | SHA-256 password hash |
| Digital signature | Private key signs; public key verifies | Integrity + authenticity | Signed software update |

### Common algorithms

| Purpose | Secure/common options | Avoid |
|---|---|---|
| Symmetric encryption | AES-256, ChaCha20 | DES, 3DES, RC4 |
| Asymmetric encryption | RSA-2048+, ECC | RSA-1024 |
| Hashing | SHA-256, SHA-3 | MD5, SHA-1 |
| Password hashing | Argon2, bcrypt, scrypt, PBKDF2 | Plain SHA-256 alone |
| Digital signatures | RSA, ECDSA, EdDSA | MD5-based signatures |
| Key exchange | ECDHE, DH | Static RSA key exchange |

### TLS/SSL

| Term | Meaning |
|---|---|
| TLS | Transport Layer Security; modern protocol |
| SSL | Older predecessor; deprecated |
| Certificate | Binds public key to identity |
| CA | Certificate Authority |
| CSR | Certificate Signing Request |
| SAN | Subject Alternative Name |
| Certificate pinning | App trusts only expected certificate/public key |
| HSTS | Forces HTTPS usage |
| mTLS | Both client and server authenticate with certificates |
| Perfect Forward Secrecy | Compromised long-term key does not reveal past sessions |

**Airline use:** All passenger apps, booking APIs, check-in kiosks, and partner integrations should use TLS 1.2+; TLS 1.3 is preferred.

### PKI

**Public Key Infrastructure (PKI)** manages digital certificates and keys.

| Component | Role |
|---|---|
| Certificate Authority | Issues/signs certificates |
| Registration Authority | Verifies identity before issuance |
| Certificate repository | Stores/validates certificates |
| CRL | Certificate Revocation List |
| OCSP | Online certificate-status protocol |
| Key management system | Generates/stores/rotates keys |

## 6. Identity, Authentication and Access Control

### Authentication factors

| Factor | Description | Example |
|---|---|---|
| Something you know | Knowledge | Password, PIN |
| Something you have | Possession | OTP device, smart card, phone |
| Something you are | Biometrics | Fingerprint, face |
| Somewhere you are | Location | Airport network/geofence |
| Something you do | Behavior | Typing pattern |

**MFA** requires at least two different factors. Password plus OTP is MFA; password plus PIN is not.

### SSO and federation

| Term | Meaning | Airline example |
|---|---|---|
| SSO | Single Sign-On | One login for HR, email, booking portal |
| IdP | Identity Provider | Airline identity service |
| SP | Service Provider | Booking/check-in application |
| SAML | XML-based enterprise federation | Airline partner SSO |
| OIDC | OpenID Connect; identity layer over OAuth 2.0 | Mobile passenger login |
| LDAP | Directory-access protocol | Employee directory |
| Kerberos | Ticket-based network authentication | Internal Windows domain |
| SCIM | Automated user provisioning/deprovisioning | Joiner-mover-leaver process |

### Access-control models

| Model | Description | Airline example |
|---|---|---|
| DAC | Owner controls access | File shared by employee |
| MAC | Central policy/labels control access | Military/secure airport system |
| RBAC | Permissions assigned through roles | Agent, supervisor, admin |
| ABAC | Decisions based on attributes | Agent can access only assigned region |
| ReBAC | Relationship-based access | Passenger can access own booking |
| Zero Trust | Never trust, always verify | Every API request authenticated/authorized |

### Privileged access

| Term | Meaning | Airline example |
|---|---|---|
| Least privilege | Minimum required access | Baggage agent cannot access payroll |
| Need to know | Access only required information | Agent sees relevant flight data |
| Separation of duties | Split critical tasks | Requester and approver differ |
| PAM | Privileged Access Management | Controls admin credentials |
| JIT access | Temporary elevated access | DBA gets 2-hour production access |
| Break-glass access | Emergency elevated access | Major outage response |
| Service account | Non-human identity | Booking service DB account |

## 7. Network Security

| Control | Purpose | Airline example |
|---|---|---|
| Firewall | Filters traffic by rules | Blocks inbound access to internal DB |
| NGFW | Application-aware firewall | Identifies and controls apps |
| IDS | Detects intrusion | Alerts on port scan |
| IPS | Blocks intrusion | Drops malicious packet |
| WAF | Protects web/API layer | Blocks SQLi/XSS payloads |
| VPN | Encrypted remote access | Employee connects to airline network |
| NAC | Network Access Control | Checks device compliance before LAN access |
| VLAN | Logical network segmentation | Separates passenger Wi-Fi from ops |
| DMZ | Isolated external-facing zone | Public web/API servers |
| Proxy | Intermediary for traffic | Controls outbound employee traffic |
| NAT | Translates private/public IPs | Internal servers access internet |
| DLP | Prevents data loss | Blocks passenger-manifest export |
| EDR | Endpoint detection/response | Detects malware on agent laptop |
| NDR | Network detection/response | Detects lateral movement |
| SIEM | Aggregates/correlates security events | Alerts on repeated failed logins |
| SOAR | Automates security response | Auto-isolates infected endpoint |

### Zero Trust principles

- Verify explicitly: authenticate and authorize every request.
- Use least-privilege access.
- Assume breach: segment, monitor, and limit blast radius.
- Micro-segment networks.
- Continuously monitor device/user behavior.
- Encrypt traffic end to end.

## 8. Cloud and Container Security

| Area | Risk | Control |
|---|---|---|
| IAM | Over-permissive roles | Least privilege, access reviews |
| Storage | Public buckets | Block public access, encryption |
| Network | Open security groups | Restrict ingress/egress |
| Secrets | Hard-coded credentials | Vault/KMS, secret rotation |
| VMs | Unpatched OS | Patching, CIS benchmarks |
| Containers | Vulnerable images | Image scanning, minimal base images |
| Kubernetes | Overprivileged pods | RBAC, Pod Security Standards |
| Serverless | Event injection, excessive permissions | Input validation, scoped roles |
| APIs | Public endpoints | Gateway, authZ, rate limiting |
| Logging | Blind spots | Cloud audit logs, SIEM |

**Shared responsibility model:**

| Model | Cloud provider secures | Customer secures |
|---|---|---|
| IaaS | Physical, hypervisor, infrastructure | OS, apps, data, IAM |
| PaaS | Runtime/platform | Apps, data, IAM, configuration |
| SaaS | Entire platform | User access, data, configuration |

## 9. Secure SDLC and DevSecOps

### Secure SDLC phases

| Phase | Security activity | Airline example |
|---|---|---|
| Requirements | Define security/compliance needs | PCI-DSS for payment data |
| Design | Threat modeling | Model booking/refund flows |
| Development | Secure coding standards | OWASP secure coding |
| Code review | Manual/automated review | Review auth logic |
| Testing | SAST/DAST/SCA | Scan booking service |
| Deployment | IaC/container scanning | Scan Terraform/Kubernetes |
| Operations | Monitoring/incident response | Detect API abuse |
| Decommissioning | Secure data disposal | Delete old passenger backups |

### DevSecOps tools

| Type | Purpose | Examples |
|---|---|---|
| SAST | Static source-code analysis | SonarQube, Semgrep |
| DAST | Dynamic running-app testing | OWASP ZAP, Burp Suite |
| IAST | Instrumented runtime analysis | Contrast Security |
| SCA | Dependency vulnerability scan | Snyk, Dependabot, Trivy |
| Secret scanning | Detect exposed credentials | Gitleaks, TruffleHog |
| IaC scanning | Scan infrastructure code | Checkov, tfsec |
| Container scanning | Scan images | Trivy, Clair |
| SBOM | Software bill of materials | CycloneDX, SPDX |
| Policy as code | Enforce guardrails | OPA, Kyverno |

### Threat modeling

Threat modeling identifies assets, entry points, trust boundaries, threats, and mitigations early.

Common methods:

| Method | Focus |
|---|---|
| STRIDE | Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege |
| PASTA | Risk-centric attack simulation |
| DREAD | Damage, Reproducibility, Exploitability, Affected users, Discoverability |
| Attack trees | Attack paths/goals |
| LINDDUN | Privacy threats |

### STRIDE airline example

| Threat | Example | Mitigation |
|---|---|---|
| Spoofing | Fake airline partner API client | mTLS/API key/OAuth |
| Tampering | Modify fare in transit | TLS + digital signature |
| Repudiation | Agent denies cancellation | Immutable audit logs |
| Information disclosure | Passenger data exposed | Encryption, access control |
| Denial of service | Booking API flooded | Rate limiting, autoscaling |
| Elevation of privilege | Agent becomes admin | RBAC, least privilege |

## 10. Security Operations and Incident Response

### Security operations terms

| Term | Meaning |
|---|---|
| SOC | Security Operations Center |
| SIEM | Security Information and Event Management |
| SOAR | Security Orchestration, Automation and Response |
| IOC | Indicator of Compromise |
| IOA | Indicator of Attack |
| TTP | Tactics, Techniques, and Procedures |
| Threat intelligence | Knowledge about threats/actors |
| Threat hunting | Proactive search for hidden threats |
| Purple team | Red + blue collaboration |
| Red team | Offensive simulation |
| Blue team | Defensive operations |
| Tabletop exercise | Discussion-based incident simulation |

### Incident-response lifecycle

NIST CSF includes **Detect**, **Respond**, and **Recover** functions, which map directly to incident handling. [nist](https://www.nist.gov/cyberframework/csf-11-five-functions)

```text
Preparation
   -> Detection and Analysis
   -> Containment
   -> Eradication
   -> Recovery
   -> Post-Incident Activity
```

| Phase | Actions | Airline ransomware example |
|---|---|---|
| Preparation | Playbooks, backups, training | Tested restore plan for booking DB |
| Detection | Alerts, logs, user report | SIEM detects mass file encryption |
| Containment | Isolate systems, block C2 | Disconnect affected ops VLAN |
| Eradication | Remove malware/close entry point | Rebuild hosts, patch VPN |
| Recovery | Restore clean systems/data | Restore booking DB from backup |
| Lessons learned | RCA, controls improvement | Add EDR coverage, MFA |

### Important metrics

| Metric | Meaning |
|---|---|
| MTTD | Mean Time to Detect |
| MTTR | Mean Time to Respond/Resolve |
| MTTC | Mean Time to Contain |
| RPO | Recovery Point Objective; acceptable data loss |
| RTO | Recovery Time Objective; acceptable downtime |
| SLA | Service Level Agreement |
| SLO | Service Level Objective |
| Error budget | Allowed unreliability |

**Example:** For a check-in service, RPO = 15 minutes and RTO = 1 hour means the organization aims to lose no more than 15 minutes of data and restore within one hour.

## 11. MITRE ATT&CK

**MITRE ATT&CK** is a globally accessible knowledge base of adversary tactics and techniques based on real-world observations, used for threat modeling, detection, and defense planning. [attack.mitre](https://attack.mitre.org/)

| Component | Meaning | Airline example |
|---|---|---|
| Tactic | Attacker’s objective | Credential Access |
| Technique | How objective is achieved | Phishing, brute force |
| Sub-technique | Specific variant | Spear-phishing link |
| Procedure | Real-world implementation | Specific group’s phishing email |

### ATT&CK tactics

| Tactic | Goal | Airline example |
|---|---|---|
| Reconnaissance | Gather information | Scan airline domains/APIs |
| Resource Development | Obtain infrastructure/capabilities | Register lookalike domain |
| Initial Access | Enter environment | Phishing employee |
| Execution | Run malicious code | Malicious macro/PowerShell |
| Persistence | Maintain access | Scheduled task/backdoor |
| Privilege Escalation | Gain higher privileges | Exploit local vulnerability |
| Defense Evasion | Avoid detection | Disable logging |
| Credential Access | Steal credentials | Keylogger |
| Discovery | Map environment | Enumerate internal services |
| Lateral Movement | Move across systems | Compromise adjacent server |
| Collection | Gather target data | Access passenger database |
| Command and Control | Communicate with attacker | HTTPS beacon |
| Exfiltration | Remove data | Upload manifests externally |
| Impact | Disrupt/destroy | Ransomware encryption |

## 12. Compliance and Data Protection

| Regulation/standard | Scope | Airline relevance |
|---|---|---|
| GDPR | EU personal-data protection | EU passenger data |
| India DPDP Act | India digital personal-data protection | Indian passenger data |
| PCI DSS | Payment-card data security | Card payments/refunds |
| ISO/IEC 27001 | Information-security management system | Airline ISMS certification |
| SOC 2 | Service-organization controls | Cloud/SaaS vendor assurance |
| HIPAA | US health data | Medical assistance data |
| IATA standards | Air-transport industry practices | Passenger/baggage interline data |
| CIS Benchmarks | Secure configuration guidance | Server/cloud hardening |

### Data classification

| Class | Example | Protection |
|---|---|---|
| Public | Airport map, flight schedule | Integrity/availability |
| Internal | Operational reports | Access control |
| Confidential | Fare strategy, contracts | Encryption, restricted access |
| Restricted/PII | Passport, address, payment data | Strong encryption, audit, DLP |

### Data protection controls

- Data minimization
- Purpose limitation
- Encryption at rest and in transit
- Tokenization of card numbers
- Masking in non-production
- Pseudonymization
- Retention and deletion policies
- Backup encryption
- DLP and audit trails
- Privacy by design

## 13. Advanced Concepts

### Defense in depth

Use multiple independent controls so one failure does not cause full compromise.

```text
User awareness
   -> MFA / identity controls
   -> Endpoint EDR
   -> Network segmentation
   -> WAF / API gateway
   -> Application authorization
   -> Database encryption
   -> Backup and monitoring
```

### Least privilege and need-to-know

- Grant minimum permissions.
- Review access periodically.
- Use just-in-time production access.
- Separate duties for payment/refund approvals.
- Use short-lived credentials where possible.

### Security by design

Security must be a non-functional requirement, not a final checklist.

Examples:
- Threat-model every new booking/refund API.
- Deny-by-default authorization.
- Fail securely: deny access if authorization service is unavailable.
- Log security-relevant events without logging secrets.
- Use secure defaults.
- Design for auditability.

### Supply-chain security

| Control | Purpose |
|---|---|
| SBOM | Know software components |
| Dependency pinning | Avoid unexpected updates |
| Signed commits/artifacts | Verify origin/integrity |
| Provenance | Trace build to source |
| Vendor assessment | Assess third-party risk |
| Software composition analysis | Detect vulnerable dependencies |
| Reproducible builds | Verify build integrity |

### AI/ML security

| Risk | Description | Airline example |
|---|---|---|
| Prompt injection | Malicious instruction changes model behavior | Passenger-support chatbot leaks policy |
| Data poisoning | Corrupts training data | Manipulates fraud-detection model |
| Model inversion | Recovers training data | Exposes passenger patterns |
| Model theft | Extracts proprietary model | Copies fare-prediction model |
| Adversarial examples | Crafted inputs fool model | Bypasses baggage-image detection |
| Insecure plugin/tool use | LLM agent misuses API | Agent performs unauthorized refund |
| PII leakage | Model outputs sensitive data | Chatbot reveals passenger details |

## 14. Rapid Revision: One-Liners

- **CIA** = Confidentiality, Integrity, Availability.
- **Risk** = Threat × Vulnerability × Impact.
- **Threat** = potential danger; **vulnerability** = weakness; **exploit** = uses the weakness.
- **Attack surface** = all points where an attacker can interact.
- **Threat vector** = route used to deliver/execute an attack.
- **Phishing** = fraudulent email; **smishing** = SMS; **vishing** = voice.
- **Whaling** targets senior executives.
- **Ransomware** encrypts data and demands payment.
- **DDoS** targets availability.
- **MITM** intercepts communication.
- **Replay attack** reuses a valid captured request.
- **SQL injection** targets databases.
- **XSS** executes malicious scripts in a victim’s browser.
- **CSRF** tricks an authenticated browser into unwanted actions.
- **SSRF** makes a server request an attacker-controlled URL.
- **IDOR** is a common broken-access-control flaw.
- **Zero-day** has no patch available at time of exploitation.
- **CVE** identifies a vulnerability; **CWE** classifies weakness type.
- **CVSS** scores vulnerability severity.
- **TLS** encrypts data in transit; SSL is deprecated.
- **AES** is symmetric encryption; **RSA/ECC** are asymmetric.
- **MD5 and SHA-1** should not be used for security-sensitive hashing.
- **Argon2, bcrypt, scrypt, PBKDF2** are password-hashing algorithms.
- **MFA** requires two different authentication factors.
- **SSO** allows one login for multiple applications.
- **SAML** is XML-based enterprise federation; **OIDC** is identity over OAuth 2.0.
- **RBAC** uses roles; **ABAC** uses attributes; **ReBAC** uses relationships.
- **Zero Trust** means “never trust, always verify.”
- **IDS** detects; **IPS** blocks.
- **WAF** protects web/API traffic.
- **DLP** prevents sensitive-data leakage.
- **SIEM** aggregates and correlates logs.
- **SOAR** automates response actions.
- **EDR** detects/responds on endpoints.
- **NIST CSF functions** = Identify, Protect, Detect, Respond, Recover. [nist](https://www.nist.gov/blogs/taking-measure/identify-protect-detect-respond-and-recover-nist-cybersecurity-framework)
- **MITRE ATT&CK** categorizes adversary tactics and techniques. [attack.mitre](https://attack.mitre.org/)
- **OWASP Top 10:2021** ranks Broken Access Control first. [top10.owasp](https://top10.owasp.org/2021/index.html)
- **STRIDE** = Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege.
- **RPO** = acceptable data loss; **RTO** = acceptable downtime.
- **Defense in depth** uses multiple security layers.
- **Least privilege** grants only required access.
- **Shared responsibility** divides security duties between cloud provider and customer.

## 15. Probable One-Mark Questions

1. **What does CIA stand for in cybersecurity?**  
   Confidentiality, Integrity, Availability.

2. **Which CIA principle is attacked by ransomware?**  
   Availability.

3. **Which attack targets confidentiality by intercepting traffic?**  
   Man-in-the-Middle.

4. **What is a vulnerability?**  
   A weakness that can be exploited.

5. **What is a zero-day vulnerability?**  
   A vulnerability exploited before a patch is available.

6. **What does CVE stand for?**  
   Common Vulnerabilities and Exposures.

7. **What does CWE stand for?**  
   Common Weakness Enumeration.

8. **What does CVSS measure?**  
   Vulnerability severity.

9. **Which OWASP Top 10:2021 risk is ranked first?**  
   Broken Access Control. [top10.owasp](https://top10.owasp.org/2021/index.html)

10. **Which attack injects malicious SQL through input fields?**  
   SQL injection.

11. **Which attack injects JavaScript into a web page?**  
   Cross-Site Scripting.

12. **Which attack tricks an authenticated browser into performing an action?**  
   CSRF.

13. **Which attack causes a server to request an attacker-controlled URL?**  
   SSRF.

14. **Which attack floods a service to make it unavailable?**  
   DDoS.

15. **What is phishing via SMS called?**  
   Smishing.

16. **What is phishing via phone call called?**  
   Vishing.

17. **What is targeted phishing of executives called?**  
   Whaling.

18. **Which NIST CSF function identifies cybersecurity events?**  
   Detect. [nist](https://www.nist.gov/cyberframework/csf-11-five-functions)

19. **Name the five NIST CSF core functions.**  
   Identify, Protect, Detect, Respond, Recover. [nist](https://www.nist.gov/blogs/taking-measure/identify-protect-detect-respond-and-recover-nist-cybersecurity-framework)

20. **What does MITRE ATT&CK model?**  
   Adversary tactics and techniques. [attack.mitre](https://attack.mitre.org/)

21. **What does STRIDE stand for?**  
   Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege.

22. **Which access-control model uses user roles?**  
   RBAC.

23. **Which principle grants only the minimum required access?**  
   Least privilege.

24. **Which control blocks web-application attacks such as SQL injection?**  
   WAF.

25. **Which system aggregates and correlates security logs?**  
   SIEM.

26. **Which protocol replaces SSL for encrypted transport?**  
   TLS.

27. **Which encryption type uses the same key for encryption and decryption?**  
   Symmetric encryption.

28. **Which password-hashing algorithms are preferred over plain SHA-256?**  
   Argon2, bcrypt, scrypt, PBKDF2.

29. **What does MFA require?**  
   Two or more different authentication factors.

30. **What does RTO mean?**  
   Recovery Time Objective — maximum acceptable downtime.
