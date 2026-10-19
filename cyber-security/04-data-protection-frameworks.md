# 04. Common Elements in Data Protection Frameworks

AAI IT Manager I. Subject: Cybersecurity Fundamentals. Part 4 of 5.

Covers: data classification, access controls, encryption, data minimization, windowing, audit and monitoring, incident response, data subject rights, and data protection practices and policies. Includes the main laws and frameworks.

Install once for code that needs it: `pip install cryptography`

---

## 1. Big Picture

**Analogy.** An airline holds your passport copy, phone number, and card details. A data protection framework is the full set of habits and rules that decides: what we collect, who can see it, how we lock it, how long we keep it, how we know if someone peeked, what we do if it leaks, and how you can ask to see or delete it.

### 1.1 Data protection, data security, privacy

| Term | Meaning | Focus |
|---|---|---|
| Data security | Technical and organizational protection against unauthorized access or loss | Keep data safe |
| Data protection | Security plus legal and ethical handling of personal data | Safe and lawful handling |
| Privacy | A person's right to control how their data is used | Rights of the individual |

You can have security without privacy (a company securely stores data it should never have collected). You cannot have real privacy without security.

### 1.2 Personal data types

| Type | Examples |
|---|---|
| Personal data or PII | Name, phone, email, address, Aadhaar, passport number, frequent flyer ID |
| Sensitive personal data | Health, financial details, biometrics, sexual orientation, passwords (often with stricter rules) |
| Non-personal data | Aggregated flight statistics, weather |
| Anonymous data | Cannot be linked back to a person (outside personal data laws if truly anonymous) |

Roles: **Data subject** (the passenger), **Data controller** or **Data fiduciary** (decides why and how data is processed, such as the airline), **Data processor** (processes for the controller, such as a cloud or SITA-like provider), **DPO** (Data Protection Officer).

### 1.3 Data life cycle

| Stage | Question | Control |
|---|---|---|
| Create or collect | Do we need this? | Minimization, consent, notice |
| Store | Is it protected? | Encryption, access control, backups |
| Use | Who sees it and why? | Role based access, audit logs |
| Share | Who receives it? | Contracts, encryption, masking |
| Archive | How long do we keep it? | Retention policy |
| Destroy | Is it gone for real? | Secure deletion, shredding |

### 1.4 The common elements (from the syllabus)

1. Data classification
2. Access controls
3. Encryption (at rest and in transit)
4. Data minimization (and windowing of data management)
5. Audit and monitoring
6. Incident response
7. Data subject rights
8. Data protection practices and policies

Each is explained below.

---

## 2. Data Classification

### 2.1 Concept

**Analogy.** Mail is sorted into postcards, letters, registered post, and sealed diplomatic bags. Each gets different handling. Data should be sorted the same way.

**Definition.** Organizing data by its sensitivity and business value, then applying matching protection.

### 2.2 Typical commercial levels

| Level | Meaning | Airport or airline example | Handling |
|---|---|---|---|
| Public | Safe to share with anyone | Flight schedule, press release | No special control, integrity still matters |
| Internal (private) | For staff only. Low damage if leaked | Staff roster, internal circulars | Login required |
| Confidential | Damage if leaked | Passenger list, contracts, fare strategy | Encryption, need to know, logging |
| Restricted (highly confidential) | Severe damage | ATC credentials, security screening procedures, encryption keys | Strongest controls, MFA, strict audit |

Government systems often use: **Top Secret, Secret, Confidential, Restricted** and "Unclassified". Follow your organization's scheme.

### 2.3 Steps

1. **Inventory and discover** where data lives (databases, file shares, cloud, emails).
2. **Define labels** and rules.
3. **Assign an owner** for each dataset (data owner decides classification, data custodian runs the systems, data user uses it).
4. **Label** (metadata, headers, file properties).
5. **Apply handling rules** (encryption, access, sharing, retention).
6. **Review** periodically and **declassify** when sensitivity drops.

### 2.4 Related ideas

| Term | Meaning |
|---|---|
| Data labeling and tagging | Marking data with its class |
| Data discovery | Tools that scan for sensitive data patterns |
| DLP | Data Loss Prevention. Policies that block or warn when sensitive data leaves |
| Data masking | Hide part of data for non-privileged use |
| Handling rules | Who can copy, print, email, or store the data |
| Aggregation risk | Many low-level items together can become sensitive |

### 2.5 Python: classify by content

```python
import re

PATTERNS = {
    "AADHAAR": re.compile(r"\b\d{4}\s?\d{4}\s?\d{4}\b"),
    "PAN":     re.compile(r"\b[A-Z]{5}\d{4}[A-Z]\b"),
    "MOBILE":  re.compile(r"\b[6-9]\d{9}\b"),
    "EMAIL":   re.compile(r"\b[\w.+-]+@[\w-]+\.[\w.]+\b"),
    "CARD":    re.compile(r"\b(?:\d[ -]?){13,16}\b"),
}

def luhn_ok(number):
    digits = [int(c) for c in re.sub(r"\D", "", number)][::-1]
    total = 0
    for i, d in enumerate(digits):
        if i % 2 == 1:
            d *= 2
            if d > 9:
                d -= 9
        total += d
    return total % 10 == 0

def classify(text):
    found = []
    for label, pattern in PATTERNS.items():
        for match in pattern.findall(text):
            if label == "CARD" and not luhn_ok(match):
                continue
            found.append(label)
    if "CARD" in found or "AADHAAR" in found:
        return "Restricted", sorted(set(found))
    if found:
        return "Confidential", sorted(set(found))
    return "Internal", []

sample = "Passenger Ravi, mobile 9876543210, card 4111 1111 1111 1111, flight AI101"
print(classify(sample))   # ('Restricted', ['CARD', 'MOBILE'])
```

---

## 3. Access Controls

### 3.1 Concept

**Analogy.** Airport zones. The terminal is open to ticketed passengers. The airside is for permit holders. The control tower is for authorized staff only. Different badges open different doors.

**Definition.** Rules and tools that decide who can access which data or system, and what they can do, so that unauthorized use is prevented.

### 3.2 Principles

| Principle | Meaning |
|---|---|
| Least privilege | Minimum rights for the task |
| Need to know | Access only information needed for the job |
| Separation of duties | Split critical tasks across people |
| Default deny | No access unless granted |
| Accountability | Every action traceable to an individual |
| Time and context limits | Access only when and where needed |

### 3.3 Access control models

| Model | Who decides | How | Good for | Example |
|---|---|---|---|---|
| DAC (Discretionary) | Resource owner | Owner shares with others (ACLs) | Flexible file sharing | Owner shares a folder with a colleague |
| MAC (Mandatory) | System policy | Labels on data and clearances on users | Military, high security | Secret data readable only with Secret clearance |
| RBAC (Role-Based) | Administrator | Permissions attach to roles, users get roles | Most enterprises | "ATC Officer" role can edit flight plan |
| ABAC (Attribute-Based) | Policy engine | Rules on user, resource, and context attributes | Fine-grained, dynamic | Allow if role is ATC and airport matches and device is managed and time is in shift |
| Rule-based | System rules | Fixed rules such as time of day | Firewalls, schedule limits | Admin login only 08:00 to 20:00 |
| ReBAC (Relationship-Based) | Relationships | Access follows relationships | Social or document graphs | Manager can see reports of team |

### 3.4 Authentication supporting access

* Passwords with strong policy and **MFA** (see file 01 for factors).
* **SSO** (Single Sign-On): one login for many apps. Protocols: SAML, OAuth 2.0 (authorization), OpenID Connect (authentication on top of OAuth), Kerberos (ticket based, used in Windows domains).
* **Federation.** Trust between organizations (airport and airline login each other's users).
* **Directory services.** Active Directory or LDAP store users and groups.
* **Passwordless.** Passkeys, FIDO2 security keys.

### 3.5 Privileged access management (PAM)

Admin and service accounts are the crown jewels.

| Control | Meaning |
|---|---|
| Vaulting | Store admin passwords in a vault, rotate automatically |
| Just-in-time access | Elevate only for a short window |
| Session recording | Record admin sessions |
| Break-glass account | Emergency account, sealed, alarmed on use |
| Separate admin accounts | Do not use admin account for email and browsing |

### 3.6 Identity lifecycle (Joiner, Mover, Leaver)

| Event | Action |
|---|---|
| Joiner | Create account with minimum role access |
| Mover | Change role, remove old rights (stop **privilege creep**) |
| Leaver | Disable accounts immediately, retrieve devices and permits |

**Access recertification.** Managers periodically confirm that each person still needs each access.

### 3.7 Physical access control (airport view)

| Layer | Control |
|---|---|
| Perimeter | Fence, patrol, CCTV, vehicle checks |
| Landside to airside | Passenger screening, airport entry permits for staff, biometric or card gates |
| Critical areas (ATC, server room, fuel farm) | Multi-factor entry (card plus PIN or biometric), mantrap, logs |
| Visitors | Registration, escort, temporary badge |
| Anti-tailgating | Turnstiles, mantraps, cameras with analytics |

### 3.8 Python: RBAC and ABAC

```python
# RBAC
roles = {
    "ATC_Officer":   {"read_flight_plan", "update_flight_plan"},
    "Airline_Staff": {"read_flight_plan"},
    "IT_Admin":      {"read_logs", "manage_users"},
}
user_roles = {"asha": "ATC_Officer", "ravi": "Airline_Staff"}

def rbac_allowed(user, action):
    role = user_roles.get(user)
    return action in roles.get(role, set())

print(rbac_allowed("asha", "update_flight_plan"))   # True
print(rbac_allowed("ravi", "update_flight_plan"))   # False

# ABAC: decision uses attributes of user, resource, and context
def abac_allowed(user, action, resource, context):
    return (
        user["role"] == "ATC_Officer"
        and action == "update"
        and resource["type"] == "flight_plan"
        and user["airport"] == resource["airport"]
        and 6 <= context["hour"] < 22
        and context["device_managed"]
    )

asha = {"role": "ATC_Officer", "airport": "MAA"}
plan = {"type": "flight_plan", "airport": "MAA"}
print(abac_allowed(asha, "update", plan, {"hour": 10, "device_managed": True}))    # True
print(abac_allowed(asha, "update", plan, {"hour": 23, "device_managed": True}))    # False
print(abac_allowed(asha, "update", plan, {"hour": 10, "device_managed": False}))   # False
```

---

## 4. Encryption (Data Protection View)

Mechanics of algorithms are in file 03. Here: **where** to apply it and **how to manage** it.

### 4.1 Three states of data

| State | Meaning | Examples | Protection |
|---|---|---|---|
| At rest | Stored | Database, laptop disk, backup tape, cloud bucket | Full-disk encryption, TDE (Transparent Data Encryption), field-level encryption, encrypted backups |
| In transit (in motion) | Moving | Web, API, email, file transfer, Wi-Fi | TLS, VPN, SSH, SFTP, WPA3 |
| In use | Being processed in memory | Application reading data | Memory protection, confidential computing, secure enclaves, tokenization |

### 4.2 Levels of encryption at rest

| Level | Protects against | Note |
|---|---|---|
| Full-disk | Lost or stolen device | Does not help once the OS is running and logged in |
| File or folder | Unauthorized file access | Per user |
| Database (TDE) | Stolen database files | Transparent to apps |
| Column or field level | Admins and apps seeing raw values | Best for passport numbers, card numbers |
| Application-level or end-to-end | Even the server operator | Strongest, harder to search |

### 4.3 Key management essentials

* Keep keys **separate** from encrypted data.
* Use **HSM** or cloud **KMS**. Control who can use which key.
* **Envelope encryption.** A data key encrypts the data. A master key encrypts the data key.
* **Rotate** keys on schedule and after incidents.
* **BYOK** (Bring Your Own Key) and **HYOK** keep control with the customer.
* Backup keys securely. **Lost key means lost data** (this is the "utility" part of the Parkerian Hexad).

### 4.4 Python: envelope encryption

```python
from cryptography.fernet import Fernet

master = Fernet(Fernet.generate_key())    # in real life the master key stays inside a KMS or HSM

def encrypt_record(plaintext: bytes):
    data_key = Fernet.generate_key()               # unique key for this record
    ciphertext = Fernet(data_key).encrypt(plaintext)
    wrapped_key = master.encrypt(data_key)         # store this next to the ciphertext
    return ciphertext, wrapped_key

def decrypt_record(ciphertext: bytes, wrapped_key: bytes):
    data_key = master.decrypt(wrapped_key)
    return Fernet(data_key).decrypt(ciphertext)

ct, wk = encrypt_record(b"Ravi Kumar | Passport X1234567 | AI101")
print(decrypt_record(ct, wk))
```

Why this design: rotating the master key only requires re-wrapping small data keys, not re-encrypting all data.

### 4.5 Related protections

| Technique | Meaning |
|---|---|
| Hashing for integrity | Detect changes |
| Digital signatures | Prove origin and integrity |
| Secure deletion | Crypto-erase (destroy the key to make data unreadable) |
| Secure backups | Encrypt backups and test restore |

---

## 5. Data Minimization

### 5.1 Concept

**Analogy.** Pack only what you need for the trip. Less luggage means less to lose.

**Definition.** Collect, use, and keep only the personal data that is necessary for a clear purpose, and only for as long as needed. In your syllabus text, this appears as **"limiting data collection or windowing of data management"**, which means: limit how much you collect and limit the time window during which you hold and use it.

### 5.2 Related principles (privacy by design)

| Principle | Meaning | Airport example |
|---|---|---|
| Purpose limitation | Use data only for the stated purpose | Passport data collected for immigration is not used for marketing |
| Data minimization | Only what is necessary | Boarding app asks name and PNR, not family details |
| Storage limitation | Do not keep longer than necessary | Delete CCTV footage after retention period unless needed for an incident |
| Accuracy | Keep data correct | Allow passengers to fix typos |
| Integrity and confidentiality | Secure processing | Encryption, access control |
| Accountability | Be able to prove compliance | Records, audits |
| Lawfulness, fairness, transparency | Clear legal basis and notice | Privacy notice at booking |

**Privacy by Design (7 foundational principles).** Proactive not reactive, privacy as default setting, privacy embedded in design, full functionality (positive sum), end-to-end security through the lifecycle, visibility and transparency, respect for user privacy.

### 5.3 Techniques to reduce exposure

| Technique | What it does | Reversible? | Example |
|---|---|---|---|
| Masking (static or dynamic) | Hides part of the value | Original may still exist elsewhere | `XXXXXXXX1234` |
| Tokenization | Replaces value with a random token. Original in a secure vault | Yes, via vault | Card number becomes `tok_9f3a...` |
| Pseudonymization | Replaces identity with a code. Extra info kept separately | Yes, with extra info | Passenger ID becomes `P-7c1e...` (still personal data under law) |
| Anonymization | Removes identity permanently | No | Aggregate stats per route |
| Generalization | Reduce precision | No | Age 34 becomes "30 to 39" |
| Aggregation | Combine records | No | Daily passenger count |
| Truncation and redaction | Remove parts | No | Remove last octet of IP |
| Synthetic data | Fake but realistic data | n/a | Testing environments |
| Differential privacy | Add calibrated noise to results | No | Public statistics |
| k-anonymity | Each record is indistinguishable from at least k minus 1 others | n/a | See below |

**Re-identification risk.** Even without a name, a few details (age, ZIP, flight date) can single out a person. That is why anonymization needs checking.

### 5.4 Python: masking, tokenization, pseudonyms, k-anonymity

```python
import hashlib
import hmac
import secrets
from collections import Counter

# Masking
def mask_card(card):
    digits = "".join(c for c in card if c.isdigit())
    return "*" * (len(digits) - 4) + digits[-4:]

print(mask_card("4111 1111 1111 1111"))    # ************1111

# Tokenization with a vault
vault = {}
def tokenize(value):
    token = "tok_" + secrets.token_hex(8)
    vault[token] = value
    return token

def detokenize(token):
    return vault[token]

t = tokenize("X1234567")
print(t, detokenize(t))

# Pseudonymization with a keyed hash (the key is the separate extra information)
SECRET = b"store-this-in-a-vault-not-in-code"
def pseudonym(passenger_id):
    return hmac.new(SECRET, passenger_id.encode(), hashlib.sha256).hexdigest()[:16]

print(pseudonym("PAX-001"))

# k-anonymity: smallest group size across quasi-identifiers
def k_value(rows, quasi_ids):
    groups = Counter(tuple(r[q] for q in quasi_ids) for r in rows)
    return min(groups.values())

rows = [
    {"age_band": "30-39", "origin": "DEL"},
    {"age_band": "30-39", "origin": "DEL"},
    {"age_band": "40-49", "origin": "BOM"},
]
print(k_value(rows, ["age_band", "origin"]))   # 1 means one person is unique: risky
```

---

## 6. Windowing

The syllabus defines windowing as dividing a continuous or large dataset into smaller manageable chunks called **windows**, by time intervals or data counts, so real-time analysis can focus on relevant subsets.

### 6.1 Why windowing exists

**Analogy.** A river never stops, so you cannot weigh "all the water". You collect a bucket every 5 minutes and measure each bucket. The bucket is the window.

**Problems it solves**
* Streams (logs, flight events, network flows) are endless. You cannot wait for the data to "finish".
* You need answers now, such as "how many failed logins in the last minute?"
* Old data loses relevance and stored data is a liability (links to data minimization).

**Security uses**
* Detect brute force (5 failed logins in 60 seconds).
* Spot DDoS (requests per IP per 10 seconds).
* Compute rolling metrics for dashboards (file 05).
* Limit retention (keep only the last 90 days of raw logs).
* Correlate events that happen close in time (login then data download).

### 6.2 Window types

| Window | Definition | Overlap | Output frequency | Airport example |
|---|---|---|---|---|
| **Tumbling** | Fixed size, back to back | None | Once per window | Landings per 5 minutes: 10:00 to 10:05, 10:05 to 10:10 |
| **Sliding (hopping)** | Fixed size, moves forward by a smaller step | Yes | Every step | Landings in the last 10 minutes, updated every 2 minutes |
| **Session** | Dynamic. Group events separated by gaps smaller than a timeout | None per key | When the gap closes it | One passenger's burst of app activity, ends after 15 minutes of silence |
| **Count-based** | Fixed number of events | Can be tumbling or sliding | After N events | Every 100 baggage scans |
| **Global** | One window with all data and custom trigger | n/a | On trigger | Total running count |

Terms: **window size** (length), **slide or hop** (step), **gap or timeout** (for sessions).

### 6.3 Time concepts

| Term | Meaning |
|---|---|
| Event time | When the event actually happened (timestamp in the data) |
| Processing time | When the system saw it |
| Ingestion time | When it entered the pipeline |
| Late data | Event arrives after its window time has passed (network delay) |
| Watermark | A moving marker meaning "we believe all events older than this have arrived". Used to close windows |
| Allowed lateness | Extra time to accept late events before dropping or sending them to a side output |

Tools that offer windows: Apache Kafka Streams, Apache Flink, Spark Structured Streaming, Azure Stream Analytics, SQL window functions, SIEM engines. (Treat tool names as awareness only.)

### 6.4 Sample data

Minutes since 10:00. Flights landing:

```python
events = [(1, "AI101"), (3, "6E202"), (4, "AI303"),
          (9, "UK404"), (12, "AI505"), (31, "6E606")]
```

### 6.5 Python: the four windows

```python
from collections import defaultdict

events = [(1, "AI101"), (3, "6E202"), (4, "AI303"),
          (9, "UK404"), (12, "AI505"), (31, "6E606")]

def tumbling(events, size):
    windows = defaultdict(list)
    for t, flight in events:
        start = (t // size) * size
        windows[start].append(flight)
    return dict(sorted(windows.items()))

def sliding(events, size, step):
    last = max(t for t, _ in events)
    out, start = {}, 0
    while start <= last:
        out[(start, start + size)] = [f for t, f in events if start <= t < start + size]
        start += step
    return out

def session(events, gap):
    sessions, current = [], [events[0]]
    for prev, now in zip(events, events[1:]):
        if now[0] - prev[0] > gap:
            sessions.append(current)
            current = []
        current.append(now)
    sessions.append(current)
    return sessions

def count_windows(events, n):
    return [events[i:i + n] for i in range(0, len(events), n)]

print(tumbling(events, 5))
# {0: ['AI101', '6E202', 'AI303'], 5: ['UK404'], 10: ['AI505'], 30: ['6E606']}

print(sliding(events, 10, 5))
# {(0, 10): [AI101, 6E202, AI303, UK404], (5, 15): [UK404, AI505], (10, 20): [AI505], ...}

print(session(events, 10))
# [[(1,'AI101'), (3,'6E202'), (4,'AI303'), (9,'UK404'), (12,'AI505')], [(31,'6E606')]]

print(count_windows(events, 3))
# [[first three events], [next three events]]
```

**Trace of the tumbling window** (size 5):
* t=1 goes to window 0 (1 // 5 = 0)
* t=3 and t=4 also window 0
* t=9 goes to window 5 (9 // 5 = 1, times 5)
* t=12 goes to window 10
* t=31 goes to window 30

**Why the first session closes.** Gaps are 2, 1, 5, 3, then 19. With timeout 10, only 19 is larger, so the session splits before 31.

### 6.6 Python: watermarks and late events

```python
from collections import defaultdict

def process_with_watermark(stream, size, allowed_lateness):
    windows = defaultdict(list)
    closed = set()
    late = []
    watermark = 0
    for event_time, flight in stream:
        start = (event_time // size) * size
        if start in closed:
            late.append((event_time, flight))     # too late, window already emitted
            continue
        windows[start].append(flight)
        watermark = max(watermark, event_time - allowed_lateness)
        for w in sorted(windows):
            if w + size <= watermark and w not in closed:
                closed.add(w)
                print(f"window {w}-{w + size} closed: {windows[w]}")
    return late

# events arrive out of order
stream = [(1, "A"), (4, "B"), (8, "C"), (3, "D"), (12, "E"), (2, "F")]
late_events = process_with_watermark(stream, size=5, allowed_lateness=2)
print("late:", late_events)
# window 0-5 closed: ['A', 'B']
# window 5-10 closed: ['C']
# late: [(3, 'D'), (2, 'F')]
```

### 6.7 Python: brute force detection with a sliding window

```python
from collections import defaultdict, deque

class BruteForceDetector:
    def __init__(self, limit=5, window_seconds=60):
        self.limit = limit
        self.window = window_seconds
        self.attempts = defaultdict(deque)

    def record_failure(self, user, ts):
        q = self.attempts[user]
        q.append(ts)
        while q and ts - q[0] > self.window:
            q.popleft()
        return len(q) >= self.limit     # True means raise an alert

d = BruteForceDetector(limit=5, window_seconds=60)
for ts in (0, 10, 20, 30, 40):
    alert = d.record_failure("atc_user1", ts)
print("ALERT" if alert else "ok")       # ALERT (5 failures inside 60 seconds)
```

### 6.8 Windowing and retention (links to minimization)

Retention window example policy:

| Data | Retention window |
|---|---|
| Raw access logs | 90 days online, 1 year archived |
| CCTV footage | Per regulation and policy (for example 30 to 90 days) unless held for an incident |
| Passenger booking data | As required by law and business need, then delete or anonymize |
| Backups | Rotation schedule with defined expiry |

---

## 7. Audit and Monitoring

### 7.1 Concept

**Analogy.** CCTV plus a visitor register at the airport. It shows who entered, when, and what they did, so misuse can be spotted and proven.

**Definition.** Continuously tracking and reviewing data access and system activity to detect anomalies and misuse, and to provide evidence for investigations and compliance.

### 7.2 Audit vs monitoring

| | Monitoring | Audit |
|---|---|---|
| Time | Continuous, near real time | Periodic or event driven |
| Aim | Detect and respond now | Check compliance and review the past |
| Output | Alerts | Reports and findings |

### 7.3 What to log (the 5 W's)

| Item | Example |
|---|---|
| Who | User ID, service account |
| What | Action (view, edit, delete, export, login failed) |
| When | Timestamp in synchronized time (NTP, UTC) |
| Where | Source IP, device, location, system |
| Result | Success or failure, record ID |

**Log sources.** Operating systems, applications, databases, firewalls, IDS, VPN, DNS, proxy, email gateway, cloud audit trails, physical access systems, endpoint tools.

### 7.4 Good logging practice

* Centralize logs in a **SIEM**. Send them off the device immediately.
* **Protect integrity** (write once, hash chain, restricted access).
* Do **not** log secrets such as passwords or full card numbers.
* Define **retention** (for example per CERT-In directions, logs of ICT systems are to be kept for 180 days within India as per its 2022 directions. Check current rules).
* Synchronize clocks.
* Review alerts, not just collect.

### 7.5 Detection ideas

| Anomaly | Why suspicious |
|---|---|
| Login at 3 AM from unusual country | Possible stolen credentials |
| Impossible travel (Chennai then London in 10 minutes) | Account sharing or compromise |
| Mass download of passenger records | Data theft |
| Privileged account used outside change window | Misuse |
| Many failed logins then a success | Brute force succeeded |
| New admin account created | Persistence |
| Access to unrelated data by an employee | Snooping |
| Logs cleared | Attacker covering tracks |

**UEBA** (User and Entity Behavior Analytics) learns normal behavior and flags deviations.

### 7.6 Python: tamper-evident audit log

```python
import hashlib
import json
import time

class AuditLog:
    def __init__(self):
        self.entries = []

    def add(self, user, action, resource):
        prev_hash = self.entries[-1]["hash"] if self.entries else "0" * 64
        body = {"ts": time.time(), "user": user, "action": action,
                "resource": resource, "prev": prev_hash}
        digest = hashlib.sha256(json.dumps(body, sort_keys=True).encode()).hexdigest()
        self.entries.append({**body, "hash": digest})

    def verify(self):
        prev = "0" * 64
        for e in self.entries:
            body = {k: e[k] for k in ("ts", "user", "action", "resource", "prev")}
            expected = hashlib.sha256(json.dumps(body, sort_keys=True).encode()).hexdigest()
            if e["prev"] != prev or e["hash"] != expected:
                return False
            prev = e["hash"]
        return True

log = AuditLog()
log.add("ravi", "VIEW", "passenger_list/AI101")
log.add("asha", "UPDATE", "flight_plan/AI101")
print(log.verify())                       # True
log.entries[0]["user"] = "someone_else"   # attacker edits history
print(log.verify())                       # False: tampering detected
```

### 7.7 Python: simple behavior anomaly rules

```python
from collections import Counter

events = [
    {"user": "ravi", "hour": 10, "action": "VIEW"},
    {"user": "ravi", "hour": 11, "action": "VIEW"},
    {"user": "ravi", "hour": 3,  "action": "EXPORT"},
    {"user": "meena", "hour": 14, "action": "VIEW"},
]

def suspicious(e):
    reasons = []
    if e["hour"] < 6 or e["hour"] > 22:
        reasons.append("off-hours")
    if e["action"] == "EXPORT":
        reasons.append("bulk export")
    return reasons

for e in events:
    r = suspicious(e)
    if r:
        print("ALERT", e["user"], r)    # ALERT ravi ['off-hours', 'bulk export']
```

---

## 8. Incident Response (IR)

### 8.1 Concept

**Analogy.** Airport emergency plan: who raises the alarm, who leads, which gates close, who talks to the media, how normal flights resume, and what lessons are written afterwards.

**Definition.** A planned way to detect, contain, and recover from security incidents quickly, limiting damage and cost.

**Event vs incident vs breach.** Event: any observable occurrence. Incident: event that threatens security or violates policy. Breach: confirmed unauthorized access to or loss of data.

### 8.2 Frameworks

| NIST SP 800-61 | SANS PICERL | Meaning |
|---|---|---|
| Preparation | Preparation | Team, tools, plans, training, backups, contact lists |
| Detection and analysis | Identification | Spot, confirm, scope, prioritize |
| Containment, eradication, recovery | Containment | Stop spread (isolate host, block IP) |
| | Eradication | Remove malware, close hole, reset credentials |
| | Recovery | Restore from clean backups, monitor closely |
| Post-incident activity | Lessons learned | Report, root cause, improve |

### 8.3 Example: ransomware at an airline

1. **Detect.** EDR alert: mass file encryption on a check-in server.
2. **Triage.** Severity Critical. Notify IR lead and management.
3. **Contain.** Isolate the server and disable affected accounts. Segment off affected VLAN.
4. **Preserve evidence.** Memory and disk images, logs. Keep chain of custody.
5. **Eradicate.** Remove malware, patch the entry point, reset credentials.
6. **Recover.** Rebuild from clean images, restore offline backups, verify integrity, bring back systems in priority order.
7. **Notify.** Report to CERT-In, regulators, and affected passengers as required. Coordinate communications.
8. **Learn.** Root cause (phishing and unpatched VPN), update controls, run drills.

### 8.4 IR team and roles

| Role | Job |
|---|---|
| Incident commander | Leads and decides |
| Security analysts | Investigate |
| IT and network engineers | Contain and restore |
| Forensics | Preserve and analyze evidence |
| Legal and compliance | Regulatory duties |
| Communications | Staff, media, customers |
| Management | Business decisions |
| Vendors and CSIRT or CERT contacts | Outside help |

### 8.5 Severity and playbooks

| Severity | Description | Example | Target response |
|---|---|---|---|
| Critical | Safety or major outage, large data loss | ATC support system ransomware | Immediate, all hands |
| High | Serious impact | Passenger database exfiltration | Within 1 hour |
| Medium | Limited impact | Single malware infection | Same day |
| Low | Minor | Blocked phishing | Routine |

**Playbook.** Predefined step list for each type (phishing, ransomware, lost laptop, DDoS, insider). **Runbook** is the technical version. **Tabletop exercise.** Discussion-based drill. **Red team and purple team** exercises test real detection.

### 8.6 Digital forensics basics

* **Order of volatility.** Collect the most fleeting first: CPU registers and cache, memory (RAM), network state, running processes, disk, remote logs, backups.
* **Chain of custody.** Written record of who held the evidence and when.
* **Hashing.** Hash the disk image to prove it was not changed.
* **Write blockers.** Prevent changes while copying.
* **Legal admissibility.** Follow proper process.

### 8.7 Breach notification (key deadlines)

| Rule | Deadline |
|---|---|
| CERT-In directions (April 2022) | Report specified cyber incidents within **6 hours** of noticing |
| GDPR | Notify the supervisory authority within **72 hours** of becoming aware of a personal data breach (unless unlikely to cause risk) |
| DPDP Act 2023 (India) | Notify the Data Protection Board and affected Data Principals of a personal data breach, in the form and time prescribed by the rules. Check current rules |

```python
from datetime import datetime, timedelta

noticed = datetime(2026, 10, 20, 9, 30)
print("CERT-In deadline:", noticed + timedelta(hours=6))
print("GDPR deadline   :", noticed + timedelta(hours=72))
```

### 8.8 Business continuity and disaster recovery (BCP and DRP)

| Term | Meaning |
|---|---|
| BCP | Plan to keep critical business functions running during disruption |
| DRP | Plan to recover IT systems and data |
| BIA | Business Impact Analysis, decides priorities |
| RTO | Maximum acceptable time to restore |
| RPO | Maximum acceptable data loss measured in time |
| MTD or MAO | Maximum tolerable downtime |
| Hot site | Fully equipped, ready in minutes or hours |
| Warm site | Partly ready, hours to days |
| Cold site | Space and power only, days to weeks |
| Cloud DR | Recover into the cloud |

**Backup rule 3-2-1** (modern: **3-2-1-1-0**): 3 copies, 2 different media, 1 offsite, 1 offline or immutable, 0 errors after verification.

**Backup types.** Full, incremental (changes since last backup of any kind), differential (changes since last full).

```python
from datetime import datetime, timedelta

def meets_rpo(last_backup, now, rpo_minutes):
    return now - last_backup <= timedelta(minutes=rpo_minutes)

now = datetime(2026, 10, 20, 12, 0)
print(meets_rpo(datetime(2026, 10, 20, 11, 58), now, 5))    # True (2 minutes old)
print(meets_rpo(datetime(2026, 10, 20, 11, 30), now, 5))    # False (30 minutes old)
```

---

## 9. Data Subject Rights

### 9.1 Concept

**Analogy.** It is your luggage. You should be able to ask the airline what it is holding, fix any wrong labels, and ask for it back or destroyed.

**Definition.** Rights of individuals over their personal data that organizations must support.

### 9.2 GDPR (European Union)

Applies to data of people in the EU, even for organizations outside the EU. Fines up to 4 percent of global annual turnover or 20 million euro, whichever is higher, for the most serious violations.

| Right | Meaning |
|---|---|
| Right to be informed | Know how data is used (privacy notice) |
| Right of access | Get a copy of own data |
| Right to rectification | Fix wrong data |
| Right to erasure ("right to be forgotten") | Delete data in certain cases |
| Right to restrict processing | Pause use |
| Right to data portability | Receive data in a usable format and move it |
| Right to object | Stop processing such as direct marketing |
| Rights on automated decisions and profiling | Human review |

Legal bases for processing: consent, contract, legal obligation, vital interests, public task, legitimate interests.

### 9.3 India: Digital Personal Data Protection Act, 2023 (DPDP)

| Term | Meaning |
|---|---|
| Data Principal | The individual (like data subject) |
| Data Fiduciary | Entity that decides purpose and means (like controller) |
| Data Processor | Processes on behalf of the fiduciary |
| Significant Data Fiduciary | Larger or riskier fiduciary with extra duties (DPO in India, independent auditor, impact assessments) |
| Consent Manager | Registered entity helping people manage consent |
| Data Protection Board of India | Adjudicating body |

**Rights of the Data Principal:** right to access information about processing, right to correction and erasure, right of grievance redressal, right to nominate another person to exercise rights on death or incapacity.

**Key duties of fiduciaries:** process for lawful purpose with consent (or certain legitimate uses), give clear notice, take **reasonable security safeguards**, report breaches, erase data when purpose is over or consent withdrawn, special care for children's data (verifiable parental consent, no tracking or targeted ads to children).

**Penalties** go up to Rs 250 crore for failure to take reasonable security safeguards. Rules under the Act are being brought into force in phases, so confirm the latest status before the exam.

### 9.4 IT Act, 2000 (India), important sections

| Section | Subject |
|---|---|
| 43 | Penalty and compensation for unauthorized access, damage, data theft, virus |
| 43A | Compensation for failure to protect sensitive personal data (SPDI Rules 2011). Being replaced under DPDP when fully in force |
| 65 | Tampering with computer source documents |
| 66 | Computer related offences (dishonest or fraudulent acts under section 43) |
| 66B | Receiving stolen computer resource |
| 66C | Identity theft |
| 66D | Cheating by personation using computer resource |
| 66E | Violation of privacy |
| 66F | Cyber terrorism |
| 67 | Publishing obscene material electronically |
| 69 | Government power to intercept, monitor, decrypt |
| 69A | Blocking public access to information |
| 70 | Protected systems. Declared by government |
| 70A | National Nodal Agency for Critical Information Infrastructure (NCIIPC) |
| 70B | CERT-In as national incident response agency |
| 72 | Breach of confidentiality and privacy |
| 72A | Disclosure of information in breach of lawful contract |

### 9.5 Handling a request (process)

1. Receive request through a defined channel.
2. **Verify identity** (do not leak data to an imposter).
3. Log the request and start the clock (GDPR: one month, extendable).
4. Locate data across all systems (including backups and processors).
5. Fulfil: provide, correct, delete, restrict, or port.
6. Inform processors and recipients.
7. Respond and record the outcome. If refused (legal need to retain), explain why.

### 9.6 Python: a simple data subject request handler

```python
store = {
    "PAX-001": {"name": "Ravi Kumar", "phone": "9876543210", "flights": ["AI101"]},
    "PAX-002": {"name": "Meena S", "phone": "9123456780", "flights": ["6E202"]},
}
request_log = []

def handle_request(pax_id, request, new_data=None):
    request_log.append((pax_id, request))
    if pax_id not in store:
        return "No data found"
    if request == "access":
        return store[pax_id]
    if request == "rectify":
        store[pax_id].update(new_data)
        return "Updated"
    if request == "erase":
        del store[pax_id]
        return "Erased"
    return "Unsupported request"

print(handle_request("PAX-001", "access"))
print(handle_request("PAX-001", "rectify", {"phone": "9000000000"}))
print(handle_request("PAX-002", "erase"))
print(handle_request("PAX-002", "access"))     # No data found
```

---

## 10. Data Protection Practices and Policies

### 10.1 Key practices

| Practice | Meaning |
|---|---|
| Data inventory and mapping | Know what data, where, who, why. Maintain a **Record of Processing Activities (RoPA)** |
| DPIA | Data Protection Impact Assessment before risky new processing |
| Privacy notice and consent | Clear and understandable, easy to withdraw |
| Privacy by design and by default | Built-in from the start |
| Vendor and third-party management | Contracts (Data Processing Agreements), audits, limit access |
| Cross-border transfer controls | Check legal basis and restrictions |
| Backup and recovery | 3-2-1-1-0 and tested restores |
| Retention and disposal | Delete on time and securely |
| DLP | Detect and block leakage via email, USB, cloud |
| Patch and vulnerability management | Close known holes |
| Security awareness | Regular training and phishing tests |
| Incident readiness | Plans, drills, contacts |
| Physical protection | Locks, shredders, clean desk |
| Governance | DPO or privacy lead, steering committee, reporting to management |

### 10.2 Secure disposal (NIST SP 800-88)

| Level | Meaning | Example |
|---|---|---|
| Clear | Overwrite using normal commands | Reuse inside the organization |
| Purge | Make recovery infeasible even with lab techniques (cryptographic erase, degauss) | Reuse outside or return to vendor |
| Destroy | Physically destroy | Shredding drives, incineration |

Paper: cross-cut shredding. SSD: crypto-erase or destroy. Keep a certificate of destruction.

### 10.3 Common policies

| Policy | Purpose |
|---|---|
| Information Security Policy | Top-level management commitment and scope |
| Acceptable Use Policy | What staff may and may not do with IT assets |
| Access Control Policy | Rules for granting and reviewing access |
| Password and Authentication Policy | Length, MFA, lockout |
| Data Classification and Handling Policy | Labels and rules |
| Data Retention and Disposal Policy | Time limits and destruction |
| Encryption and Key Management Policy | Where and how |
| Backup Policy | Frequency, offsite, testing |
| BYOD and Mobile Policy | Personal devices |
| Remote Work Policy | VPN, device standards |
| Incident Response Policy | Roles and reporting |
| Vendor and Third-Party Policy | Due diligence and contracts |
| Clean Desk and Clear Screen Policy | Protect paper and screens |
| Change Management Policy | Controlled, approved changes |
| Privacy Policy and Notice | Public statement of data handling |
| Business Continuity Policy | Resilience |

### 10.4 Frameworks and standards

| Framework | What it gives |
|---|---|
| ISO/IEC 27001:2022 | Requirements for an ISMS. Uses Plan, Do, Check, Act. Annex A has 93 controls in 4 themes: Organizational (37), People (8), Physical (14), Technological (34) |
| ISO/IEC 27002 | Guidance for those controls |
| ISO/IEC 27701 | Privacy extension to 27001 (privacy information management) |
| ISO/IEC 22301 | Business continuity management |
| NIST Cybersecurity Framework 2.0 | Six functions: Govern, Identify, Protect, Detect, Respond, Recover |
| NIST Privacy Framework | Privacy risk management |
| NIST SP 800-53 | Detailed control catalogue |
| NIST SP 800-61 | Incident handling guide |
| NIST SP 800-88 | Media sanitization |
| CIS Controls | Prioritized practical safeguards |
| COBIT | IT governance |
| PCI DSS | Payment card data protection (airline ticket payments) |
| SOC 2 | Audit of service providers on security, availability, confidentiality, processing integrity, privacy |
| GDPR | EU data protection law |
| DPDP Act 2023 | India personal data law |
| IT Act 2000 and SPDI Rules 2011 | Indian cyber law |
| CERT-In directions 2022 | Incident reporting and log retention |
| National Cyber Security Policy and sectoral guidance (for example for aviation and CII) | Government direction |

### 10.5 Roles to remember

| Role | Responsibility |
|---|---|
| CISO | Head of information security |
| DPO | Advises on data protection compliance |
| Data owner | Decides classification and access |
| Data custodian | Runs the systems holding the data |
| System owner | Accountable for a system |
| Auditor | Checks controls independently |

---

## 11. Putting it all together: Passenger Data Case

Scenario: An airline stores passenger records (name, passport, phone, card).

| Element | Applied how |
|---|---|
| Classification | Passport and card data are Restricted. Name and phone are Confidential |
| Access control | RBAC. Check-in agents see only the flights they handle. Admin access through PAM with MFA |
| Encryption | TLS in transit. Database TDE plus field-level encryption for passport and card. Keys in an HSM |
| Minimization | Card data tokenized by the payment provider. Delete or anonymize records after the retention window |
| Windowing | Real-time rule: more than 5 failed logins in 60 seconds triggers lockout. Retention windows delete old logs |
| Audit and monitoring | Every view of a passport record is logged and sent to the SIEM. UEBA flags bulk exports |
| Incident response | Playbook for data breach, CERT-In reporting within 6 hours, communication plan |
| Data subject rights | Portal to access, correct, and request deletion, with identity checks |
| Policies | Classification, retention, access, vendor, and incident policies approved and reviewed yearly |

---

## 12. Exam Corner

### One-mark facts

* Elements: classification, access control, encryption, data minimization (windowing), audit and monitoring, incident response, data subject rights, policies.
* Classification levels: Public, Internal, Confidential, Restricted.
* RBAC uses roles. ABAC uses attributes. MAC uses labels. DAC lets owners decide.
* Encryption states: at rest, in transit, in use.
* Data minimization: collect and keep only what is needed.
* Masking hides part of data. Tokenization replaces data with a token. Anonymization is irreversible. Pseudonymization is reversible with extra information.
* Windowing: dividing a continuous data stream into smaller chunks by time or count.
* Tumbling = fixed and no overlap. Sliding = overlapping. Session = activity bursts separated by inactivity. Count-based = fixed number of events.
* Watermark: marker used to decide when a time window is complete.
* Audit trail records who, what, when, where.
* NIST IR phases: Preparation, Detection and Analysis, Containment Eradication and Recovery, Post-Incident Activity.
* RTO is time. RPO is data loss. Backup rule is 3-2-1.
* GDPR: 72 hours to notify. CERT-In: 6 hours to report.
* DPDP Act terms: Data Principal, Data Fiduciary, Data Protection Board.
* IT Act: 66C identity theft, 66D cheating by personation, 66F cyber terrorism, 70B CERT-In, 70A NCIIPC, 72 breach of confidentiality.
* ISO 27001 = ISMS. NIST CSF 2.0 has six functions including Govern.

### Common traps

1. Confusing anonymization and pseudonymization. Pseudonymized data is still personal data.
2. Thinking encryption at rest protects data from a logged-in malicious user. It does not.
3. Saying tumbling windows overlap. They do not.
4. Forgetting that session windows have no fixed length.
5. Saying RPO is time to restore. That is RTO.
6. Treating compliance as security. Compliance is a minimum.
7. Keeping logs forever. Retention should be defined.

### Self-check (answers below)

1. Name the four commercial classification levels.
2. Which access control model uses security labels and clearances?
3. What is envelope encryption?
4. Define tumbling window and give one security use.
5. What is the purpose of a watermark in stream processing?
6. What is the difference between tokenization and masking?
7. What is chain of custody?
8. Under DPDP Act 2023, who is the Data Fiduciary?
9. State the 3-2-1 backup rule.
10. Which ISO standard is for an ISMS?

**Answers.** 1 Public, Internal, Confidential, Restricted. 2 MAC. 3 A data key encrypts data and a master key encrypts the data key. 4 Fixed-size non-overlapping time slices. Use: count failed logins per minute. 5 It tells the system that no more events older than a time point are expected, so the window can be closed. 6 Masking hides part of the value. Tokenization replaces the whole value with a token and keeps the original in a vault. 7 Documented record of who handled evidence and when. 8 The entity that decides the purpose and means of processing personal data (such as the airline). 9 Three copies, two media types, one offsite. 10 ISO/IEC 27001.

---

*End of file 04. Continue with 05-cybersecurity-dashboarding.md*
