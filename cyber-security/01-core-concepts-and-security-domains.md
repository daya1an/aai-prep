# 01. Core Concepts and Security Domains

AAI IT Manager I. Subject: Cybersecurity Fundamentals. Part 1 of 5.

Every concept follows this order: a plain analogy, then an Airport, Flight or Airline example, then Python where code helps. Code is Python 3 and uses only the standard library unless a `pip install` is shown.

**Study order for all five files**

| File | Covers |
|---|---|
| 01 (this file) | What security is, domains, CIA, AAA, principles, risk, controls |
| 02 | Threat vectors, cyberattacks, vulnerabilities |
| 03 | Network security (firewall, VPN, IDS/IPS, encryption, segmentation, endpoint, cloud) |
| 04 | Data protection frameworks (classification, access control, windowing, audit, incident response, laws) |
| 05 | Cybersecurity dashboarding |

---

## 1. What is Cybersecurity

**Analogy.** An airport has gates, guards, ID checks, CCTV, and rules about who may enter the runway. Cybersecurity is the same idea for computers, networks, and data.

**Definition.** Cybersecurity is the practice of protecting computers, networks, programs, and data from unauthorized access, damage, theft, or disruption.

### Related terms

| Term | Meaning | Note |
|---|---|---|
| Information security | Protect information in any form (digital, paper, spoken) | Widest term |
| Cybersecurity | Protect digital systems and data from digital threats | Focus of this subject |
| IT security | Protect IT assets such as servers and devices | Similar to cybersecurity |
| Network security | Protect data while it moves across networks | Covered in file 03 |
| Application security | Make software free of exploitable flaws | AppSec |
| Data security | Protect data itself (at rest, in transit, in use) | Covered in file 04 |
| Privacy | Right of a person to control their personal data | Legal and ethical |

### Safety vs Security vs Privacy

| Term | Question | Airport example |
|---|---|---|
| Safety | Will accidents or failures hurt people? | Runway lights fail by faulty wiring |
| Security | Will someone deliberately cause harm? | Hacker switches off runway lights |
| Privacy | Is personal data handled with consent and care? | Passenger passport data sold to a third party |

In aviation, safety and security overlap. A cyberattack on a safety system becomes a safety event.

### Why aviation is a high-value target

1. Many people and high public visibility, so attacks gain attention.
2. Lots of personal and payment data (passport, phone, card, travel pattern).
3. Strict time schedules, so even short downtime causes big losses (delayed flights cascade).
4. Many connected parties (airlines, airports, ground handlers, caterers, customs, vendors), so attackers can enter through the weakest partner.
5. Mix of old operational technology and modern IT.
6. National importance. Airports are usually treated as **critical information infrastructure**.

### Typical airport and airline systems

| System | Full form or meaning | What it does |
|---|---|---|
| AODB | Airport Operational Database | Central store of flight and resource data |
| FIDS | Flight Information Display System | Arrival and departure screens |
| DCS | Departure Control System | Check-in and boarding |
| PSS | Passenger Service System | Reservation, inventory, ticketing |
| CUTE or CUPPS | Common Use Terminal Equipment or Common Use Passenger Processing Systems | Shared check-in counters for many airlines |
| BHS | Baggage Handling System | Conveyors, scanners, sorting |
| CNS/ATM | Communication, Navigation, Surveillance and Air Traffic Management | ATC voice, radar, ADS-B, navigation aids |
| GDS | Global Distribution System | Travel agent booking networks |
| ACS | Access Control System | Doors, turnstiles, airport entry permits |
| CCTV and VMS | Video Management System | Cameras and recording |
| BMS | Building Management System | Lifts, air conditioning, power |
| Payment gateway | Card and UPI processing | Ticket and retail payments |

### IT vs OT

| Point | IT (Information Technology) | OT (Operational Technology) |
|---|---|---|
| Examples | Booking servers, email, HR | Baggage conveyors, runway lighting, ILS |
| Top priority | Confidentiality | Availability and safety |
| Patching | Frequent and quick | Rare and carefully planned |
| Lifespan | 3 to 5 years | 15 to 25 years |
| Risk when merged | OT devices were not designed for internet exposure | |

> **Remember.** In IT the priority order is usually C, I, A. In OT it is often A, I, C because a stopped conveyor or lighting system is a direct operational and safety problem.

### Real cases (widely reported, use only as awareness)

* **British Airways 2018.** A web-skimming attack on its website and app stole card details of about 380,000 transactions. The UK regulator later issued a large fine.
* **Air India 2021.** A cyberattack on SITA, a passenger service system provider, exposed data of millions of passengers across several airlines. Lesson: third-party risk.

### Indian bodies you should know

| Body | Role |
|---|---|
| MoCA | Ministry of Civil Aviation |
| AAI | Airports Authority of India, runs many airports and air navigation services |
| DGCA | Regulates civil aviation safety |
| BCAS | Bureau of Civil Aviation Security, aviation security regulator |
| CERT-In | National agency for cyber incident response |
| NCIIPC | Protects critical information infrastructure |
| MeitY | Ministry of Electronics and Information Technology |
| ICAO | International body. Annex 17 covers aviation security |

---

## 2. Key Security Domains

A security domain is an area of security with its own goals, tools, and skills. A complete program covers all of them. Weakness in one domain breaks the whole chain.

### 2.1 Overview table

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
| 9 | Mobile and IoT security | Protect phones, sensors, cameras, kiosks |
| 10 | OT and ICS security | Protect control systems such as baggage and lighting |
| 11 | Incident response and recovery | Handle attacks and restore service |
| 12 | Business continuity and disaster recovery (BCP and DRP) | Keep operations alive |
| 13 | Governance, risk and compliance (GRC) | Policies, risk decisions, laws |
| 14 | Security awareness and training | Humans as a defense |
| 15 | Cryptography | Math tools for secrecy and trust |
| 16 | Security operations and monitoring | Watch, detect, respond 24x7 |

### 2.2 Each domain in detail

**1. Network security**
* Analogy: Roads, signals, and checkpoints of a city.
* Controls: firewall, VPN, IDS/IPS, segmentation, secure Wi-Fi, NAC.
* Airport: Passenger Wi-Fi is separated from ATC network.
* Covered fully in file 03.

**2. Endpoint security**
* Analogy: Every room in the building has its own lock.
* Controls: antivirus, EDR, disk encryption, patching, USB blocking, MDM.
* Airport: Check-in counter PCs only run approved software.

**3. Application security**
* Analogy: A strong door is useless if the window glass is thin.
* Controls: secure coding, input validation, code review, SAST and DAST, WAF, dependency scanning.
* Airport: Flight status website must not allow SQL injection.

**4. Data security**
* Analogy: A safe inside the strong room.
* Controls: classification, encryption, DLP, masking, backups, retention.
* Airport: Passenger passport scans stored encrypted.

**5. Identity and access management**
* Analogy: Boarding pass and ID check before you enter the aircraft.
* Controls: unique IDs, MFA, single sign-on, RBAC, privileged access management, access reviews.
* Airport: Ground staff cannot open ATC systems.

**6. Cloud security**
* Analogy: Renting a locker in a shared building. The building owner secures the building, you secure your locker.
* Controls: IAM policies, encryption, configuration scanning, logging, CASB.
* Airport: Airline stores loyalty data in a cloud database. Public access must be blocked.

**7. Operational security (OpSec)**
* Analogy: Not announcing the VIP's route in advance.
* Practice: Protect small pieces of information that together reveal a secret. Example: shift rosters, security patrol timings, and system diagrams must not be posted publicly.

**8. Physical security**
* Analogy: Fences, guards, and locks.
* Controls: access cards, biometrics, CCTV, mantraps, server room locks, visitor logs, cable protection.
* Airport: Only authorized staff enter the server room. Doors alarm if propped open.

**9. Mobile and IoT security**
* Analogy: Many small unlocked windows.
* Issues: default passwords, no patches, weak encryption.
* Airport: Smart cameras, boarding gate scanners, handheld baggage scanners.

**10. OT and ICS security**
* Industrial Control Systems.
* Controls: air gaps or strict firewalls, protocol monitoring, change control, vendor access control.
* Airport: Baggage conveyors and runway lighting controllers.

**11. Incident response (IR)**
* Analogy: Fire brigade.
* Steps: prepare, detect, contain, eradicate, recover, learn.

**12. BCP and DRP**
* Analogy: Backup generator and an alternate airport.
* BCP keeps business running. DRP restores IT after disaster. Terms: RTO and RPO.

**13. GRC**
* Policies, risk assessments, audits, laws like IT Act 2000 and DPDP Act 2023.

**14. Awareness**
* Humans cause most breaches by mistakes. Training and phishing drills reduce clicks.

**15. Cryptography**
* Encryption, hashing, digital signatures, certificates. File 03.

**16. Security operations**
* SOC team, SIEM, threat hunting, dashboards. File 05.

### 2.3 Layers of defense (visual)

```
Policies, laws, awareness          (people and process)
  Physical security                (fences, guards, locks)
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

**Airport trace.**
1. Passenger list for flight AI101 is stored in the DCS.
2. A curious employee from catering tries to open it.
3. Access control checks the role "Catering" and denies the request.
4. Result: confidentiality kept.

### 3.2 Integrity

**Analogy.** A tamper-evident seal on a medicine bottle. If broken, you know someone touched it.

**Definition.** Data and systems stay accurate, complete, and unaltered by unauthorized or accidental changes.

| Threats | Controls |
|---|---|
| Tampering, malware, SQL injection, man-in-the-middle, human error | Hashing, digital signatures, checksums, version control, input validation, change control, write-protected logs |

**Airport trace.**
1. The AODB says AI101 departs from gate B12.
2. An attacker changes it to gate A3 in the display feed.
3. A digital signature on the data feed no longer matches.
4. System rejects the change and raises an alert.

### 3.3 Availability

**Analogy.** A tap that must give water whenever you open it.

**Definition.** Systems and data are accessible to authorized users when needed.

| Threats | Controls |
|---|---|
| DoS and DDoS, ransomware, hardware failure, power cut, natural disaster | Redundancy, load balancing, backups, UPS and generators, DDoS protection, patching, disaster recovery |

**Airport trace.**
1. Ransomware encrypts the check-in servers.
2. Without backup, check-in stops and flights are delayed.
3. With offline backup and a standby site, staff switch over within the RTO.

**Availability math.** Uptime % = (Total time minus Downtime) / Total time x 100.

| Uptime | Downtime per year |
|---|---|
| 99% | about 3.65 days |
| 99.9% ("three nines") | about 8.76 hours |
| 99.99% | about 52.6 minutes |
| 99.999% ("five nines") | about 5.26 minutes |

### 3.4 Trade-offs

* More secrecy often reduces convenience and sometimes availability (too many checks slow staff).
* High availability needs copies of data, and more copies means more places to protect (confidentiality risk).
* Always decide priority by asset. Public flight schedule: availability first. Passenger passport data: confidentiality first. Flight plan data: integrity first.

### 3.5 Python demo of all three

```python
import hashlib

# CONFIDENTIALITY: role based read check
permissions = {"Airline_Staff": {"read_passenger_list"}, "Catering": set()}

def can_read(role):
    return "read_passenger_list" in permissions.get(role, set())

print(can_read("Airline_Staff"))   # True
print(can_read("Catering"))        # False

# INTEGRITY: detect change using a hash
def fingerprint(text):
    return hashlib.sha256(text.encode()).hexdigest()

original = "AI101|DEL-BOM|Gate B12|10:00"
stored_hash = fingerprint(original)
received = "AI101|DEL-BOM|Gate A3|10:00"
print("Integrity OK" if fingerprint(received) == stored_hash else "TAMPERED")

# AVAILABILITY: uptime calculation
def uptime_percent(total_minutes, downtime_minutes):
    return (total_minutes - downtime_minutes) / total_minutes * 100

year_minutes = 365 * 24 * 60
print(round(uptime_percent(year_minutes, 52.6), 3))   # about 99.99
```

---

## 4. Beyond CIA

| Concept | Meaning | Tool | Airport example |
|---|---|---|---|
| Authenticity | Data or user is genuine | Certificates, signatures | Message really came from the airline |
| Non-repudiation | Sender cannot deny sending | Digital signature, audit log | Controller cannot deny approving a plan change |
| Accountability | Actions trace to a person | Unique IDs, logs | Every ATC login is individual |
| Reliability | System works consistently | Testing, redundancy | Radar feed stable |
| Privacy | Control over personal data | Consent, minimization | Passenger can ask for data deletion |

**Parkerian Hexad** adds three items to CIA: **Possession or control** (who physically holds the data, such as a stolen laptop even if encrypted), **Authenticity**, and **Utility** (data must be usable, such as an encrypted file whose key is lost).

---

## 5. Identity and the AAA Model

### 5.1 Four steps

| Step | Question | Example |
|---|---|---|
| Identification | Who do you claim to be? | Enter staff ID "AAI4521" |
| Authentication | Prove it | Password and OTP |
| Authorization | What are you allowed to do? | View flight plan, cannot edit |
| Accounting (audit) | What did you do? | Log: AAI4521 viewed plan at 10:15 |

**Analogy.** Airport entry. Say your name (identification), show your ID (authentication), the guard checks which areas you may enter (authorization), and the gate machine logs your entry time (accounting).

**AAA protocols.** RADIUS (common for Wi-Fi and VPN), TACACS+ (device administration, encrypts full payload), Diameter (modern successor to RADIUS).

### 5.2 Authentication factors

| Factor | Meaning | Examples | Weakness |
|---|---|---|---|
| Something you know | Secret | Password, PIN | Guessable, phishable |
| Something you have | Object | Smart card, phone OTP, hardware token | Lost or stolen |
| Something you are | Biometric | Fingerprint, face, iris | Cannot be changed if leaked |
| Somewhere you are | Location | IP geofence | Spoofable |
| Something you do | Behavior | Typing pattern | Needs tuning |

**MFA** means two or more different factor types. Two passwords is not MFA.

**Biometric errors.** FAR (False Acceptance Rate: wrong person accepted, security risk). FRR (False Rejection Rate: right person rejected, usability problem). **CER or EER** is the point where FAR equals FRR. Lower is better.

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
users = {"ravi": {"salt": salt, "hash": hash_pw("Str0ng#Pass", salt), "role": "Airline_Staff"}}
roles = {"Airline_Staff": {"read_flight_plan"}}

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

print(access("ravi", "Str0ng#Pass", "read_flight_plan"))     # OK
print(access("ravi", "Str0ng#Pass", "update_flight_plan"))   # Not authorized
```

---

## 6. Security Design Principles

| Principle | Plain meaning | Airport example |
|---|---|---|
| Defense in depth | Many layers, not one wall | Fence, guard, ID check, locked room, camera |
| Least privilege | Only the access needed | Cargo staff cannot view crew data |
| Need to know | Even with clearance, only the info you need | VIP flight details limited to the handling team |
| Separation of duties | No one person controls a full critical task | One person requests a gate change, another approves |
| Job rotation and mandatory leave | Exposes fraud when someone else does the job | IT admin takes 2 weeks leave, another admin reviews |
| Fail-safe vs fail-secure | What happens when a system fails | Emergency exit doors unlock on power failure (fail-safe for life safety), server room doors stay locked (fail-secure) |
| Complete mediation | Check every access every time | Do not cache "allowed" forever |
| Economy of mechanism | Keep design simple | Fewer rules, fewer holes |
| Open design (Kerckhoffs) | Security must not depend on a secret algorithm, only on the key | Use AES, not a homemade cipher |
| Secure by default | Safe settings out of the box | New devices ship with no default password |
| Zero Trust | Never trust, always verify | Even inside the airport network, every request is authenticated |
| Weakest link | Attackers pick the easiest path | A vendor laptop on staff VLAN |
| Security through obscurity is not security | Hiding is a bonus, never the main control | Unlisted URL still needs login |

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

**Analogy.** Asset = house. Threat = flood. Vulnerability = house is at river bank. Risk = how likely and how costly. Control = raising the foundation, buying insurance.

### 7.2 Risk management process

1. **Identify assets** and rate their value (data, systems, people).
2. **Identify threats** and **vulnerabilities**.
3. **Assess risk** (qualitative or quantitative).
4. **Treat risk** (choose a response).
5. **Implement controls.**
6. **Monitor and review** continually.

### 7.3 Risk treatment options

| Option | Meaning | Airport example |
|---|---|---|
| Mitigate (reduce) | Apply controls | Install patches and MFA on check-in system |
| Transfer (share) | Move financial impact | Cyber insurance, outsource to a managed provider |
| Avoid | Stop the risky activity | Do not allow personal USB drives at all |
| Accept (retain) | Knowingly live with it | Low-impact risk on an old internal wiki |

### 7.4 Qualitative assessment

Rate likelihood and impact as Low, Medium, High (or 1 to 5) and place on a matrix.

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

Control is worth buying when: (ALE before) minus (ALE after) is greater than annual cost of control.

**Worked example.** Baggage system is worth 5 crore rupees. Ransomware would destroy 40 percent of its value. It is expected once in 5 years.

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

### 7.5b Business Impact Analysis (BIA)

Finds which functions must recover first and how fast. Output includes **RTO** (Recovery Time Objective: maximum time to restore), **RPO** (Recovery Point Objective: maximum data loss measured in time) and **MTD or MAO** (Maximum Tolerable Downtime). Order: RTO must be less than MTD.

Airport example: Check-in system RTO 1 hour, RPO 5 minutes. Staff cafeteria billing RTO 3 days, RPO 1 day.

---

## 8. Security Controls

### 8.1 By nature

| Category | Meaning | Examples |
|---|---|---|
| Administrative (managerial) | Rules and people | Policies, training, background checks |
| Technical (logical) | Software and hardware controls | Firewall, encryption, MFA |
| Physical | Real-world barriers | Locks, guards, CCTV, fences |

### 8.2 By function

| Function | Purpose | Airport example |
|---|---|---|
| Preventive | Stop it from happening | Firewall, door lock |
| Detective | Notice it happening | IDS, CCTV, log review |
| Corrective | Fix after it happened | Patch, restore backup |
| Deterrent | Discourage the attempt | Warning signs, banners |
| Recovery | Restore normal operation | DR site, backup restore |
| Compensating | Substitute when primary control impossible | Manual boarding when system is down |
| Directive | Instruct behavior | Acceptable use policy |

> One control can fit several types. A CCTV camera is detective and also deterrent.

---

## 9. Policy, Standard, Procedure, Guideline

| Document | Nature | Example |
|---|---|---|
| Policy | High level rule from management. Mandatory | "All staff must use MFA for remote access" |
| Standard | Specific mandatory requirement | "MFA must use TOTP or hardware token. SMS not allowed" |
| Procedure | Step by step instructions | "How to enrol a phone in the authenticator app" |
| Guideline | Recommended advice. Optional | "Prefer hardware keys for administrators" |
| Baseline | Minimum configuration | "Windows 11 hardened baseline" |

Hierarchy from top: Policy, Standard, Baseline, Procedure, Guideline.

---

## 10. Threat Actors and Threat Modeling

### 10.1 Actors (short, detail in file 02)

| Actor | Motive | Skill | Airport scenario |
|---|---|---|---|
| Script kiddie | Fun, fame | Low | Runs a downloaded flood tool on airline site |
| Hacktivist | Cause | Medium | Defaces website to protest |
| Cybercriminal | Money | Medium to high | Ransomware on airline |
| Insider | Revenge, money, error | Varies, has access | Staff copies passenger data |
| Nation-state or APT | Spying, disruption | Very high | Long stealth intrusion into ATC support systems |
| Competitor | Business advantage | Medium | Steals fare strategy |
| Terrorist | Fear, harm | Varies | Targets safety systems |

**Hat colours.** White hat (authorized ethical hacker), Black hat (criminal), Grey hat (no permission but not malicious), Red team (simulate attacker), Blue team (defend), Purple team (red and blue working together).

### 10.2 STRIDE threat model

Threat modeling means thinking like an attacker before building the system. STRIDE is the best known method (Microsoft).

| Letter | Threat | Violates | Airport example | Typical control |
|---|---|---|---|---|
| S | Spoofing | Authentication | Fake gate-agent login | MFA, certificates |
| T | Tampering | Integrity | Edit baggage tag data | Hashes, signatures, access control |
| R | Repudiation | Non-repudiation | Officer denies approving change | Audit logs, signatures |
| I | Information disclosure | Confidentiality | Passport data leaked | Encryption, access control |
| D | Denial of service | Availability | Flood on booking API | Rate limiting, DDoS protection, redundancy |
| E | Elevation of privilege | Authorization | Ground staff becomes admin | Least privilege, patching |

Other models: **DREAD** (rating threats), **PASTA** (process driven), **attack trees** (goal at top, ways to achieve it below), **Data flow diagrams** (draw where data moves and mark trust boundaries).

### 10.3 Cyber Kill Chain (Lockheed Martin)

| Stage | What attacker does | Defender action |
|---|---|---|
| 1 Reconnaissance | Gathers info about target | Limit public info |
| 2 Weaponization | Builds malicious payload | Threat intelligence |
| 3 Delivery | Sends email, USB, link | Email filter, awareness |
| 4 Exploitation | Uses a vulnerability | Patching |
| 5 Installation | Plants malware or backdoor | EDR, application control |
| 6 Command and Control (C2) | Attacker controls remotely | Egress filtering, DNS filtering |
| 7 Actions on objectives | Steals, encrypts, destroys | Backups, DLP, monitoring |

**MITRE ATT&CK** is a public knowledge base of attacker tactics (goals) and techniques (methods) such as Initial Access, Execution, Persistence, Privilege Escalation, Lateral Movement, Exfiltration, Impact. SOC teams map detections to it.

---

## 11. Security Lifecycle and Frameworks (Overview)

Security is a continuous cycle: **Plan, Do, Check, Act (PDCA)**.

| Framework | What it is | Key idea |
|---|---|---|
| ISO/IEC 27001 | International standard for an Information Security Management System (ISMS) | Certifiable. Risk based. Annex A controls |
| ISO/IEC 27002 | Guidance on controls | Detail for 27001 controls |
| NIST Cybersecurity Framework 2.0 | US framework, used worldwide | Govern, Identify, Protect, Detect, Respond, Recover |
| NIST SP 800-53 | Catalogue of security controls | Detailed control families |
| CIS Controls | Prioritized practical actions | Good starting checklist |
| COBIT | IT governance | Link IT to business goals |
| PCI DSS | Card data security | Needed for ticket payments |
| SOC 2 | Audit report on service providers | Trust service criteria |

Detailed treatment of laws and frameworks is in file 04.

---

## 12. Exam Corner

### One-mark facts

* CIA stands for Confidentiality, Integrity, Availability.
* Risk = Threat x Vulnerability x Impact (or Likelihood x Impact).
* ALE = SLE x ARO and SLE = AV x EF.
* Digital signature gives integrity, authenticity, and non-repudiation.
* Authentication proves identity. Authorization decides permissions.
* MFA needs two different factor types.
* Defense in depth means layered security.
* Least privilege means minimum access for the job.
* Separation of duties prevents one person controlling an entire critical process.
* RTO is time, RPO is data loss.
* STRIDE has six threat types.
* Kill chain has seven stages.

### Common traps

1. Mixing authentication and authorization.
2. Thinking encryption alone gives integrity. It does not. Use hash, MAC, or signature.
3. Calling DoS an attack on confidentiality. It targets availability.
4. Thinking IDS blocks traffic. IDS only alerts, IPS blocks.
5. Treating two passwords as MFA.
6. Confusing RTO and RPO.

### Self-check (answers below)

1. A hacker alters a flight departure time in a database. Which CIA goal fails?
2. A ground staff member logs in with correct password and then opens a restricted module. Which AAA step should stop this?
3. Give the formula for ALE.
4. Which control type is CCTV?
5. In STRIDE, what does the R stand for?
6. What is the difference between a threat and a vulnerability?
7. A control that substitutes for another that cannot be applied is called?
8. Which kill chain stage uses a software flaw?

**Answers.** 1 Integrity. 2 Authorization. 3 ALE = SLE x ARO. 4 Detective (and deterrent). 5 Repudiation. 6 A threat is a possible cause of harm. A vulnerability is a weakness it can exploit. 7 Compensating. 8 Exploitation.

---

*End of file 01. Continue with 02-threats-attacks-and-vulnerabilities.md*
