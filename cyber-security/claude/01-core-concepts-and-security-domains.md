# 01. Core Concepts and Security Domains

Subject: Cybersecurity Fundamentals. Part 1 of 5.

Each concept follows this order: a short everyday analogy, then a concrete example or trace, then Python where code helps. Code is Python 3 using only the standard library unless a `pip install` is shown. Examples use a generic company with employees, a web application, servers, and a customer database.

**Study order**

| File | Covers |
|---|---|
| 01 (this file) | What security is, domains, CIA, AAA, principles, risk, controls, threat modeling |
| 02 | Threat vectors, cyberattacks, vulnerabilities |
| 03 | Network security (firewall, VPN, IDS/IPS, encryption, segmentation, endpoint, cloud) |
| 04 | Data protection frameworks (classification, access control, windowing, audit, incident response, laws) |
| 05 | Cybersecurity dashboarding |

---

## 1. What is Cybersecurity

**Analogy.** A bank has locks, guards, cameras, a vault, and rules about who may enter which room. Cybersecurity is the same idea applied to computers, networks, software, and data.

**Definition.** Cybersecurity is the practice of protecting computers, networks, programs, and data from unauthorized access, damage, theft, or disruption.

### 1.1 Related terms

| Term | Meaning | Note |
|---|---|---|
| Information security | Protect information in any form (digital, paper, spoken) | Widest term |
| Cybersecurity | Protect digital systems and data from digital threats | Focus of this subject |
| IT security | Protect IT assets such as servers and devices | Similar to cybersecurity |
| Network security | Protect data while it moves across networks | File 03 |
| Application security | Make software free of exploitable flaws | AppSec |
| Data security | Protect the data itself (at rest, in transit, in use) | File 04 |
| Privacy | Right of a person to control how their data is used | Legal and ethical |

### 1.2 Safety vs security vs privacy

| Term | Question | Example |
|---|---|---|
| Safety | Can accidents or failures cause harm? | A faulty power supply burns out a server |
| Security | Can someone deliberately cause harm? | An attacker destroys the server's data on purpose |
| Privacy | Is personal data handled lawfully and with consent? | Customer phone numbers sold without consent |

### 1.3 What is being protected (assets)

| Asset type | Examples |
|---|---|
| Data | Customer records, source code, credentials, financial data, logs |
| Hardware | Servers, laptops, routers, phones, IoT devices |
| Software | Operating systems, web apps, databases, APIs |
| Network | Cables, Wi-Fi, VPN links, cloud connectivity |
| People | Employees, contractors, customers |
| Processes | Payment, hiring, approvals |
| Reputation and trust | Brand, regulatory standing |

### 1.4 Why it matters

* Breaches cause financial loss, legal penalties, downtime, and loss of trust.
* Attackers are organized, automated, and profit-driven (ransomware, fraud, data theft).
* Systems are connected, so one weak link (a vendor, a laptop) can expose everything.
* Laws require protection of personal data (IT Act 2000, DPDP Act 2023, GDPR).

### 1.5 Well-known incidents (awareness only)

| Incident | Lesson |
|---|---|
| Equifax 2017 | Unpatched web application flaw led to theft of data of well over 100 million people. Patch management matters |
| NotPetya 2017 | Malware spread through a compromised software update and halted many global firms. Supply chain risk and network segmentation matter |
| SolarWinds 2020 | Attackers inserted malicious code into a trusted software update. Trust in vendors can be abused |
| Colonial Pipeline 2021 | Ransomware forced a shutdown of operations. Stolen credentials without MFA and weak segmentation can halt business |
| AIIMS Delhi 2022 | Ransomware disrupted hospital IT services in India. Backups and incident readiness matter |

### 1.6 IT vs OT

| Point | IT (Information Technology) | OT (Operational Technology) |
|---|---|---|
| Examples | Email, ERP, databases | Industrial controllers, building systems, sensors |
| Top priority | Confidentiality | Availability and safety |
| Patching | Frequent | Rare and carefully planned |
| Lifespan | 3 to 5 years | 15 to 25 years |
| Risk | Data theft | Physical damage or stoppage |

> In IT the usual priority order is C, I, A. In OT it is often A, I, C.

### 1.7 Indian bodies to know

| Body | Role |
|---|---|
| CERT-In | National agency for cyber incident response (IT Act section 70B) |
| NCIIPC | Protects critical information infrastructure (section 70A) |
| MeitY | Ministry of Electronics and Information Technology |
| CCA | Controller of Certifying Authorities. Licenses certificate authorities |
| Data Protection Board of India | Adjudicates under the DPDP Act 2023 |
| NIC | National Informatics Centre. Runs government IT infrastructure |

---

## 2. Key Security Domains

A security domain is an area of security with its own goals, tools, and skills. A complete program covers all of them. A weakness in one domain can break the whole chain.

### 2.1 Overview

| # | Domain | One-line goal |
|---|---|---|
| 1 | Network security | Protect data moving across networks |
| 2 | Endpoint security | Protect user devices |
| 3 | Application security | Build and run software without exploitable flaws |
| 4 | Data security | Protect the data itself |
| 5 | Identity and access management (IAM) | Right person, right access, right time |
| 6 | Cloud security | Protect cloud workloads and data |
| 7 | Operational security (OpSec) | Protect day-to-day processes and sensitive operations |
| 8 | Physical security | Protect buildings, rooms, and hardware |
| 9 | Mobile and IoT security | Protect phones, sensors, cameras |
| 10 | OT and ICS security | Protect industrial and building control systems |
| 11 | Incident response and recovery | Handle attacks and restore service |
| 12 | Business continuity and disaster recovery (BCP and DRP) | Keep operations alive |
| 13 | Governance, risk and compliance (GRC) | Policies, risk decisions, laws |
| 14 | Security awareness and training | Humans as a defense |
| 15 | Cryptography | Math tools for secrecy and trust |
| 16 | Security operations and monitoring | Watch, detect, respond 24x7 |

### 2.2 Each domain in detail

**1. Network security.** Controls: firewall, VPN, IDS/IPS, segmentation, secure Wi-Fi, NAC. Example: the guest Wi-Fi is on a separate network and cannot reach internal servers. Full coverage in file 03.

**2. Endpoint security.** Controls: antivirus, EDR, disk encryption, patching, USB blocking, MDM. Example: a laptop is lost, but full-disk encryption keeps the data unreadable.

**3. Application security.** Controls: secure coding, input validation, code review, SAST and DAST, WAF, dependency scanning. Example: a login form uses parameterized queries so SQL injection fails.

**4. Data security.** Controls: classification, encryption, DLP, masking, backups, retention. Example: payment card numbers are tokenized so the main database never holds the real number.

**5. Identity and access management.** Controls: unique IDs, MFA, single sign-on, RBAC, privileged access management, access reviews. Example: a support agent can read tickets but cannot open the payroll system.

**6. Cloud security.** Controls: IAM policies, encryption, configuration scanning, logging, CASB. Example: a storage bucket is checked to make sure it is not public.

**7. Operational security (OpSec).** Protect small pieces of information that together reveal a plan. Example: staff do not post system diagrams, shift timings, or internal tool names publicly, because attackers use them for targeting.

**8. Physical security.** Controls: access cards, biometrics, CCTV, mantraps, server room locks, visitor logs. Example: the server room door alarms if left open.

**9. Mobile and IoT security.** Issues: default passwords, no patches, weak encryption. Example: a smart camera with the default password becomes part of a botnet.

**10. OT and ICS security.** Industrial Control Systems run physical processes. Controls: strict network separation, protocol monitoring, change control, vendor access control.

**11. Incident response (IR).** Steps: prepare, detect, contain, eradicate, recover, learn.

**12. BCP and DRP.** BCP keeps the business running. DRP restores IT after a disaster. Key terms: RTO and RPO.

**13. GRC.** Policies, risk assessments, audits, and laws such as IT Act 2000 and DPDP Act 2023.

**14. Awareness.** Most breaches involve human error. Training and phishing drills reduce risky clicks.

**15. Cryptography.** Encryption, hashing, digital signatures, certificates. File 03.

**16. Security operations.** SOC team, SIEM, threat hunting, dashboards. File 05.

### 2.3 Layers of defense (visual)

```
Policies, laws, awareness          (people and process)
  Physical security                (locks, guards, cameras)
    Perimeter / Network security   (firewall, VPN, IDS)
      Host / Endpoint security     (antivirus, EDR, patching)
        Application security       (secure code, WAF)
          Data security            (encryption, backup, DLP)
```

An attacker has to defeat every layer. This idea is called **defense in depth**.

---

## 3. The CIA Triad

The most important model in security. Every control, attack, and exam question ties back to it.

### 3.1 Confidentiality

**Analogy.** A sealed envelope. Only the addressee should open it.

**Definition.** Keeping information secret from unauthorized people.

| Threats | Controls |
|---|---|
| Data breach, eavesdropping, shoulder surfing, insider leaks, phishing | Encryption, access control, MFA, classification, DLP, need-to-know |

**Trace.**
1. The customer database holds names, phone numbers, and addresses.
2. A marketing intern tries to open the full table.
3. Access control checks the role "Intern" and denies the request.
4. Result: confidentiality kept.

### 3.2 Integrity

**Analogy.** A tamper-evident seal on a medicine bottle. If the seal is broken, you know someone touched it.

**Definition.** Data and systems stay accurate, complete, and unaltered by unauthorized or accidental changes.

| Threats | Controls |
|---|---|
| Tampering, malware, SQL injection, man-in-the-middle, human error | Hashing, digital signatures, checksums, version control, input validation, change control, write-protected logs |

**Trace.**
1. An invoice file has a stored SHA-256 hash.
2. An attacker edits the bank account number inside the file.
3. The new hash no longer matches the stored hash.
4. The system rejects the file and raises an alert.

### 3.3 Availability

**Analogy.** A tap that must give water whenever you open it.

**Definition.** Systems and data are accessible to authorized users when needed.

| Threats | Controls |
|---|---|
| DoS and DDoS, ransomware, hardware failure, power cut, disaster | Redundancy, load balancing, backups, UPS and generators, DDoS protection, patching, disaster recovery |

**Trace.**
1. Ransomware encrypts the order-processing servers.
2. Without a backup, the business stops.
3. With an offline backup and a standby site, staff restore within the RTO.

**Availability math.** Uptime % = (Total time minus Downtime) / Total time x 100.

| Uptime | Downtime per year |
|---|---|
| 99% | about 3.65 days |
| 99.9% ("three nines") | about 8.76 hours |
| 99.99% | about 52.6 minutes |
| 99.999% ("five nines") | about 5.26 minutes |

### 3.4 Trade-offs

* More secrecy often reduces convenience and sometimes availability (many approval steps slow people).
* High availability needs copies of data, and more copies means more places to protect.
* Choose the priority by asset. A public marketing page needs availability first. A customer identity database needs confidentiality first. A financial ledger needs integrity first.

### 3.5 Python demo of all three

```python
import hashlib

# CONFIDENTIALITY: role based read check
permissions = {"Support": {"read_tickets"}, "Intern": set()}

def can_read(role, action):
    return action in permissions.get(role, set())

print(can_read("Support", "read_tickets"))   # True
print(can_read("Intern", "read_tickets"))    # False

# INTEGRITY: detect change using a hash
def fingerprint(text):
    return hashlib.sha256(text.encode()).hexdigest()

original = "INV-1001|Vendor: Acme|Account: 111122223333|Amount: 50000"
stored_hash = fingerprint(original)
received = "INV-1001|Vendor: Acme|Account: 999988887777|Amount: 50000"
print("Integrity OK" if fingerprint(received) == stored_hash else "TAMPERED")

# AVAILABILITY: uptime calculation
def uptime_percent(total_minutes, downtime_minutes):
    return (total_minutes - downtime_minutes) / total_minutes * 100

year_minutes = 365 * 24 * 60
print(round(uptime_percent(year_minutes, 52.6), 3))   # about 99.99
```

---

## 4. Beyond CIA

| Concept | Meaning | Tool | Example |
|---|---|---|---|
| Authenticity | Data or user is genuine | Certificates, signatures | A software update really came from the vendor |
| Non-repudiation | Sender cannot deny sending | Digital signature, audit log | An approver cannot deny approving a payment |
| Accountability | Actions trace to a person | Unique IDs, logs | No shared admin account |
| Reliability | System works consistently | Testing, redundancy | Stable authentication service |
| Privacy | Control over personal data | Consent, minimization | A user can request deletion |

**Parkerian Hexad** adds three items to CIA: **Possession or control** (who physically holds the data, such as a stolen laptop even if encrypted), **Authenticity**, and **Utility** (data must be usable, such as an encrypted file whose key is lost).

---

## 5. Identity and the AAA Model

### 5.1 Four steps

| Step | Question | Example |
|---|---|---|
| Identification | Who do you claim to be? | Enter username "ravi" |
| Authentication | Prove it | Password and OTP |
| Authorization | What are you allowed to do? | Can view reports, cannot edit |
| Accounting (audit) | What did you do? | Log: ravi viewed report at 10:15 |

**Analogy.** Entering an office building. You say your name (identification), show your ID card (authentication), the system checks which floors you may enter (authorization), and the door logs your entry time (accounting).

**AAA protocols.** RADIUS (common for Wi-Fi and VPN), TACACS+ (device administration, encrypts the full payload), Diameter (modern successor to RADIUS).

### 5.2 Authentication factors

| Factor | Meaning | Examples | Weakness |
|---|---|---|---|
| Something you know | Secret | Password, PIN | Guessable, phishable |
| Something you have | Object | Smart card, phone OTP, hardware token | Lost or stolen |
| Something you are | Biometric | Fingerprint, face, iris | Cannot be changed if leaked |
| Somewhere you are | Location | IP geofence | Spoofable |
| Something you do | Behavior | Typing pattern | Needs tuning |

**MFA** means two or more different factor types. Two passwords is not MFA.

**Biometric errors.** FAR (False Acceptance Rate: wrong person accepted, a security risk). FRR (False Rejection Rate: right person rejected, a usability problem). **CER or EER** is the point where FAR equals FRR. Lower is better.

**OTP types.** HOTP is counter based. TOTP is time based (usually 30 seconds), used by authenticator apps.

```python
import base64
import hashlib
import hmac
import struct
import time

def totp(secret_b32, step=30, digits=6):
    key = base64.b32decode(secret_b32)
    counter = int(time.time() // step)
    msg = struct.pack(">Q", counter)
    h = hmac.new(key, msg, hashlib.sha1).digest()
    offset = h[-1] & 0x0F
    code = (struct.unpack(">I", h[offset:offset + 4])[0] & 0x7FFFFFFF) % (10 ** digits)
    return str(code).zfill(digits)

print(totp("JBSWY3DPEHPK3PXP"))   # changes every 30 seconds
```

### 5.3 Mini AAA flow

```python
import hashlib
import hmac
import logging
import os

logging.basicConfig(level=logging.INFO, format="%(asctime)s %(message)s")

def hash_pw(password, salt):
    return hashlib.pbkdf2_hmac("sha256", password.encode(), salt, 200_000)

salt = os.urandom(16)
users = {"ravi": {"salt": salt, "hash": hash_pw("Str0ng#Pass", salt), "role": "Support"}}
roles = {"Support": {"read_tickets"}}

def authenticate(user, password):
    rec = users.get(user)
    if not rec:
        return False
    candidate = hash_pw(password, rec["salt"])
    return hmac.compare_digest(candidate, rec["hash"])

def authorize(user, action):
    return action in roles[users[user]["role"]]

def access(user, password, action):
    if not authenticate(user, password):
        logging.info("user=%s action=LOGIN result=DENIED", user)
        return "Authentication failed"
    if not authorize(user, action):
        logging.info("user=%s action=%s result=FORBIDDEN", user, action)
        return "Not authorized"
    logging.info("user=%s action=%s result=ALLOWED", user, action)
    return "OK"

print(access("ravi", "Str0ng#Pass", "read_tickets"))     # OK
print(access("ravi", "Str0ng#Pass", "delete_tickets"))   # Not authorized
```

---

## 6. Security Design Principles

| Principle | Plain meaning | Example |
|---|---|---|
| Defense in depth | Many layers, not one wall | Firewall, MFA, endpoint protection, encryption, backups |
| Least privilege | Only the access needed | A developer cannot open the production database |
| Need to know | Even with clearance, only the information required | Only the deal team sees merger documents |
| Separation of duties | No one person controls a full critical task | One person requests a payment, another approves |
| Job rotation and mandatory leave | Exposes fraud when someone else does the job | An admin takes leave and another admin reviews their work |
| Fail-safe vs fail-secure | What happens when a system fails | Fail-secure: a firewall that fails blocks traffic. Fail-safe: a fire exit door unlocks on power loss |
| Complete mediation | Check every access every time | Do not cache "allowed" forever |
| Economy of mechanism | Keep design simple | Fewer rules, fewer holes |
| Open design (Kerckhoffs) | Security depends on the key, not on a secret algorithm | Use AES, not a homemade cipher |
| Secure by default | Safe settings out of the box | New devices have no default password |
| Zero Trust | Never trust, always verify | Even inside the office network, every request is authenticated |
| Weakest link | Attackers pick the easiest path | An unpatched vendor laptop |
| Obscurity is not security | Hiding is a bonus, never the main control | An unlisted URL still needs a login |

---

## 7. Risk Management

### 7.1 Vocabulary

| Term | Meaning |
|---|---|
| Asset | Anything valuable (data, system, person, reputation) |
| Threat | Possible cause of harm |
| Vulnerability | Weakness a threat can use |
| Exposure | Being open to a threat |
| Likelihood | Chance it happens |
| Impact | Damage if it happens |
| Risk | Likelihood x Impact. Often written Threat x Vulnerability x Impact |
| Control or safeguard | Measure that reduces risk |
| Residual risk | Risk left after controls |
| Risk appetite | How much risk the organization accepts |

**Analogy.** Asset = a house. Threat = flood. Vulnerability = the house sits on a river bank. Risk = how likely and how costly a flood is. Control = a raised foundation or insurance.

### 7.2 Risk management process

1. **Identify assets** and rate their value.
2. **Identify threats** and **vulnerabilities**.
3. **Assess risk** (qualitative or quantitative).
4. **Treat risk** (choose a response).
5. **Implement controls.**
6. **Monitor and review** continually.

### 7.3 Risk treatment options

| Option | Meaning | Example |
|---|---|---|
| Mitigate (reduce) | Apply controls | Patch servers and add MFA |
| Transfer (share) | Move financial impact | Cyber insurance, outsource to a managed provider |
| Avoid | Stop the risky activity | Do not allow personal USB drives |
| Accept (retain) | Knowingly live with it | Low-impact risk on an old internal wiki |

### 7.4 Qualitative assessment

Rate likelihood and impact as Low, Medium, High (or 1 to 5) and place them on a matrix.

| Likelihood \ Impact | Low | Medium | High |
|---|---|---|---|
| High | Medium | High | Critical |
| Medium | Low | Medium | High |
| Low | Low | Low | Medium |

### 7.5 Quantitative assessment

| Term | Formula | Meaning |
|---|---|---|
| AV | Asset Value | Worth of the asset |
| EF | Exposure Factor | Percentage of asset lost in one event |
| SLE | AV x EF | Single Loss Expectancy |
| ARO | Annual Rate of Occurrence | Times per year |
| ALE | SLE x ARO | Annualized Loss Expectancy |

A control is worth buying when (ALE before) minus (ALE after) is greater than the annual cost of the control.

**Worked example.** The order-processing system is worth 5 crore rupees. Ransomware would destroy 40 percent of its value. It is expected once in 5 years.

* SLE = 5 crore x 0.40 = 2 crore
* ARO = 1/5 = 0.2
* ALE = 2 crore x 0.2 = 40 lakh

A control costing 15 lakh per year cuts ARO to 0.05.
* New ALE = 2 crore x 0.05 = 10 lakh
* Saving = 40 lakh minus 10 lakh = 30 lakh
* Net benefit = 30 lakh minus 15 lakh = 15 lakh. Worth buying.

```python
def sle(asset_value, exposure_factor):
    return asset_value * exposure_factor

def ale(sle_value, aro):
    return sle_value * aro

asset_value = 5_00_00_000   # 5 crore
single = sle(asset_value, 0.40)
before = ale(single, 0.2)
after = ale(single, 0.05)
control_cost = 15_00_000

print("SLE", single)              # 20000000 (2 crore)
print("ALE before", before)       # 4000000.0 (40 lakh)
print("ALE after", after)         # 1000000.0 (10 lakh)
print("Net benefit", before - after - control_cost)   # 1500000.0 (15 lakh)
```

### 7.6 Business Impact Analysis (BIA)

Finds which functions must recover first and how fast. Outputs include **RTO** (Recovery Time Objective: maximum time to restore), **RPO** (Recovery Point Objective: maximum data loss measured in time), and **MTD or MAO** (Maximum Tolerable Downtime). RTO must be smaller than MTD.

Example: the payments system has RTO 1 hour and RPO 5 minutes. The staff cafeteria billing system has RTO 3 days and RPO 1 day.

---

## 8. Security Controls

### 8.1 By nature

| Category | Meaning | Examples |
|---|---|---|
| Administrative (managerial) | Rules and people | Policies, training, background checks |
| Technical (logical) | Software and hardware controls | Firewall, encryption, MFA |
| Physical | Real-world barriers | Locks, guards, CCTV, fences |

### 8.2 By function

| Function | Purpose | Example |
|---|---|---|
| Preventive | Stop it from happening | Firewall, door lock |
| Detective | Notice it happening | IDS, CCTV, log review |
| Corrective | Fix after it happened | Patch, restore backup |
| Deterrent | Discourage the attempt | Warning banner, signs |
| Recovery | Restore normal operation | DR site, backup restore |
| Compensating | Substitute when the primary control is impossible | Manual approval when an automated check is down |
| Directive | Instruct behavior | Acceptable use policy |

> One control can fit several types. A CCTV camera is detective and also deterrent.

---

## 9. Policy, Standard, Procedure, Guideline

| Document | Nature | Example |
|---|---|---|
| Policy | High-level rule from management. Mandatory | "All staff must use MFA for remote access" |
| Standard | Specific mandatory requirement | "MFA must use TOTP or a hardware token. SMS is not allowed" |
| Procedure | Step by step instructions | "How to enrol a phone in the authenticator app" |
| Guideline | Recommended advice. Optional | "Prefer hardware keys for administrators" |
| Baseline | Minimum configuration | "Hardened Windows 11 baseline" |

Hierarchy from top: Policy, Standard, Baseline, Procedure, Guideline.

---

## 10. Threat Actors and Threat Modeling

### 10.1 Actors (more in file 02)

| Actor | Motive | Skill | Scenario |
|---|---|---|---|
| Script kiddie | Fun, fame | Low | Runs a downloaded flood tool on a website |
| Hacktivist | Cause | Medium | Defaces a website to protest |
| Cybercriminal | Money | Medium to high | Ransomware campaign |
| Insider | Revenge, money, error | Varies, has access | Employee copies customer data |
| Nation-state or APT | Spying, disruption | Very high | Long stealth intrusion |
| Competitor | Business advantage | Medium | Steals product plans |
| Terrorist | Fear, harm | Varies | Targets critical systems |

**Hat colours.** White hat (authorized ethical hacker), Black hat (criminal), Grey hat (no permission but not malicious), Red team (simulates attackers), Blue team (defends), Purple team (red and blue working together).

### 10.2 STRIDE threat model

Threat modeling means thinking like an attacker before building a system. STRIDE is the best-known method (Microsoft).

| Letter | Threat | Violates | Example | Typical control |
|---|---|---|---|---|
| S | Spoofing | Authentication | Fake login page or stolen session | MFA, certificates |
| T | Tampering | Integrity | Editing an order amount in transit | Hashes, signatures, TLS |
| R | Repudiation | Non-repudiation | A user denies deleting records | Audit logs, signatures |
| I | Information disclosure | Confidentiality | Database exposed on the internet | Encryption, access control |
| D | Denial of service | Availability | Flood on the login API | Rate limiting, DDoS protection, redundancy |
| E | Elevation of privilege | Authorization | Normal user becomes admin | Least privilege, patching |

Other models: **DREAD** (rating threats), **PASTA** (process driven), **attack trees** (goal at the top, ways to achieve it below), **Data flow diagrams** (draw where data moves and mark trust boundaries).

### 10.3 Cyber Kill Chain (Lockheed Martin)

| Stage | What attacker does | Defender action |
|---|---|---|
| 1 Reconnaissance | Gathers information about the target | Limit public information |
| 2 Weaponization | Builds the malicious payload | Threat intelligence |
| 3 Delivery | Sends email, USB, link | Email filter, awareness |
| 4 Exploitation | Uses a vulnerability | Patching |
| 5 Installation | Plants malware or backdoor | EDR, application control |
| 6 Command and Control (C2) | Controls the system remotely | Egress filtering, DNS filtering |
| 7 Actions on objectives | Steals, encrypts, destroys | Backups, DLP, monitoring |

**MITRE ATT&CK** is a public knowledge base of attacker tactics (goals) and techniques (methods) such as Initial Access, Execution, Persistence, Privilege Escalation, Lateral Movement, Exfiltration, and Impact. SOC teams map detections to it.

---

## 11. Security Lifecycle and Frameworks (Overview)

Security is a continuous cycle: **Plan, Do, Check, Act (PDCA)**.

| Framework | What it is | Key idea |
|---|---|---|
| ISO/IEC 27001 | International standard for an Information Security Management System (ISMS) | Certifiable. Risk based. Annex A controls |
| ISO/IEC 27002 | Guidance on controls | Detail for 27001 controls |
| NIST Cybersecurity Framework 2.0 | US framework used worldwide | Govern, Identify, Protect, Detect, Respond, Recover |
| NIST SP 800-53 | Catalogue of security controls | Detailed control families |
| CIS Controls | Prioritized practical actions | Good starting checklist |
| COBIT | IT governance | Link IT to business goals |
| PCI DSS | Card data security | Needed wherever card payments are handled |
| SOC 2 | Audit report on service providers | Trust service criteria |

Laws and frameworks are covered in detail in file 04.

---

## 12. Exam Corner

### One-mark facts

* CIA stands for Confidentiality, Integrity, Availability.
* Risk = Threat x Vulnerability x Impact (or Likelihood x Impact).
* ALE = SLE x ARO and SLE = AV x EF.
* A digital signature gives integrity, authenticity, and non-repudiation.
* Authentication proves identity. Authorization decides permissions.
* MFA needs two different factor types.
* Defense in depth means layered security.
* Least privilege means minimum access for the job.
* Separation of duties prevents one person controlling an entire critical process.
* RTO is time. RPO is data loss.
* STRIDE has six threat types.
* The kill chain has seven stages.

### Common traps

1. Mixing authentication and authorization.
2. Thinking encryption alone gives integrity. It does not. Use a hash, MAC, or signature.
3. Calling DoS an attack on confidentiality. It targets availability.
4. Thinking IDS blocks traffic. IDS only alerts, IPS blocks.
5. Treating two passwords as MFA.
6. Confusing RTO and RPO.

### Self-check (answers below)

1. An attacker alters an invoice amount in a database. Which CIA goal fails?
2. A user logs in correctly and then opens a restricted module. Which AAA step should stop this?
3. Give the formula for ALE.
4. Which control type is CCTV?
5. In STRIDE, what does the R stand for?
6. What is the difference between a threat and a vulnerability?
7. A control that substitutes for another that cannot be applied is called?
8. Which kill chain stage uses a software flaw?

**Answers.** 1 Integrity. 2 Authorization. 3 ALE = SLE x ARO. 4 Detective (and deterrent). 5 Repudiation. 6 A threat is a possible cause of harm. A vulnerability is a weakness it can exploit. 7 Compensating. 8 Exploitation.

---

*End of file 01. Continue with 02-threats-attacks-and-vulnerabilities.md*
