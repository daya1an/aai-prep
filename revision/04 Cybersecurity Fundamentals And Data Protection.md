# Module 4: Cybersecurity Fundamentals, Data Protection Frameworks & Dashboarding
**Syllabus Coverage:** Part-B, Section 4 (Intro, i, ii, iii) — Threat vectors, types of cyberattacks, common vulnerabilities; Network Security (Wi-Fi, LAN, VLAN, Corporate LANs, Cloud, Firewalls, VPNs, IDS/IPS, Encryption, Network Segmentation, Endpoint Security, Cloud Security); Common Elements in Data Protection Frameworks (Data Classification, Access Controls, Encryption at rest & in transit, Data Minimization, Windowing: Tumbling, Sliding, Session, Count-based, Audit & Monitoring, Incident Response, Data Subject Rights, Data Protection Practices & Policies); Cybersecurity Dashboarding.

## 4.1 Core Security Concepts, Threat Vectors, Cyberattacks & Vulnerabilities

### 1. Foundational Security Pillars (CIA Triad & AAA Framework)
* **The CIA Triad:**
  1. **Confidentiality:** Preventing unauthorized disclosure of information (enforced via **Encryption, Access Control Lists, Data Masking, Steganography**). Violated by **Snooping, Eavesdropping, Phishing, Dumpster Diving**.
  2. **Integrity:** Ensuring data is trustworthy, complete, and has not been altered in an unauthorized manner at rest or in transit (enforced via **Cryptographic Hashing SHA-256/SHA-3, HMAC, Digital Signatures, Checksums, Version Control**). Violated by **Man-in-the-Middle (MITM) tampering, SQL Injection modification, Salami Attacks**.
  3. **Availability:** Ensuring authorized users have reliable, timely access to data and systems when needed (enforced via **Redundancy, RAID, Load Balancers, Clustering, Backups, DDoS Mitigation/CDN**). Violated by **DoS/DDoS, Ransomware encryption, Hardware failure**.
* **Extended Security Principles:**
  * **Non-Repudiation:** Guarantee that a sender/actor **cannot deny** having sent a message or performed an action (enforced via **Asymmetric Digital Signatures** + **Public Key Infrastructure (PKI)** + **Audit Logs**).
  * **AAA Framework:**
    * **Authentication:** Verifying identity (*"Who are you?"* — Something you **know** [password/PIN], Something you **have** [smart card/token/authenticator], Something you **are** [biometrics], Somewhere you **are** [geolocation]).
    * **Authorization:** Determining granted permissions (*"What are you allowed to do?"*).
    * **Accounting (Auditing):** Tracking user actions and resource consumption (*"What did you do and when?"*).
  * **Principle of Least Privilege (PoLP):** Granting users/processes only the **minimum permissions necessary** to perform their assigned function, and only for the minimum time required (**Just-in-Time [JIT] access**).
  * **Defense in Depth (Layered Security):** Deploying multiple overlapping security controls (Physical, Network, Endpoint, Application, Data, Human) so that if one control fails, another stops the attack.
  * **Zero Trust Architecture (NIST SP 800-207):** **"Never Trust, Always Verify"** — eliminates implicit trust based on network location (e.g., being inside the corporate LAN); verifies identity, device posture, and context on **every single access request**.

### 2. Threat Terminology (Risk Formula)
* **Asset:** Anything of value to the organization (data, servers, reputation).
* **Vulnerability:** A **weakness or flaw** in design, implementation, or configuration that can be exploited.
* **Threat:** Any potential danger or circumstance (natural or human **Threat Actor**) capable of exploiting a vulnerability.
* **Threat Vector (Attack Vector):** The **pathway or method** used by a threat actor to gain unauthorized access (e.g., email attachments, open RDP ports, USB drives, misconfigured cloud buckets, compromised credentials).
* **Exploit:** Specific code, tool, or technique that takes advantage of a vulnerability.
* **Risk Equation:** $\text{Risk} = \text{Threat} \times \text{Vulnerability} \times \text{Impact (Asset Value)}$ (Quantitative risk: $\text{ALE} = \text{SLE} \times \text{ARO}$, where **SLE** = Single Loss Expectancy and **ARO** = Annualized Rate of Occurrence).

### 3. Types of Cyberattacks & Common Vulnerabilities (High-Yield Exam Matrix)
| Attack Category | Specific Attack / Vulnerability | Mechanism & Primary Countermeasure |
| :--- | :--- | :--- |
| **Social Engineering** | **Phishing** (Bulk), **Spear Phishing** (Targeted individual/group), **Whaling** (C-suite executives), **Vishing** (Voice/Phone), **Smishing** (SMS), **Pharming** (DNS cache poisoning/hosts file redirect), **Baiting**, **Tailgating/Piggybacking** | Exploits human psychology; mitigated via **Security Awareness Training, Phishing-resistant MFA (FIDO2/WebAuthn), SPF/DKIM/DMARC email authentication**. |
| **Malware Types** | **Virus** (requires host file & human action to spread), **Worm** (self-replicating across networks **without** human interaction, e.g., WannaCry, Stuxnet), **Trojan** (disguised as legitimate software), **Ransomware** (encrypts files and demands ransom; **Double Extortion** also exfiltrates data), **Rootkit** (hides deep in OS kernel/bootloader), **Spyware/Keylogger**, **Fileless Malware** (lives in RAM using PowerShell/WMI — **Living off the Land [LotL]**) | Mitigated via **EDR/EPP, Application Allowlisting, Immutable Offline Backups (3-2-1 Rule), Patch Management, Network Segmentation**. |
| **Injection & Web Attacks (OWASP Top 10)** | **SQL Injection (SQLi):** Injects malicious SQL (`' OR 1=1 --`) into unvalidated inputs (In-band/Union, Error-based, Blind Boolean/Time-based). | **Primary Fix:** **Parameterized Queries / Prepared Statements** (separates SQL code structure from user data) + Input Validation. |
| **Web Attacks** | **Cross-Site Scripting (XSS):** Injects malicious client-side JavaScript into web pages viewed by other users (**Stored/Persistent XSS**, **Reflected XSS**, **DOM-based XSS**). | **Primary Fix:** **Context-aware Output Encoding/Escaping**, **Content Security Policy (CSP)** headers, and **`HttpOnly`** flag on session cookies. |
| **Web Attacks** | **Cross-Site Request Forgery (CSRF / XSRF):** Tricks an authenticated victim's browser into sending an unintended state-changing request using cached session cookies. | **Primary Fix:** **Anti-CSRF Tokens (Synchronizer Token Pattern)** and **`SameSite=Strict/Lax`** cookie attribute. |
| **Web Attacks** | **SSRF (Server-Side Request Forgery):** Tricks the server into making HTTP requests to internal-only resources (e.g., cloud metadata IP `169.254.169.254`). | **Primary Fix:** Strict URL allowlisting, disabling unused URL schemas, and **IMDSv2** token enforcement on cloud instances. |
| **Web Attacks** | **Broken Access Control (#1 in OWASP Top 10 2021) / IDOR (Insecure Direct Object Reference):** Modifying a URL parameter (`/invoices/1002` $\rightarrow$ `/invoices/1003`) to view another user's record. | **Primary Fix:** Server-side object-level authorization checks on every request + indirect/UUID references. |
| **Network & Protocol Attacks** | **MITM (Man-in-the-Middle) / On-Path Attack:** Intercepting communication via **ARP Poisoning/Spoofing**, **DNS Spoofing**, **Evil Twin Wi-Fi AP**, or **SSL Stripping**. | **Primary Fix:** **Mutual TLS (mTLS), HSTS, Dynamic ARP Inspection (DAI), DNSSEC, VPN**. |
| **Availability Attacks** | **DoS / DDoS (Distributed Denial of Service):** Uses a **Botnet** of compromised zombie devices (`C2` / Command & Control server). **Volumetric** (UDP/ICMP floods, DNS/NTP amplification), **Protocol** (SYN Flood exhausting TCP half-open state table, Ping of Death, Smurf attack), **Application Layer 7** (HTTP GET/POST floods, Slowloris). | **Primary Fix:** **SYN Cookies**, Rate Limiting, Anycast **CDN / DDoS Scrubbing Centers** (Cloudflare, AWS Shield), **WAF**. |
| **Memory & System Vulnerabilities** | **Buffer Overflow** (Stack/Heap overflow overwriting return pointer), **Race Condition / TOCTOU (Time-of-Check to Time-of-Use)**, **Zero-Day Vulnerability** (unpatched flaw exploited before vendor has a patch — 0 days to fix), **Supply Chain Attack** (SolarWinds, compromised npm/PyPI dependencies), **APT (Advanced Persistent Threat)** (stealthy, state-sponsored, long-term intrusion). | **Primary Fix:** **ASLR (Address Space Layout Randomization), DEP/NX bit (Data Execution Prevention)**, Bounds Checking, SBOM (Software Bill of Materials), Threat Hunting. |

* **Vulnerability Identification Standards (MITRE & NIST):**
  * **CVE (Common Vulnerabilities and Exposures):** Public dictionary assigning unique IDs (`CVE-YYYY-NNNNN`) to publicly known cybersecurity vulnerabilities.
  * **CWE (Common Weakness Enumeration):** Categorizes **types of software/hardware weaknesses** (e.g., `CWE-89` is SQL Injection, `CWE-79` is XSS).
  * **CVSS (Common Vulnerability Scoring System):** Numerical severity score from **`0.0` to `10.0`** (`None: 0.0`, `Low: 0.1–3.9`, `Medium: 4.0–6.9`, `High: 7.0–8.9`, **`Critical: 9.0–10.0`**) based on **Base, Temporal/Threat, and Environmental** metric groups.

## 4.2 Network Security & Key Components (Syllabus Section 4(i))

### 1. Securing Network Environments: Wi-Fi, LAN, VLAN, Corporate LANs & Cloud
* **Wi-Fi (IEEE 802.11) Wireless Security Evolution (Classic MCQ):**
| Standard | Encryption Protocol / Cipher | Integrity Check | Key Exam Facts |
| :--- | :--- | :--- | :--- |
| **WEP** (1999) | **RC4** stream cipher (24-bit IV) | CRC-32 | **Deprecated / Easily Cracked** in minutes due to short 24-bit Initialization Vector (IV) reuse. |
| **WPA** (2003) | **TKIP** (Temporal Key Integrity Protocol — wraps RC4) | **MIC** (Michael) | Interim software-compatible upgrade over WEP; now deprecated. |
| **WPA2** (IEEE **802.11i**, 2004) | **AES-CCMP** (Counter Mode with Cipher Block Chaining MAC) | CCMP (CBC-MAC) | Industry standard for years; vulnerable to **KRACK (Key Reinstallation Attack)** on 4-way handshake and offline dictionary attacks in PSK mode. |
| **WPA3** (2018–Present) | **AES-GCMP-256** (Enterprise 192-bit mode) / CCMP-128 | GMAC / CMAC | Replaces Pre-Shared Key (PSK) with **SAE (Simultaneous Authentication of Equals — "Dragonfly" handshake)** providing **Forward Secrecy** and resistance to offline dictionary attacks + **PMF (Protected Management Frames — 802.11w)** mandatory. |

  * **Personal (PSK/SAE) vs. Enterprise (`802.1X` / EAP) Mode:** WPA2/WPA3-Enterprise uses **IEEE 802.1X** port-based authentication with a **Supplicant** (client), **Authenticator** (Access Point/Switch), and **Authentication Server** (**RADIUS** server using **EAP-TLS**, **PEAP**, or **EAP-TTLS**).
* **LAN, VLAN & Corporate LAN Security:**
  * **VLAN (Virtual Local Area Network — IEEE `802.1Q`):** Logically segments a physical Layer 2 switch into multiple isolated **Broadcast Domains** by inserting a **4-byte (32-bit) 802.1Q tag** (containing a **12-bit VLAN ID** supporting up to **4,094 usable VLANs**) into Ethernet frames across **Trunk Ports**.
  * **Layer 2 LAN Attacks & Switch Countermeasures:**
    * **CAM / MAC Table Overflow Attack** (floods switch with fake source MACs so switch fails open and broadcasts like a dumb hub) $\rightarrow$ Mitigated by **Port Security** (sticky MACs, max MAC count).
    * **VLAN Hopping (Switch Spoofing & Double Tagging):** Attacker negotiates a trunk via DTP or double-tags frames on the Native VLAN $\rightarrow$ Mitigated by **disabling DTP (`switchport mode access`)**, changing the **Native VLAN** from default `VLAN 1` to an unused VLAN ID, and explicitly pruning allowed VLANs.
    * **DHCP Starvation & Rogue DHCP Server** $\rightarrow$ Mitigated by **DHCP Snooping** (distinguishes Trusted vs. Untrusted ports).
    * **ARP Spoofing / Poisoning** $\rightarrow$ Mitigated by **DAI (Dynamic ARP Inspection)** validating ARP packets against the DHCP Snooping binding database.
    * **STP (Spanning Tree Protocol) Manipulation** $\rightarrow$ Mitigated by **BPDU Guard** and **Root Guard**.

### 2. Key Network Security Components (Explicitly Listed in Syllabus)

#### (a) Firewalls: Regulate Network Traffic Based on Security Policies
| Firewall Type | OSI Layer | How It Works & Exam Facts |
| :--- | :--- | :--- |
| **Stateless Packet-Filtering Firewall** | Layer 3 (Network) & Layer 4 (Transport) | Inspects each packet **in isolation** using static **ACLs** (Source/Dest IP, Source/Dest Port, Protocol TCP/UDP/ICMP); no memory of connection state; vulnerable to spoofed ACK packets. |
| **Stateful Inspection Firewall** | Layer 3 & Layer 4 (tracks session state) | Maintains a **State Table (Connection Table)** tracking active TCP 3-way handshakes (`SYN` $\rightarrow$ `SYN-ACK` $\rightarrow$ `ACK`) and UDP pseudo-sessions; dynamically allows return traffic only if it matches an established outbound session. |
| **Circuit-Level Gateway** | Layer 5 (Session) | Validates TCP/UDP session handshakes (e.g., **SOCKS proxy**) without deep payload inspection. |
| **Application-Level Gateway (Proxy Firewall) / WAF** | **Layer 7 (Application)** | Inspects actual application protocol payloads (**HTTP/HTTPS, DNS, FTP**); **WAF (Web Application Firewall)** specifically protects web apps against **SQLi, XSS, CSRF, and L7 DDoS**. |
| **NGFW (Next-Generation Firewall)** | **Layers 3 to 7** | Combines **Stateful Inspection** + **Deep Packet Inspection (DPI)** + **Integrated IPS** + **Application Awareness** (identifies apps regardless of port) + **User Identity Awareness** (Active Directory integration) + **SSL/TLS Decryption/Inspection** + **Threat Intelligence feeds**. |

*(Exam Rule: Firewall ACLs are evaluated **top-down (first match wins)** and always end with an **Implicit Deny All** rule at the bottom!)*

#### (b) VPNs (Virtual Private Networks): Encrypt Data for Secure Communication Over Public Networks
* **Core Tunneling Protocols:**
  1. **IPSec (Internet Protocol Security — Layer 3 Network Layer):**
     * **2 Key Protocols:**
       * **AH (Authentication Header — IP Protocol `51`):** Provides **Authentication, Integrity, and Anti-Replay**, but **NO Confidentiality (NO Encryption)**; hashes the entire packet including immutable outer IP headers (incompatible with **NAT**).
       * **ESP (Encapsulating Security Payload — IP Protocol `50`):** Provides **Confidentiality (Encryption) + Authentication + Integrity + Anti-Replay** (supports **NAT-T** over UDP Port `4500`).
     * **2 Operating Modes:**
       * **Transport Mode:** Encrypts **only the IP payload** (TCP/UDP header + data), leaving the **original IP header intact** (used for **Host-to-Host** or inside GRE tunnels).
       * **Tunnel Mode:** Encrypts the **entire original IP packet** (header + payload) and encapsulates it inside a **brand-new outer IP header** (used for **Site-to-Site VPN gateways**).
     * **IKE (Internet Key Exchange — UDP Port `500` / `4500`):** Negotiates **Security Associations (SAs)** in two phases: **Phase 1** establishes a secure management tunnel (ISAKMP SA via Diffie-Hellman), and **Phase 2** establishes the unidirectional IPSec SAs for actual data transfer.
  2. **SSL/TLS VPN (Layer 4–7, e.g., OpenVPN, Cisco AnyConnect) & WireGuard:**
     * Ideal for **Remote Access (Client-to-Site)** from user laptops/phones over standard TCP/UDP ports (like **TCP `443`**), bypassing restrictive hotel/public Wi-Fi firewalls.
* **Split Tunneling vs. Full Tunneling:**
  * **Full Tunneling:** **All** client traffic (both corporate and public internet browsing) is routed through the corporate VPN tunnel for inspection (maximizes security, consumes corporate bandwidth).
  * **Split Tunneling:** Only traffic destined for **corporate subnets** goes through the VPN tunnel, while general internet traffic goes directly out the user's local ISP (saves bandwidth, but exposes the endpoint to direct internet threats while connected to corporate LAN).

#### (c) IDS / IPS: Detect and Block Suspicious Network Activity
| Attribute | **IDS (Intrusion Detection System)** | **IPS (Intrusion Prevention System)** |
| :--- | :--- | :--- |
| **Primary Action** | **Passive Monitoring & Alerting** (Detects and logs; does not block inline) | **Active Inline Blocking** (Detects and **drops malicious packets** in real time) |
| **Network Deployment** | **Out-of-Band (Promiscuous Mode)** connected via a **Switch SPAN / Mirror Port** or **Network TAP** | **Inline** directly in the traffic path (between router/firewall and internal switch) |
| **Failure Impact** | If IDS crashes, network traffic continues unaffected (may miss an already-forwarded initial packet) | Must support hardware bypass (**Fail-Open** vs **Fail-Closed**) so an IPS crash doesn't sever connectivity |

* **Deployment Scope:** **NIDS / NIPS** (Network-based — monitors subnet traffic) vs. **HIDS / HIPS** (Host-based — installed on individual servers/endpoints monitoring OS logs, system calls, and file integrity, e.g., OSSEC, Wazuh).
* **Detection Methodologies (Classic MCQ):**
  1. **Signature-Based (Knowledge-Based / Misuse Detection):** Matches traffic against a database of known attack patterns/hashes (e.g., **Snort, Suricata** rules). **Low False Positives**, but **cannot detect Zero-Day (unknown) attacks** (**High False Negatives** for novel attacks).
  2. **Anomaly-Based (Behavior-Based / Heuristic / Statistical Detection):** Builds a baseline of "normal" network/user behavior and alerts on statistical deviations. **Can detect Zero-Day attacks**, but suffers from **Higher False Positives** if baseline changes legitimately.
* **Alert Confusion Matrix:**
  * **True Positive (TP):** Attack occurs $\rightarrow$ Alert triggered (Correct detection).
  * **False Positive (FP — Type I Error):** Benign traffic $\rightarrow$ Alert triggered (False alarm causing **Alert Fatigue**).
  * **False Negative (FN — Type II Error — Most Dangerous!):** Attack occurs $\rightarrow$ **No alert triggered** (Missed intrusion).
  * **True Negative (TN):** Benign traffic $\rightarrow$ No alert triggered.

#### (d) Encryption in Transit (HTTPS, TLS 1.2 / TLS 1.3)
* **TLS (Transport Layer Security — Successor to deprecated SSL 2.0/3.0):**
  * **HTTPS = HTTP over TLS (Default TCP Port `443`)**.
  * **Hybrid Cryptography in TLS:** Uses **Asymmetric Cryptography** (RSA, ECDHE) during the **TLS Handshake** to authenticate the server's **X.509 Digital Certificate** and securely exchange/derive shared keys, then switches to fast **Symmetric Authenticated Encryption (AEAD)** (**AES-GCM** or **ChaCha20-Poly1305**) for bulk session data transfer!
  * **TLS 1.3 Improvements over TLS 1.2 (RFC 8446):**
    * Reduces handshake latency from **2-RTT (Round-Trip Times)** in TLS 1.2 to **1-RTT** (and supports **0-RTT** session resumption).
    * **Mandates Perfect Forward Secrecy (PFS)** by removing static RSA key transport and requiring **ephemeral Diffie-Hellman (`ECDHE` / `DHE`)** — ensuring that even if a server's long-term private key is stolen years later, past recorded TLS sessions **cannot be decrypted**.
    * Removes legacy weak ciphers (RC4, DES, 3DES, MD5, SHA-1, CBC-mode ciphers vulnerable to POODLE/BEAST).
  * **HSTS (HTTP Strict Transport Security):** Response header (`Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`) instructing browsers to **only** connect via **HTTPS**, preventing **SSL Stripping** downgrade attacks.

#### (e) Network Segmentation: Limits Access by Dividing Networks to Contain Threats
* **Purpose:** Reduces the **Attack Surface**, contains **Lateral Movement (East-West propagation)** of ransomware/attackers, shrinks compliance scope (e.g., isolating cardholder data environment for PCI-DSS), and improves performance.
* **Implementation Techniques:**
  1. **Physical Segmentation:** Completely separate switches, cabling, and **Air-Gapped** networks (used in ICS/SCADA/OT nuclear and defense systems).
  2. **Logical Segmentation (Subnetting & VLANs):** Dividing IP address spaces using CIDR subnet masks and Layer 2 **`802.1Q` VLANs**, routing inter-VLAN traffic through an internal firewall.
  3. **DMZ (Demilitarized Zone / Screened Subnet):** A perimeter buffer network sitting between the untrusted public Internet and the trusted internal Corporate LAN (often using a **Dual-Firewall** or **Three-Legged Firewall** architecture). Public-facing servers (**Web, Reverse Proxy, Email Relay, Public DNS**) reside in the DMZ; **Internal Databases and Active Directory Domain Controllers MUST NEVER be placed directly in the DMZ!**
  4. **Zero-Trust Microsegmentation (SDN / Host-based):** Enforces granular workload-to-workload policies down to individual VMs, containers, or processes (even on the same VLAN/subnet) so Web Server A can talk to App Server B on port 8443, but cannot SSH or SMB into Web Server C.

#### (f) Endpoint Security: Safeguarding User Devices
* **Scope:** Protects laptops, desktops, mobile phones, IoT devices, and servers representing the network perimeter edge.
* **Core Endpoint Security Technologies:**
  * **Antivirus (AV) $\rightarrow$ EPP (Endpoint Protection Platform):** Preventative suite combining signature AV, next-gen ML behavioral anti-malware, personal host firewall, device control (blocking unauthorized USB ports), and full-disk encryption (**BitLocker / FileVault / LUKS**).
  * **EDR (Endpoint Detection and Response):** Continuous real-time flight-recorder telemetry on endpoints; detects behavioral anomalies, visualizes attack kill-chain process trees, and enables **remote host isolation (quarantine)** and remediation.
  * **XDR (Extended Detection and Response):** Extends EDR by correlating telemetry across **Endpoints + Network + Cloud + Email + Identity** in a unified platform.
  * **MDM (Mobile Device Management) / UEM (Unified Endpoint Management):** Enforces corporate policies on BYOD and corporate mobile devices (**Containerization/Work Profiles, Remote Wipe, Enforced Passcodes, Jailbreak/Root Detection**).
  * **Hardening & Patch Management:** Disabling unused services/ports, removing local admin rights, enforcing **Secure Boot & TPM (Trusted Platform Module)** hardware root of trust, and automated OS/application patching.

#### (g) Cloud Security: Ensuring Security in Cloud Environments
* **The Cloud Shared Responsibility Model (Critical Exam Concept):**
| Responsibility Layer | **On-Premises** | **IaaS** (EC2, Azure VM) | **PaaS** (RDS, App Engine) | **SaaS** (Microsoft 365, Salesforce) |
| :--- | :--- | :--- | :--- | :--- |
| **Data, Classification, Identities (IAM), Endpoints** | **Customer** | **Customer** | **Customer** | **Customer** *(Always Customer!)* |
| **Application Code & Logic** | Customer | **Customer** | **Customer** | Cloud Provider |
| **Guest OS, Patching, Host Firewall** | Customer | **Customer** | Cloud Provider | Cloud Provider |
| **Hypervisor, Physical Network, Storage, Data Center** | Customer | **Cloud Provider** | **Cloud Provider** | **Cloud Provider** |

  * *(AWS Mnemonic: The **Cloud Provider** is responsible for **Security OF the Cloud**; the **Customer** is responsible for **Security IN the Cloud**).*
* **Cloud Security Acronym Toolkit (CNAPP Stack):**
  * **CSPM (Cloud Security Posture Management):** Continuously scans cloud configurations to detect misconfigurations (e.g., **publicly exposed S3 buckets**, overly permissive Security Groups `0.0.0.0/0` on port 22/3389, unencrypted EBS volumes) and checks compliance (CIS Benchmarks, NIST).
  * **CWPP (Cloud Workload Protection Platform):** Protects runtime workloads (VMs, containers, serverless functions) against vulnerabilities and runtime exploits.
  * **CASB (Cloud Access Security Broker):** Policy enforcement point placed between cloud consumers and SaaS/cloud providers to discover **Shadow IT**, enforce **DLP (Data Loss Prevention)**, and control access.
  * **CIEM (Cloud Infrastructure Entitlement Management):** Manages cloud identity permissions and enforces least privilege across complex IAM roles.
  * **CNAPP (Cloud-Native Application Protection Platform):** Unified platform combining **CSPM + CWPP + CIEM + IaC Scanning**.

## 4.3 Common Elements in Data Protection Frameworks (Syllabus Section 4(ii))

### 1. Data Classification
* **Definition:** Organizing data based on its **level of sensitivity, business value, and regulatory impact**, and applying appropriate security controls to each tier throughout the data lifecycle (**Create $\rightarrow$ Store $\rightarrow$ Use $\rightarrow$ Share $\rightarrow$ Archive $\rightarrow$ Destroy**).
* **Standard 4-Tier Classification Model (Government vs. Commercial):**
| Commercial Tier | Government / Military Equivalent | Description & Examples | Protection Measures Required |
| :--- | :--- | :--- | :--- |
| **1. Restricted / Highly Confidential** | **Top Secret** | Severe/irreparable harm or statutory violation if disclosed (**PII, PHI, PCI cardholder data, Trade Secrets, M&A plans, Cryptographic Private Keys**) | Strong encryption at rest & in transit, strict **Need-to-Know + MFA + PAM**, DLP blocking, full audit logging, zero external sharing. |
| **2. Confidential / Sensitive** | **Secret** | Significant financial, legal, or competitive harm (**Employee salaries, customer contracts, source code, unreleased financials**) | Encryption, Role-Based Access Control (RBAC), NDA for third parties. |
| **3. Internal Use Only / Private** | **Confidential / Controlled Unclassified (CUI)** | Minor operational disruption (**Internal memos, org charts, internal wikis, IT policies**) | Authenticated employee login required; not indexed publicly. |
| **4. Public** | **Unclassified** | Zero harm from disclosure; approved for public release (**Marketing brochures, public press releases, published job postings**) | Integrity controls (prevent website defacement) and availability. |

* **Data Roles in Governance (High-Yield Exam Distinction):**
  * **Data Owner:** Senior executive/business unit leader with **ultimate legal and business accountability** who determines the data's **classification level** and approves access policies.
  * **Data Custodian:** **IT / Security / Database Administrator** responsible for the **day-to-day technical implementation** of controls (configuring backups, applying encryption, setting ACLs) as directed by the Data Owner.
  * **Data Steward:** Manages data quality, metadata cataloging, and business glossary standards.
  * **Data Controller vs. Data Processor (GDPR) / Data Fiduciary vs. Data Processor (India DPDP Act):**
    * **Controller / Data Fiduciary:** Determines the **purposes and means ("Why and How")** of processing personal data.
    * **Processor:** Processes personal data **on behalf of** the Controller/Fiduciary (e.g., a cloud payroll vendor).

### 2. Access Controls
* **Access Control Models Comparison (Classic MCQ Table):**
| Model | Full Name | How Access Decisions Are Made | Key Example / Exam Facts |
| :--- | :--- | :--- | :--- |
| **DAC** | **Discretionary Access Control** | Resource **Owner** has discretion to grant/revoke permissions to other users via **ACLs**. | Standard **UNIX/Linux (`chmod rwx`)** and **Windows NTFS file permissions**; flexible but vulnerable to Trojan Horse permission inheritance. |
| **MAC** | **Mandatory Access Control** | Centrally enforced by the OS kernel using **Security Labels**: compares user's **Clearance Level** against object's **Classification Label** + **Compartments**. Users **cannot** alter permissions even on files they created. | Military/Defense systems, **SELinux**, **AppArmor**; mathematical models: **Bell-LaPadula** (Confidentiality: *"No Read Up, No Write Down"*) & **Biba** (Integrity: *"No Read Down, No Write Up"*). |
| **RBAC** | **Role-Based Access Control** (Explicitly highlighted in Syllabus) | Permissions are assigned to job **Roles** (e.g., `HR_Manager`, `Auditor`, `DBA`), and users are assigned to Roles (**Non-discretionary**). | Prevents permission sprawl, enforces **Separation of Duties (SoD)** and **Least Privilege**, simplifies onboarding/offboarding (**Role Creep** prevented via periodic Access Reviews). |
| **ABAC** | **Attribute-Based Access Control** | Evaluates dynamic boolean policies combining **Subject Attributes** (department, clearance), **Resource Attributes** (sensitivity, owner), **Action**, and **Environmental/Context Attributes** (time of day, IP location, device posture). | Cloud IAM policies (AWS IAM condition keys, **XACML** standard); fine-grained and context-aware. |
| **RuBAC** | **Rule-Based Access Control** | Access determined by a system-wide set of deterministic **`IF-THEN` rules** defined by an administrator regardless of identity. | **Firewall ACL rules** or time-of-day router restrictions. |

* **Identity & Access Management (IAM) Protocols:**
  * **Kerberos (TCP/UDP Port `88`):** Ticket-based symmetric authentication protocol used in **Active Directory** comprising **KDC (Key Distribution Center)** = **AS (Authentication Server)** (issues **TGT — Ticket Granting Ticket**) + **TGS (Ticket Granting Server)** (issues Service Ticket); relies on synchronized clocks (within 5 minutes via **NTP**) to prevent replay attacks.
  * **LDAP / LDAPS (Ports `389` / `636`):** Directory service protocol for querying hierarchical organizational user objects (`DC=`, `OU=`, `CN=`).
  * **SAML 2.0 (Security Assertion Markup Language):** XML-based enterprise **Federated Single Sign-On (SSO)** standard exchanging signed assertions between an **Identity Provider (IdP)** and a **Service Provider (SP)**.

### 3. Encryption: Securing Data at Rest, in Transit & in Use
* **3 States of Data & Their Encryption Mechanisms:**
  1. **Data at Rest (Stored on disks, databases, backups, cloud storage):**
     * **Full Disk Encryption (FDE) / Self-Encrypting Drives (SED):** **BitLocker** (Windows), **FileVault** (macOS), **LUKS / dm-crypt** (Linux) tied to **TPM 2.0**.
     * **Database Encryption:** **TDE (Transparent Data Encryption)** (encrypts database data files and transaction logs at page level), **Column-Level / Field-Level Encryption**, **Client-Side Always Encrypted**.
     * **Cloud Storage Encryption:** Server-Side Encryption (**SSE-S3 / SSE-KMS / SSE-C**) using **Envelope Encryption** (encrypting data with a unique **DEK — Data Encryption Key**, and encrypting the DEK with a master **KEK — Key Encryption Key** stored inside a FIPS 140-3 **HSM — Hardware Security Module** or **Cloud KMS**).
  2. **Data in Transit (Moving across networks):** **TLS 1.2 / 1.3**, **HTTPS**, **IPSec**, **SSH (Port `22`)**, **SFTP**, **WPA3**.
  3. **Data in Use (Being processed in RAM/CPU):** **Confidential Computing**, **Trusted Execution Environments (TEEs / Secure Enclaves — Intel SGX, AMD SEV, AWS Nitro Enclaves)**, and **Homomorphic Encryption** (allows mathematical computations directly on ciphertext without decrypting it first!).

* **Symmetric vs. Asymmetric Encryption vs. Cryptographic Hashing:**
| Property | **Symmetric Encryption (Secret-Key)** | **Asymmetric Encryption (Public-Key)** | **Cryptographic Hashing (One-Way)** |
| :--- | :--- | :--- | :--- |
| **Keys Used** | **1 Shared Secret Key** (same key encrypts and decrypts) | **2 Mathematically Linked Keys** (**Public Key** + **Private Key**) | **0 Keys** (unless **HMAC** keyed hash) |
| **Speed** | **Very Fast** (hardware accelerated; used for bulk data at rest & in transit) | **Slow** (computationally expensive; used for key exchange & digital signatures) | **Extremely Fast** |
| **Key Distribution Scaling** |Requires $\frac{n(n-1)}{2}$ keys for $n$ parties (Key exchange problem) | Requires only $2n$ keys ($n$ key pairs); public keys shared openly | N/A (One-way irreversible digest) |
| **Key Algorithms** | **AES** (128, 192, 256-bit block cipher — Rijndael), **ChaCha20** (stream cipher), **3DES** (168-bit, legacy), **DES** (56-bit, insecure), Blowfish, Twofish | **RSA** (2048/4096-bit — integer factorization), **ECC / ECDSA / ECDH** (Elliptic Curve — **smaller keys with equivalent security**, e.g., 256-bit ECC $\approx$ 3072-bit RSA), **Diffie-Hellman (DH)**, ElGamal | **SHA-2** (`SHA-256`, `SHA-512`), **SHA-3 (Keccak)**; Password hashing: **Argon2id**, **bcrypt**, **PBKDF2** (with unique random **Salt** to defeat **Rainbow Tables**). *(Note: MD5 128-bit and SHA-1 160-bit are broken due to collision attacks).* |

* **Asymmetric Key Usage Rule (Never Miss This on an Exam!):**
  * **For Confidentiality (Encryption):** Sender encrypts with **Receiver's PUBLIC Key** $\rightarrow$ Only Receiver can decrypt with **Receiver's PRIVATE Key**.
  * **For Authentication, Integrity & Non-Repudiation (Digital Signature):** Sender signs (encrypts the message hash) with **Sender's PRIVATE Key** $\rightarrow$ Anyone can verify using **Sender's PUBLIC Key** (certified by a **CA — Certificate Authority** in an **X.509** certificate; revocation checked via **CRL** or **OCSP**).

### 4. Data Minimization & Privacy-Enhancing Techniques
* **Data Minimization Principle:** Collecting, processing, and retaining **only the minimum amount of personal data strictly necessary** for a specific, explicit, legitimate purpose (**Purpose Limitation**) and deleting or anonymizing it once that purpose is fulfilled (**Storage Limitation**).
* **De-Identification & Privacy Techniques:**
  * **Data Masking (Obfuscation):** Replacing sensitive characters with symbols (`XXXX-XXXX-XXXX-1234`) for UI display or non-production QA/testing environments (Static vs. Dynamic Data Masking).
  * **Tokenization:** Replacing a sensitive data element (e.g., a 16-digit PAN credit card number) with a **non-sensitive random surrogate string (Token)** that has **no mathematical relationship** to the original data; the mapping is stored securely in a **Token Vault** (unlike encryption, a token cannot be reversed via an algorithm/key).
  * **Pseudonymization vs. Anonymization:**
    * **Pseudonymization:** Replaces direct identifiers with an alias/pseudonym; **reversible** if combined with the kept mapping table (**still regulated as Personal Data under GDPR/DPDP**).
    * **Anonymization:** **Irreversibly** strips all direct and indirect identifiers so the individual can never be re-identified (**falls outside GDPR/DPDP scope**).
  * **Statistical Privacy Models:**
    * **$k$-Anonymity (Sweeney):** Every combination of **quasi-identifiers** (e.g., ZIP code + Birth Date + Gender) appears in at least **$k$ indistinguishable records** in the dataset. (Vulnerable to *Homogeneity Attack* if all $k$ records share the same sensitive disease).
    * **$l$-Diversity:** Extends $k$-anonymity by requiring at least **$l$ well-represented distinct values** for the sensitive attribute within each equivalence class.
    * **$t$-Closeness:** Requires the distribution of a sensitive attribute in any equivalence class to be within distance **$t$** of its overall distribution in the table.
    * **Differential Privacy ($\epsilon$-Differential Privacy):** Adds calibrated mathematical noise (Laplace or Gaussian) to query outputs or model gradients so that the presence or absence of any single individual's record does not noticeably change the output probability (quantified by privacy budget **$\epsilon$**).

### 5. Windowing & Types of Windows (Explicitly Featured in Syllabus Section 4(ii))
* **Definition:** Method of dividing continuous, unbounded data streams or large datasets into smaller, manageable finite chunks called **"windows"** for processing, aggregation, or anomaly analysis over specific **time intervals** or **data counts**. Enables efficient **real-time data analysis** and **data minimization** (retaining only active window state in memory rather than the entire infinite stream).

#### Complete Comparison of the 4 Syllabus Window Types (Guaranteed MCQ/MSQ/Fill-in-the-Blank Topic)
| Window Type | Syllabus Definition | Overlap? | Window Size | Key Trigger / Mechanism & Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **1. Tumbling Window** | **Fixed, non-overlapping intervals.** | **No Overlap** (Disjoint / Contiguous) | Fixed time duration ($W$) | Every event belongs to **exactly ONE** window. When window $[0\text{s}, 60\text{s})$ closes, the next window $[60\text{s}, 120\text{s})$ immediately starts (`Slide = Size`). **Example:** Counting total failed logins per minute every 1 minute. |
| **2. Sliding Window** *(also called Hopping Window)* | **Overlapping intervals that move forward incrementally.** | **Overlaps** (when $\text{Slide} < \text{Size}$) | Fixed time duration ($W$) with step/hop interval ($S$) | Moves forward by a smaller increment ($S < W$), so a single event can belong to **multiple overlapping windows** ($\frac{W}{S}$ windows). **Example:** Computing a 5-minute moving average of network traffic updated every 10 seconds ($W=5\text{m}, S=10\text{s}$). |
| **3. Session Window** | **Defined by periods of activity separated by inactivity.** | **No Overlap** (Per key/user) | **Dynamic / Variable length** (unfixed duration) | Driven by an **Inactivity Gap (Session Timeout $\Delta t$)**. Remains open as long as events keep arriving within $\Delta t$; closes only after a period of **inactivity** exceeding $\Delta t$. **Example:** Grouping a user's clickstream or VPN session activity until 30 minutes of idle silence. |
| **4. Count-based Window** | **Based on a fixed number of data points.** | Can be Tumbling or Sliding by count | Fixed **record count ($N$)** (independent of wall-clock time) | Triggers aggregation after collecting exactly **$N$ events/elements** regardless of how much time elapsed. **Example:** Calculating average sensor temperature or anomaly score over every **100 consecutive packets**. |

* **Essential Stream Windowing Concepts:**
  * **Event Time vs. Processing Time vs. Ingestion Time:**
    * **Event Time:** Timestamp when the event actually occurred on the originating device/sensor.
    * **Ingestion Time:** Timestamp when the event entered the message broker (e.g., Kafka/Kinesis).
    * **Processing Time:** Wall-clock time of the worker node currently executing the window transformation.
  * **Watermarks:** A monotonically increasing timestamp threshold $W(t)$ in Event-Time processing declaring that the stream engine assumes *"no more events with timestamp $< W(t)$ will arrive"*, allowing windows to close and emit results while handling bounded **Out-of-Order / Late-Arriving Data**.

### 6. Audit and Monitoring
* **Definition:** Continuously tracking, recording, and reviewing data access, user actions, and system events to detect anomalies, policy violations, insider threats, or misuse.
* **Key Technologies:**
  * **SIEM (Security Information and Event Management — e.g., Splunk, Microsoft Sentinel, IBM QRadar, Elastic SIEM):** Centralizes **Log Aggregation, Normalization, Correlation Rules, Real-Time Alerting, Forensic Search, and Compliance Reporting** across firewalls, endpoints, AD, and databases.
  * **SOAR (Security Orchestration, Automation, and Response):** Integrates with SIEM to automatically execute **Incident Response Playbooks / Runbooks** (e.g., automatically querying VirusTotal, blocking a malicious IP on the firewall, and disabling a compromised AD account without human delay).
  * **UEBA (User and Entity Behavior Analytics):** Uses Machine Learning to baseline normal user/service behavior and detect subtle threats like **compromised credentials, privilege abuse, and insider data exfiltration** (e.g., "Impossible Travel" login from India and USA within 10 minutes).
  * **FIM (File Integrity Monitoring — e.g., Tripwire, Wazuh):** Computes cryptographic hashes of critical OS and configuration files (`/etc/passwd`, `/etc/shadow`, Windows `System32`) to detect unauthorized tampering.
  * **Audit Log Protection (WORM):** Audit logs must be **time-synchronized via NTP**, access-restricted, and stored on **Write-Once-Read-Many (WORM)** or immutable remote syslog/cloud storage so attackers cannot erase their tracks.

### 7. Incident Response (IR)
* **Definition:** Establishing clear, repeatable organizational procedures, roles (**CSIRT / CERT / SOC**), and playbooks to quickly detect, contain, address, and mitigate data breaches or cyberattacks.

#### NIST SP 800-61 (4 Phases) vs. SANS / PICERL (6 Phases) Incident Response Lifecycle
| **NIST SP 800-61 Phase (4 Steps)** | **SANS / PICERL Equivalent (6 Steps)** | Key Activities Performed in This Phase |
| :--- | :--- | :--- |
| **1. Preparation** | **1. Preparation** | Drafting IR policies & playbooks, forming CSIRT, deploying SIEM/EDR tools, conducting tabletop exercises, maintaining contact lists & jump kits. |
| **2. Detection and Analysis** | **2. Identification** | Monitoring alerts, validating indicators of compromise (**IoCs** vs **IoAs**), triaging severity, declaring an official incident, documenting initial timeline. |
| **3. Containment, Eradication, and Recovery** | **3. Containment** | **Short-term containment:** Isolating infected host from network (VLAN quarantine / EDR isolation) while **preserving volatile RAM forensics** (*Never simply pull the power cord if memory forensics is needed!*). **Long-term containment:** Applying temporary firewall blocks/DNS sinkholes. |
| *(Part of NIST Phase 3)* | **4. Eradication** | Finding and eliminating the **root cause**: removing malware/backdoors, patching exploited vulnerabilities, resetting compromised credentials. |
| *(Part of NIST Phase 3)* | **5. Recovery** | Restoring systems from **clean, verified backups**, bringing services back online incrementally, and conducting heightened validation monitoring. |
| **4. Post-Incident Activity** | **6. Lessons Learned** | Holding a **blameless post-mortem / root-cause analysis (RCA)** within days of recovery, updating SIEM detection rules/playbooks, calculating metrics, and filing final regulatory reports. |

* **Digital Forensics Order of Volatility (RFC 3227 — Collect Most Volatile First!):**
  1. **CPU Registers & L1/L2/L3 Cache** (Nanoseconds)
  2. **RAM / Main Memory, Routing Tables, ARP Cache, Running Processes, Active Network Connections**
  3. **Swap Space / Paging File on Disk**
  4. **Hard Disk / SSD Persistent Storage**
  5. **Remote Logs & Monitoring Data**
  6. **Archival Backups / Offline Optical/Tape Media** (Least Volatile)
* **Chain of Custody:** Chronological legal documentation tracking **who** collected, handled, transferred, and stored forensic evidence, **when**, and **why**, verified with cryptographic hashes (`SHA-256`) to prove admissibility in court.

### 8. Data Subject Rights & Data Protection Practices/Policies

#### Core Data Subject Rights (GDPR Chapter 3 & India DPDP Act 2023)
| Data Subject Right | Meaning & Regulatory Obligation |
| :--- | :--- |
| **1. Right to Be Informed (Transparency)** | Right to clear, plain-language notice at collection time explaining what data is collected, why, retention period, and who it is shared with. |
| **2. Right of Access (DSAR — Data Subject Access Request)** | Right to confirm whether personal data is being processed and obtain a **copy of one's personal data** and processing summary. |
| **3. Right to Rectification (Correction)** | Right to have inaccurate, outdated, or incomplete personal data **corrected or updated** promptly. |
| **4. Right to Erasure ("Right to be Forgotten")** | Right to request **deletion/erasure** of personal data when it is no longer needed for its original purpose or when consent is withdrawn (subject to legal retention exceptions). |
| **5. Right to Restrict Processing** | Right to temporarily halt active processing of data (storage only) while accuracy or lawfulness is being contested. |
| **6. Right to Data Portability** | Right to receive one's personal data in a **structured, commonly used, machine-readable format (e.g., JSON, CSV, XML)** and transmit it to another controller. |
| **7. Right to Object & Automated Decision-Making Rights** | Right to opt out of direct marketing/profiling and **not be subject to purely automated decisions (AI/ML profiling)** producing legal or similarly significant effects without human intervention. |
| **8. Additional Rights under India's DPDP Act, 2023** | **Right to Grievance Redressal** + **Right to Nominate** (nominating another individual to exercise rights in the event of death or incapacity of the **Data Principal**). |

#### Key Regulatory Frameworks & Breach Timelines
* **GDPR (EU General Data Protection Regulation):**
  * Mandatory breach notification to the Supervisory Authority within **72 hours** of becoming aware of a personal data breach.
  * Maximum penalty: Up to **€20 Million** or **4% of global annual turnover** (whichever is higher).
* **India DPDP Act, 2023 (Digital Personal Data Protection Act) & CERT-In Directions:**
  * Uses terms **Data Principal** (individual), **Data Fiduciary** (entity determining purpose/means), **Significant Data Fiduciary (SDF)** (must appoint an India-based **Data Protection Officer [DPO]** and independent Data Auditor, and conduct **DPIA — Data Protection Impact Assessments**), and **Consent Manager**.
  * Penalties up to **₹250 Crore** by the Data Protection Board of India for failure to maintain reasonable security safeguards preventing a data breach.
  * **CERT-In Directive (India):** Mandates reporting specified cybersecurity incidents to **CERT-In within 6 hours** of noticing/being brought to notice, and retaining ICT system logs securely within India for a rolling period of **180 days**.
* **Other Global Standards:** **ISO/IEC 27001** (ISMS — Information Security Management System standard), **ISO/IEC 27701** (PIMS — Privacy Information Management System), **NIST Cybersecurity Framework (CSF 2.0 — 6 Core Functions: Govern, Identify, Protect, Detect, Respond, Recover)**, **PCI-DSS** (Payment Card Industry Data Security Standard), **HIPAA** (US Healthcare PHI).
* **Data Protection Policies:** **Data Retention & Disposal Policy** (Cryptographic Erasure / Crypto-Shredding, Degaussing for magnetic media, Physical Shredding/Incineration, NIST SP 800-88 Clear/Purge/Destroy), **Acceptable Use Policy (AUP)**, **Clean Desk Policy**, **BYOD Policy**, and **DLP (Data Loss Prevention)**.

## 4.4 Cybersecurity Dashboarding (Syllabus Section 4(iii))

### 1. Definition & Core Objectives of Cybersecurity Dashboarding
* **Definition:** Real-time, interactive **visual interfaces** (typically powered by SIEM, SOAR, XDR, or GRC platforms) that consolidate **critical security metrics, alerts, logs, and trends** into a single pane of glass ("Single Source of Truth") to enable security teams (**SOC Analysts, CISOs, Incident Responders, Compliance Auditors**) to monitor, analyze, and respond to threats efficiently.
* **4 Strategic Objectives Explicitly Highlighted in Syllabus:**
  1. **Centralize Security Posture Visibility:** Aggregates disparate telemetry from firewalls, endpoints (EDR), cloud (CSPM), identity (IAM/AD), and vulnerability scanners into unified health scores and asset inventories.
  2. **Rapidly Identify and Prioritize Incidents:** Uses automated risk scoring, threat intelligence enrichment, and asset criticality weighting to surface high-severity incidents first and combat **alert fatigue**.
  3. **Monitor Compliance and Risk Metrics:** Continuously tracks adherence to regulatory frameworks (**ISO 27001, GDPR, DPDP, PCI-DSS, NIST CSF, CIS Controls**) via automated control pass/fail percentages and **KRIs (Key Risk Indicators)**.
  4. **Facilitate Informed Decision-Making with Actionable Insights:** Translates raw technical telemetry into operational actions for SOC analysts and strategic budget/risk posture summaries for executive leadership.

### 2. Core Dashboard Components & Visualizations (Explicitly Listed in Syllabus)
| Dashboard Element | Purpose & Best Visualization Practice |
| :--- | :--- |
| **1. Visualizations: Charts, Heat Maps, and Timelines** | • **Heat Maps:** Visualize **Risk Matrices (Likelihood $\times$ Impact)**, geographic attack origins, vulnerability density across business units, or MITRE ATT&CK technique coverage.<br>• **Timelines / Time-Series Line & Area Charts:** Track event volume spikes, attack progression sequences, and historical **MTTD/MTTR** trends over hours/days/months.<br>• **Bar / Stacked Column / Donut Charts:** Compare open vulnerabilities by CVSS severity (`Critical`, `High`, `Medium`, `Low`), top targeted assets, or phishing click rates by department. |
| **2. Real-Time Alerts and Notifications** | Live triage queue prioritized by severity (P1/Critical to P4/Low), correlated SIEM/XDR detections, threshold breach notifications, and automated **SOAR** playbook status triggers. |
| **3. Incident Tracking and Summaries** | Tracks active incidents across the lifecycle (**Open $\rightarrow$ Investigating $\rightarrow$ Contained $\rightarrow$ Eradicated $\rightarrow$ Closed**), assigned SOC owner, SLA countdown timers, affected hosts, and executive incident summaries. |
| **4. Vulnerability and Patch Status** | Tracks **Total Open Vulnerabilities by CVSS Score**, **Patch Compliance %** across OS/third-party apps, **Mean Time to Patch (MTTP)**, **SLA Overdue Vulnerabilities**, and unpatched **Zero-Day / CISA KEV (Known Exploited Vulnerabilities)** exposure. |
| **5. User Activity and Access Logs** | Monitors **Authentication Success/Failure spikes** (brute-force/password spraying), **Privileged Account (PAM/Admin) creations & escalations**, **Impossible Travel / Geo-velocity anomalies**, off-hours sensitive database queries, and **DLP** bulk download alerts. |

### 3. Essential Cybersecurity Dashboard Metrics & KPIs (High-Yield Formulas)
* **MTTD (Mean Time to Detect / Identify):** Average time taken by the security team to **discover** a security incident after it begins:
  $$\text{MTTD} = \frac{\sum (\text{Timestamp of Detection} - \text{Timestamp of Incident Start})}{\text{Total Number of Incidents}}$$
* **MTTA (Mean Time to Acknowledge):** Average time between an alert triggering and a SOC analyst beginning investigation.
* **MTTC (Mean Time to Contain):** Average time taken to isolate/contain the threat so it cannot spread laterally.
* **MTTR (Mean Time to Respond / Resolve / Recover):** Average time taken to **fully remediate and recover** from an incident after detection.
* **Dwell Time:** Total duration an attacker remains **undetected inside a victim network** from initial compromise to detection.
* **Patch Velocity / Compliance Rate:** $\frac{\text{Patched Assets within SLA}}{\text{Total Vulnerable Assets}} \times 100\%$.
* **Three Tiers of Security Dashboards:**
  1. **Operational Dashboard (Tier 1/2 SOC Analysts):** Real-time (refresh in seconds/minutes); focuses on live alerts, active incident queues, firewall blocks, and user access anomalies.
  2. **Tactical Dashboard (SOC Managers / Vulnerability Managers):** Daily/weekly trends; focuses on SLA compliance, patch backlog, MTTD/MTTR trends, and analyst workload.
  3. **Strategic / Executive Dashboard (CISO / Board of Directors):** Monthly/quarterly high-level posture; focuses on overall **Cyber Risk Score**, compliance posture %, peer benchmarking, and ROI of security investments.

## 4.5 Rapid-Fire MCQ / MSQ / Fill-in-the-Blank Pointers (Module 4)
1. **Fill in the blank:** In IPSec, **`_____`** provides authentication and integrity without encryption, whereas **`_____`** provides confidentiality (encryption) along with authentication and integrity. $\rightarrow$ **AH (Authentication Header)**, **ESP (Encapsulating Security Payload)**.
2. **Fill in the blank:** An **`_____`** is deployed out-of-band (via SPAN port/TAP) to passively detect and alert on suspicious traffic, whereas an **`_____`** is deployed inline to actively block malicious packets. $\rightarrow$ **IDS (Intrusion Detection System)**, **IPS (Intrusion Prevention System)**.
3. **Fill in the blank:** In stream data processing, a **`_____`** window divides data into fixed, non-overlapping time intervals, whereas a **`_____`** window uses overlapping intervals that move forward incrementally. $\rightarrow$ **Tumbling**, **Sliding**.
4. **Fill in the blank:** A **`_____`** window is defined dynamically by periods of user/system activity separated by a gap of inactivity, whereas a **`_____`** window is triggered by a fixed number of data points. $\rightarrow$ **Session**, **Count-based**.
5. **Fill in the blank:** In asymmetric cryptography, to ensure confidentiality, the sender encrypts a message using the **`_____`**'s **`_____`** key; to create a digital signature, the sender signs using the **`_____`**'s **`_____`** key. $\rightarrow$ **Receiver, Public**; **Sender, Private**.
6. **Fill in the blank:** In the Bell-LaPadula Mandatory Access Control (MAC) model for confidentiality, the Simple Security Property states "**`_____`**" and the *-Property (Star Property) states "**`_____`**". $\rightarrow$ **No Read Up**, **No Write Down**.
7. **Fill in the blank:** In cybersecurity dashboarding, **`_____`** visualizes two-dimensional risk matrices (likelihood vs. impact) or MITRE ATT&CK coverage using color intensity, while **`_____`** measures the average time taken to discover an intrusion. $\rightarrow$ **Heat map**, **MTTD (Mean Time to Detect)**.
