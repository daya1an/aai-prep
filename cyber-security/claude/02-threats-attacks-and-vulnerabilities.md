# 02. Threat Vectors, Cyberattacks and Vulnerabilities

AAI IT Manager I. Subject: Cybersecurity Fundamentals. Part 2 of 5.

All code in this file is defensive. It shows a weakness only to show the fix next to it.

---

## 1. Threat, Vulnerability, Exploit, Attack: how they connect

**Analogy.** A thief (threat actor) notices a window with a broken latch (vulnerability), uses a crowbar (exploit), and enters to steal (attack). The loss is the impact.

```
Threat actor  --uses-->  Threat vector  --reaches-->  Vulnerability  --exploited by-->  Exploit  --causes-->  Impact
```

| Term | Meaning |
|---|---|
| Threat actor | Who (person or group) |
| Threat vector or attack vector | The path used to reach the target |
| Attack surface | Sum of all possible entry points |
| Vulnerability | The weakness |
| Exploit | Tool or method that uses the weakness |
| Payload | The harmful part delivered after exploit (ransomware, backdoor) |
| Indicator of Compromise (IoC) | Evidence of attack (bad IP, file hash, strange login) |
| TTP | Tactics, Techniques and Procedures of an attacker |

---

## 2. Threat Vectors

A threat vector is the route. For each: how it works, airport trace, defense.

### 2.1 Email

* **How.** Phishing mail with a link or attachment.
* **Trace.** Fake "Crew roster update" mail with an infected spreadsheet reaches an airline planner.
* **Defense.** Spam filter, attachment sandboxing, SPF, DKIM, DMARC, awareness training, disabling macros.

### 2.2 Web

* **How.** Malicious or fake websites, drive-by downloads, compromised third-party scripts.
* **Trace.** Fake "airport parking booking" site steals card details.
* **Defense.** Web filtering, DNS filtering, HTTPS checks, browser isolation, Content Security Policy.

### 2.3 Removable media

* **How.** Infected USB, external drives, even charging cables.
* **Trace.** A found USB labelled "Salary 2026" is plugged into a terminal PC.
* **Defense.** Block USB storage by policy, scan media on a kiosk, endpoint control.

### 2.4 Wireless

* **How.** Fake hotspot, weak Wi-Fi encryption, Bluetooth attacks.
* **Trace.** "Airport_Free_WiFi" fake hotspot captures logins.
* **Defense.** WPA3 or WPA2 Enterprise, wireless IPS, VPN for staff, guest isolation.

### 2.5 Social engineering (human vector)

* **How.** Manipulating people instead of machines.
* **Trace.** A caller says "I am from IT helpdesk, give me your OTP to fix your account."
* **Defense.** Verification procedures, never share OTP, training, culture of reporting.

### 2.6 Insider

* **How.** Employee, contractor, or partner misuses legitimate access (malicious or careless).
* **Trace.** A staff member copies the frequent flyer database to a personal drive.
* **Defense.** Least privilege, DLP, monitoring, exit procedures, separation of duties.

### 2.7 Supply chain and third party

* **How.** Attack a vendor and ride in through trusted software or access.
* **Trace.** A malicious update in a baggage-system vendor's software pushes malware into 20 airports.
* **Defense.** Vendor risk assessment, signed updates, SBOM (Software Bill of Materials), segmented vendor access, contract security clauses.

### 2.8 Cloud

* **How.** Misconfiguration, stolen cloud keys, weak IAM.
* **Trace.** Cloud storage with passenger scans is set to "public".
* **Defense.** Cloud posture management, least privilege IAM, key rotation, logging.

### 2.9 IoT and OT devices

* **How.** Default passwords, no patching, direct internet exposure.
* **Trace.** Internet-facing CCTV with password "admin" becomes part of a botnet.
* **Defense.** Change defaults, segment, firmware updates, disable unused services.

### 2.10 Remote access

* **How.** Open RDP or VPN with weak passwords.
* **Trace.** An attacker guesses the password for a vendor's remote desktop and installs ransomware.
* **Defense.** VPN with MFA, no direct RDP to the internet, account lockout, jump host.

### 2.11 Physical

* **How.** Walk in, plug in a device, steal equipment, tailgate.
* **Trace.** Stranger follows a technician into the server room.
* **Defense.** Mantraps, badge checks, visitor escort, CCTV, locked racks.

### 2.12 Mobile apps and personal devices (BYOD)

* **How.** Malicious apps, jailbroken phones, lost devices.
* **Defense.** MDM, app whitelisting, remote wipe, containerization.

### 2.13 Software vulnerabilities

* **How.** Unpatched OS, libraries, and applications.
* **Defense.** Patch management, vulnerability scanning, secure development.

### 2.14 Other vectors

| Vector | Example |
|---|---|
| Credentials | Leaked passwords reused on airline portal |
| Open ports and services | Telnet enabled on a switch |
| Shadow IT | Staff share rosters using personal cloud drives |
| Public information (OSINT) | LinkedIn posts reveal the tech stack |
| Radio signals | GPS and ADS-B spoofing and jamming |
| AI-enabled | Deepfake voice call imitating a senior officer |

---

## 3. Types of Cyberattacks

### 3.1 Malware (malicious software)

**Analogy.** Different kinds of pests. Each behaves differently.

| Type | How it works | Spread | Airport example |
|---|---|---|---|
| Virus | Attaches to a file and runs when the file is opened | Needs user action | Infected spreadsheet of crew roster |
| Worm | Self-replicates across the network | Automatic | Spreads across all check-in PCs |
| Trojan | Looks useful, hides harmful code | User installs it | "Free boarding pass tool" |
| Ransomware | Encrypts files, demands payment | Phishing, exploits | Baggage system data locked |
| Spyware | Secretly collects data | Bundled software | Watches staff activity |
| Keylogger | Records key presses | Trojan or hardware | Steals ATC portal password |
| Rootkit | Hides deep, often at OS or firmware level | After privilege gain | Hides attacker's processes |
| Backdoor | Secret way in | Planted | Hidden admin account |
| Botnet and bot | Infected machines controlled remotely | Worm, trojan | Cameras used for DDoS |
| RAT | Remote Access Trojan, full remote control | Phishing | Attacker uses staff PC |
| Logic bomb | Triggers when a condition is met (date, deletion of an account) | Planted by insider | Deletes data after the admin is fired |
| Adware | Unwanted ads | Free software | Pop-ups on kiosks |
| Fileless malware | Runs in memory using built-in tools like PowerShell | Phishing, exploit | Leaves no file for antivirus |
| Wiper | Destroys data without ransom | Targeted | Destroys servers during conflict |
| Cryptojacker | Uses your CPU to mine coins | Web or malware | Slow servers |
| Scareware | Fake virus warnings that sell fake cleaners | Web | Pop-up on staff PC |

#### Ransomware in detail

1. **Entry.** Phishing mail, exposed RDP, or unpatched VPN device.
2. **Foothold.** Malware installs and calls home (C2).
3. **Spread.** Steals credentials and moves laterally to many servers.
4. **Data theft.** Copies sensitive data first.
5. **Encryption.** Locks files across systems, deletes backups if reachable.
6. **Demand.** Ransom note.
7. **Double extortion.** "Pay or we publish your data" on top of "pay to unlock".

**Defense.** Offline and immutable backups, segmentation, patching, MFA, EDR, email security, tested restore. **Do not rely on paying.** Payment does not guarantee recovery and funds crime.

#### Malware analysis (basic terms)

* **Static analysis.** Study the file without running it (strings, hashes).
* **Dynamic analysis.** Run it in a **sandbox** and watch behavior.
* **Signature.** Fingerprint of known malware. Fails for brand new malware.
* **Heuristic or behavior detection.** Judges by actions.

### 3.2 Social engineering

| Attack | Meaning | Example |
|---|---|---|
| Phishing | Mass fraudulent messages | "Your flight is cancelled, click to rebook" |
| Spear phishing | Targeted at one person with personal details | Mail to an airline finance officer using real vendor names |
| Whaling | Targets senior executives | Mail to the airport director about a legal notice |
| Business Email Compromise (BEC) | Impersonates an executive or vendor to move money | "Change the vendor bank account" |
| Vishing | Voice call | "IT helpdesk" asks for OTP |
| Smishing | SMS | "Refund pending, tap link" |
| Quishing | QR code phishing | Fake QR code sticker on parking machine |
| Pretexting | Invented story to obtain data | "I am an auditor, I need the passenger list" |
| Baiting | Tempting item such as free USB | USB left in the lounge |
| Quid pro quo | Offers a service in exchange for info | "Free tech support if you share the password" |
| Tailgating or piggybacking | Follows a person through a secure door | Follows staff into the server room |
| Shoulder surfing | Watching screens or keypads | Watches PIN entry |
| Dumpster diving | Searching trash | Finds printed boarding data |
| Impersonation | Pretends to be staff or vendor | Fake uniform and ID |
| Deepfake fraud | AI voice or video clone | Cloned voice of the CEO requests urgent transfer |

**How to spot phishing.** Urgency, threats, unusual sender address, mismatched link (hover to see), spelling errors, unexpected attachment, request for password or OTP.

```python
import re
from urllib.parse import urlparse

def phishing_score(url):
    host = urlparse(url).netloc.lower()
    score = 0
    if re.fullmatch(r"\d{1,3}(\.\d{1,3}){3}", host):
        score += 2                      # raw IP instead of a name
    if "@" in url:
        score += 2                      # credentials trick
    if host.count("-") >= 2:
        score += 1                      # many hyphens
    if host.count(".") >= 4:
        score += 1                      # long subdomain chain
    if url.startswith("http://"):
        score += 1                      # no HTTPS
    if any(w in host for w in ("login", "secure", "verify", "update")):
        score += 1                      # bait words
    return score

print(phishing_score("http://secure-login-airindia.verify-account.example.xyz/login"))
print(phishing_score("https://www.airindia.com"))
```

This is only a toy. Real filters use reputation databases, ML, and sandbox detonation.

### 3.3 Denial of Service (DoS and DDoS)

**Analogy.** A thousand people crowd the check-in counter without wanting to fly. Real passengers cannot reach the desk.

| Type | Layer | Technique | Notes |
|---|---|---|---|
| Volumetric | 3 and 4 | Huge traffic to fill bandwidth | UDP flood, ICMP flood |
| Amplification and reflection | 3 and 4 | Small request to a third-party server, large reply sent to victim (spoofed source) | DNS amplification, NTP amplification |
| Protocol attack | 3 and 4 | Exhausts state tables | SYN flood, Ping of Death, Smurf |
| Application layer | 7 | Many valid-looking requests | HTTP flood on the flight search API |
| Slow attacks | 7 | Keep connections open slowly | Slowloris |
| Permanent DoS (phlashing) | Hardware | Damages firmware | Rare |

**SYN flood in plain words.** TCP needs a three-way handshake: SYN, SYN-ACK, ACK. Attacker sends many SYNs and never sends ACK. The server keeps half-open entries until the table is full.

**DoS vs DDoS.** DoS has one source. DDoS has many sources, usually a botnet.

**Defense.** Rate limiting, SYN cookies, CDN and scrubbing services, anycast, upstream ISP filtering, autoscaling, WAF, blackhole routing as last resort, DDoS runbook.

```python
from collections import defaultdict, deque

class RateLimiter:
    """Sliding window limiter: max N requests per IP in W seconds."""
    def __init__(self, limit=100, window=10):
        self.limit = limit
        self.window = window
        self.hits = defaultdict(deque)

    def allow(self, ip, now):
        q = self.hits[ip]
        while q and now - q[0] > self.window:
            q.popleft()
        if len(q) >= self.limit:
            return False
        q.append(now)
        return True

rl = RateLimiter(limit=3, window=10)
print([rl.allow("1.2.3.4", t) for t in (1, 2, 3, 4, 12)])
# [True, True, True, False, True]
```

### 3.4 Man-in-the-Middle (MitM) and eavesdropping

**Analogy.** A postman secretly opens, reads, and reseals your letters.

| Variant | Meaning |
|---|---|
| Evil twin | Fake Wi-Fi with a trusted name |
| ARP spoofing | Fake MAC mapping on a LAN so traffic passes through attacker |
| DNS spoofing or cache poisoning | Fake DNS answers send users to a fake site |
| SSL stripping | Downgrades HTTPS to HTTP |
| Session hijacking | Steals session cookie or token |
| Rogue DHCP | Fake DHCP server gives attacker's gateway |
| BGP hijack | Fake routing announcements redirect internet traffic |
| Sniffing | Passive capture of traffic (unencrypted protocols are readable) |

**Defense.** HTTPS everywhere and HSTS, certificate validation, VPN, WPA3, Dynamic ARP Inspection, DHCP snooping, DNSSEC, port security, 802.1X.

### 3.5 Spoofing family

| Spoofing | What is faked |
|---|---|
| IP spoofing | Source IP address |
| MAC spoofing | Hardware address |
| Email spoofing | "From" address |
| Caller ID spoofing | Phone number |
| DNS spoofing | DNS answers |
| GPS spoofing | Position signal. Risk to navigation |
| ADS-B spoofing | Fake aircraft positions in surveillance data |
| Website spoofing | Lookalike site |

### 3.6 Web application attacks

| Attack | Meaning | Plain example | Fix |
|---|---|---|---|
| SQL injection (SQLi) | Attacker's input becomes part of a database command | Search box returns all flights | Parameterized queries |
| Command injection | Input runs as OS command | Ping tool input with an extra shell command attached | Avoid shell, validate input |
| XSS | Script from attacker runs in victim's browser | Comment box with script steals cookie | Output encoding, CSP |
| CSRF | Tricks a logged-in user to submit an unwanted request | Hidden form cancels a booking | Anti-CSRF tokens, SameSite cookies |
| SSRF | Makes server fetch internal URLs | API fetches cloud metadata | Allow-list outbound targets |
| Path traversal | `../` to read other files | Reads server config | Normalize and restrict paths |
| XXE | Malicious XML reads files | XML upload | Disable external entities |
| Insecure deserialization | Crafted object runs code | Session object | Avoid untrusted deserialization |
| File upload attacks | Uploads a script as image | Profile photo upload | Check type, store outside web root |
| Clickjacking | Hidden frame over a button | Fake button | X-Frame-Options, CSP frame-ancestors |
| Open redirect | Trusted site redirects to bad site | Login redirect parameter | Allow-list redirects |
| Broken authentication | Weak login or session handling | Predictable session ID | Strong session management |
| IDOR | Change an ID in the URL to see another record | `/booking/1001` to `/booking/1002` | Check ownership on server |
| API abuse | Missing auth or rate limit | Scraping all fares | Authentication, quotas |

**SQL injection: types.**
* In-band (error-based, union-based): results appear in the same page.
* Blind (boolean-based, time-based): no data shown, attacker infers from true/false or delay.
* Out-of-band: data leaves through another channel such as DNS.

**Runnable demo.**

```python
import sqlite3

db = sqlite3.connect(":memory:")
db.execute("CREATE TABLE flights (id TEXT, route TEXT, crew_note TEXT)")
db.executemany("INSERT INTO flights VALUES (?, ?, ?)", [
    ("AI101", "DEL-BOM", "crew list ready"),
    ("6E202", "MAA-BLR", "diversion plan filed"),
])

def search_unsafe(flight_id):
    query = f"SELECT id, route FROM flights WHERE id = '{flight_id}'"
    return db.execute(query).fetchall()

def search_safe(flight_id):
    return db.execute("SELECT id, route FROM flights WHERE id = ?", (flight_id,)).fetchall()

attack = "x' OR '1'='1"
print(search_unsafe(attack))   # every row leaks
print(search_safe(attack))     # []
```

**Why the unsafe version breaks.** The final SQL becomes `... WHERE id = 'x' OR '1'='1'`. The condition is always true. With a parameter, the database treats the whole string as a plain value.

**XSS types.** Stored (saved on server, hits every visitor), Reflected (comes from the link and reflects back), DOM-based (happens in browser code).

```python
import html

comment = "<script>steal(document.cookie)</script>"
print(html.escape(comment))
# The angle brackets are converted to safe text codes, so the browser shows the text and never runs it
```

### 3.7 Password and credential attacks

| Attack | Meaning |
|---|---|
| Brute force | Tries every combination |
| Dictionary | Tries common words and leaked passwords |
| Hybrid | Dictionary words plus variations (`Airport@2026`) |
| Credential stuffing | Uses leaked pairs from site A on site B |
| Password spraying | One common password across many accounts to avoid lockout |
| Rainbow table | Precomputed hash lookup. Defeated by salting |
| Pass-the-hash | Uses stolen hash directly |
| Kerberoasting | Cracks service ticket passwords in Windows domains |
| Keylogging and shoulder surfing | Captures typed secrets |
| MFA fatigue | Spams approval prompts hoping user accepts |
| SIM swap | Takes over a phone number to receive OTP |

**How big is the search space?**

```python
def crack_time_seconds(charset_size, length, guesses_per_sec):
    return (charset_size ** length) / guesses_per_sec

rate = 1_000_000_000   # one billion guesses per second (fast hash, offline attack)
print(crack_time_seconds(26, 8, rate))   # lowercase only, 8 chars, about 209 seconds
print(crack_time_seconds(62, 8, rate) / 86400)   # letters and digits, about 2.5 days
print(crack_time_seconds(94, 12, rate) / (86400 * 365))   # all printable, 12 chars, about 15 million years
```

Length beats complexity. Use long passphrases and MFA.

**Safe password storage.**

```python
import hashlib
import hmac
import os

def hash_password(password, iterations=200_000):
    salt = os.urandom(16)
    digest = hashlib.pbkdf2_hmac("sha256", password.encode(), salt, iterations)
    return salt, iterations, digest

def verify_password(password, salt, iterations, stored):
    digest = hashlib.pbkdf2_hmac("sha256", password.encode(), salt, iterations)
    return hmac.compare_digest(digest, stored)

salt, it, stored = hash_password("Blue-Plane-Runway-27")
print(verify_password("Blue-Plane-Runway-27", salt, it, stored))   # True
print(verify_password("guess", salt, it, stored))                  # False
```

Use salt (unique random per password), slow hashing (bcrypt, scrypt, Argon2 or PBKDF2), constant time compare, lockout or throttling, and breached password checks.

### 3.8 Wireless attacks

| Attack | Meaning | Defense |
|---|---|---|
| Evil twin and rogue AP | Fake or unauthorized access point | WIPS, WPA2-Enterprise or WPA3, certificate checks |
| Deauthentication | Forces clients off Wi-Fi to capture handshake or cause outage | Protected Management Frames (802.11w) |
| WPS attack | Cracks WPS PIN | Disable WPS |
| Bluejacking and bluesnarfing | Unwanted Bluetooth messages and data theft | Disable Bluetooth when unused |
| Jamming | Radio interference | Spectrum monitoring |
| War driving | Hunting for Wi-Fi networks | Strong encryption |

### 3.9 Advanced and strategic attacks

| Attack | Meaning |
|---|---|
| APT (Advanced Persistent Threat) | Long, stealthy, targeted, well funded. Goal is spying or disruption |
| Zero-day attack | Exploits a flaw before a patch exists |
| Supply chain attack | Compromise a trusted supplier to reach many customers |
| Living off the land | Uses legitimate system tools (PowerShell, WMI) to avoid detection |
| Lateral movement | Moves from one system to another |
| Privilege escalation | Gains higher rights (vertical) or another user's rights (horizontal) |
| Data exfiltration | Sneaks stolen data out |
| Watering hole | Infects a site the target group visits (aviation news portal) |
| Island hopping | Uses a weak partner to reach the real target |
| Cryptographic attacks | Birthday attack on hashes, downgrade attack, replay |
| Side-channel attack | Learns secrets from timing, power, or sound |
| Physical attacks | Skimmers on card readers, rogue devices in network ports |
| Insider threat | Trusted person misuses access |
| AI-driven attacks | Deepfakes, automated phishing, malware generation |
| Quantum threat (future) | Quantum computers could break RSA and ECC. Post-quantum algorithms are being standardized |
| OT and ICS attacks | Manipulate controllers. Stuxnet is the well-known industrial example |

**Replay attack.** Attacker captures a valid message and sends it again later.
Defense: nonces, timestamps, sequence numbers.

```python
import time

seen_nonces = set()

def accept(message_nonce, message_time, max_age=30):
    if message_nonce in seen_nonces:
        return "Rejected: replay"
    if abs(time.time() - message_time) > max_age:
        return "Rejected: too old"
    seen_nonces.add(message_nonce)
    return "Accepted"
```

### 3.10 Attack classification cheat table

| By goal | Attacks |
|---|---|
| Hit confidentiality | Sniffing, MitM, phishing, data breach, spyware, insider theft |
| Hit integrity | SQLi, tampering, MitM modification, malware, DNS spoofing |
| Hit availability | DoS, DDoS, ransomware, wiper, physical damage |
| Hit authentication | Brute force, credential stuffing, spoofing, session hijacking |
| Hit authorization | Privilege escalation, IDOR, broken access control |

| By behavior | Meaning |
|---|---|
| Passive | Only watches (sniffing, traffic analysis). Hard to detect, prevent with encryption |
| Active | Changes or disrupts (injection, DoS, modification). Detect and block |
| Insider | From inside |
| Outsider | From outside |
| Close-in | Physically near |
| Distribution | Tampering in hardware or software before delivery |

---

## 4. Vulnerabilities

### 4.1 Categories

| Category | Examples | Airport example |
|---|---|---|
| Software | Bugs, buffer overflow, unpatched OS, vulnerable libraries | Old Java version in check-in app |
| Configuration | Default passwords, open ports, public storage, debug mode on | Telnet enabled on switch |
| Network | Unencrypted protocols, flat network, weak Wi-Fi | Staff and guest Wi-Fi on one network |
| Hardware and firmware | Outdated firmware, backdoored components | Old firmware in access control panels |
| Cryptographic | Weak algorithm (MD5, DES), short keys, bad key storage | Passwords hashed with plain MD5 |
| Authentication and access | Weak passwords, no MFA, shared accounts, excessive rights | Shared admin account for ATC support |
| Human | Untrained staff, careless sharing | Clicks phishing link |
| Process and policy | No patch policy, no incident plan, weak vendor control | Vendor given permanent access |
| Physical | No door locks, no CCTV, open cabling | Server room door propped open |
| Design flaws | Insecure by design | Safety system with no authentication |
| Legacy and end of life | No vendor patches | Windows 7 on baggage controllers |
| Third party | Vendor software issues | Library flaw inside booking app |

### 4.2 OWASP Top 10 (2021 edition)

OWASP (Open Web Application Security Project) publishes the most critical web application risks. An updated edition may exist, so check owasp.org. The 2021 list is the one commonly asked.

| ID | Name | Plain meaning | Example | Prevention |
|---|---|---|---|---|
| A01 | Broken Access Control | Users do things beyond their permission | Change URL id to see another booking | Deny by default, server-side checks |
| A02 | Cryptographic Failures | Data not protected well | Passport data sent over HTTP | TLS, strong algorithms, key management |
| A03 | Injection | Untrusted input becomes a command | SQLi, XSS, command injection | Parameterized queries, validation, encoding |
| A04 | Insecure Design | Missing security in design | No rate limit on password reset | Threat modeling, secure patterns |
| A05 | Security Misconfiguration | Unsafe settings | Default admin page open | Hardening, automated config checks |
| A06 | Vulnerable and Outdated Components | Old libraries or servers | Known flaw in old framework | Inventory, SCA, patching |
| A07 | Identification and Authentication Failures | Weak login logic | No MFA, weak password rules | MFA, rate limits, strong sessions |
| A08 | Software and Data Integrity Failures | Unverified code and data | Auto-update without signature check | Signed updates, CI/CD security |
| A09 | Security Logging and Monitoring Failures | Attacks go unnoticed | No logs for failed logins | Central logging, alerts |
| A10 | Server-Side Request Forgery (SSRF) | Server tricked into fetching internal resources | Image fetch reads metadata service | Allow-list, network controls |

### 4.3 Standards for naming and scoring vulnerabilities

| Item | Full form | Meaning |
|---|---|---|
| CVE | Common Vulnerabilities and Exposures | Unique ID for a known vulnerability, e.g. CVE-2024-12345 |
| CWE | Common Weakness Enumeration | List of weakness types (for example CWE-89 is SQL injection) |
| NVD | National Vulnerability Database | US database with CVE details and scores |
| CVSS | Common Vulnerability Scoring System | Score from 0.0 to 10.0 |
| CPE | Common Platform Enumeration | Names for products |
| KEV | Known Exploited Vulnerabilities (CISA list) | CVEs being used in real attacks |
| EPSS | Exploit Prediction Scoring System | Probability that a CVE will be exploited |
| CERT-In advisories | Indian alerts | Local vulnerability notes |

**CVSS base metrics (v3.1).**

| Metric | Values | Meaning |
|---|---|---|
| Attack Vector (AV) | Network, Adjacent, Local, Physical | How far away the attacker can be |
| Attack Complexity (AC) | Low, High | How hard |
| Privileges Required (PR) | None, Low, High | Account needed first |
| User Interaction (UI) | None, Required | Need a victim click? |
| Scope (S) | Unchanged, Changed | Does it affect other components? |
| Confidentiality, Integrity, Availability impact | None, Low, High | Effect on CIA |

Severity: 0.0 None, 0.1 to 3.9 Low, 4.0 to 6.9 Medium, 7.0 to 8.9 High, 9.0 to 10.0 Critical. CVSS v4.0 adds more metrics such as Attack Requirements and supplemental metrics. Temporal and environmental metrics adjust the score for the real world.

**A CVSS 9.8 example.** Network attack, low complexity, no privileges, no user interaction, high CIA impact. Remotely exploitable by anyone. Patch first.

**Scores are not the full story.** A medium CVSS on an internet-facing ATC support server can matter more than a critical on an isolated lab PC. This is **risk-based prioritization**.

```python
def priority(cvss, asset_criticality, internet_facing, exploited_in_wild):
    """asset_criticality: 1 to 5. Returns a simple 0 to 100 priority."""
    score = cvss * 6                   # up to 60
    score += asset_criticality * 4     # up to 20
    score += 10 if internet_facing else 0
    score += 10 if exploited_in_wild else 0
    return round(min(score, 100), 1)

findings = [
    ("Check-in server", 9.8, 5, True, True),
    ("Staff wiki", 9.1, 1, False, False),
    ("FIDS web", 6.5, 3, True, True),
]
for name, cvss, crit, net, kev in sorted(findings, key=lambda f: -priority(*f[1:])):
    print(name, priority(cvss, crit, net, kev))
# Check-in server 98.8
# FIDS web 71.0
# Staff wiki 58.6
```

Look at the result. The staff wiki has a higher CVSS (9.1) than the FIDS web server (6.5), yet it ranks lower because it is internal, low value, and not exploited in the wild. Change the weights to match your own policy.

### 4.4 Vulnerability life cycle

1. A flaw is introduced during design or coding.
2. Someone discovers it (researcher, vendor, attacker).
3. Reported to the vendor (**responsible or coordinated disclosure**) or sold or leaked.
4. Vendor issues a patch and advisory, CVE is assigned.
5. Proof-of-concept and exploits appear.
6. Customers patch (the gap is the **window of exposure**).
7. Old systems remain vulnerable for years.

**Zero-day** means the vendor has had zero days to fix. **N-day** means a known and patched flaw that is still exploitable on unpatched systems.

### 4.5 Vulnerability management cycle

| Step | Action |
|---|---|
| 1 Discover | Maintain asset inventory (you cannot patch what you do not know) |
| 2 Scan | Authenticated and unauthenticated scans (tools such as Nessus, OpenVAS, Qualys) |
| 3 Assess | Validate, remove false positives, score |
| 4 Prioritize | Use CVSS, asset value, exposure, exploit status |
| 5 Remediate | Patch, reconfigure, replace, or apply a **compensating control** such as virtual patching with IPS or WAF |
| 6 Verify | Rescan |
| 7 Report | Dashboards and metrics (file 05) |

### 4.6 Scan vs penetration test vs red team

| Activity | Goal | Depth | Who knows |
|---|---|---|---|
| Vulnerability scan | List known weaknesses | Automated, wide | Everyone |
| Penetration test | Prove exploitability and impact | Manual plus tools, scoped | Management and tester |
| Red team | Test detection and response using real attacker TTPs | Long, goal based | Only a few people know |
| Bug bounty | Public researchers report flaws for rewards | Continuous | Open program |
| Audit | Check compliance against standard | Documents and evidence | Planned |

**Pen test types.** Black box (no information), White box (full information, source and diagrams), Grey box (partial).
**Pen test phases.** Planning and scope, reconnaissance, scanning and enumeration, exploitation, post-exploitation, reporting, retest. Always have written authorization and rules of engagement.

### 4.7 Patch management

1. Inventory systems and versions.
2. Subscribe to vendor advisories and CERT-In alerts.
3. Test patches in a staging environment (especially for OT).
4. Deploy in rings (pilot, then wider).
5. Schedule downtime windows for critical airport systems.
6. Have rollback plans.
7. Verify with a scan.
8. If patching is impossible (legacy OT), isolate the system and apply compensating controls.

**Typical remediation timelines (example policy).** Critical within 7 days (or 24 to 72 hours if exploited), High within 30 days, Medium within 60 to 90 days, Low at next cycle. Your organization's policy decides.

### 4.8 Secure development and SBOM

* **SAST** scans source code, **DAST** tests the running app, **IAST** instruments the app, **SCA** scans third-party libraries.
* **SBOM** lists all components in software so you can quickly find affected products when a new flaw appears.
* **Secure SDLC** adds security activities at every phase (requirements, design, code, test, deploy, maintain).
* Principle: **shift left**, find flaws early when they are cheaper to fix.

---

## 5. Putting it together: one story

Scenario: **An airline's mobile app leaks passenger data.**

| Layer | What happened |
|---|---|
| Vulnerability | API endpoint returns booking details for any booking number (IDOR, A01) |
| Threat actor | Opportunistic criminal |
| Threat vector | Public web API |
| Exploit | Script that tries booking numbers in sequence |
| Attack | Data scraping and exfiltration |
| Impact | Confidentiality loss, legal exposure, brand damage |
| Controls that would have helped | Server-side ownership check, rate limiting, API logging and alerting, pen test before launch |
| Response | Block IPs, patch API, notify regulator (CERT-In within 6 hours) and affected passengers, review logs |

---

## 6. Exam Corner

### One-mark facts

* Self-replicating malware over network: **worm**.
* Locks files and demands payment: **ransomware**.
* Looks useful but harmful: **trojan**.
* Network of infected machines: **botnet**.
* Phishing aimed at senior leaders: **whaling**.
* Phishing via SMS: **smishing**. Via voice: **vishing**.
* Following someone through a secure door: **tailgating**.
* Fake Wi-Fi with a real name: **evil twin**.
* Best defense against SQL injection: **parameterized queries**.
* Best defense against XSS: **output encoding and CSP**.
* Many bots flooding a service: **DDoS**.
* One password across many accounts: **password spraying**.
* Reusing leaked passwords elsewhere: **credential stuffing**.
* Attack using an unknown flaw: **zero-day**.
* Long stealthy targeted attack: **APT**.
* Unique ID for a known vulnerability: **CVE**. Severity score: **CVSS** (0 to 10).
* List of top web risks: **OWASP Top 10**.
* Authorized simulated attack to prove exploitability: **penetration test**.

### Common traps

1. Virus vs worm: virus needs a host file and user action, worm spreads alone.
2. DoS vs DDoS: single source vs many.
3. Vulnerability scan does not exploit. Pen test does.
4. High CVSS alone does not set priority.
5. Passive attacks are about listening (hard to detect). Active attacks change things.

### Self-check (answers below)

1. Name the three kinds of SQL injection.
2. What does the "S" in CVSS stand for?
3. A caller says "I am from IT, tell me your OTP." Which attack and which type?
4. Which attack floods half-open TCP connections?
5. What is the difference between horizontal and vertical privilege escalation?
6. Name two defenses against ransomware.
7. What is IDOR?
8. Which pen test type gives the tester full information?

**Answers.** 1 In-band, blind, out-of-band. 2 System (Common Vulnerability Scoring System). 3 Social engineering, specifically vishing (and pretexting). 4 SYN flood. 5 Horizontal gains another user's rights at the same level, vertical gains higher privileges. 6 Offline immutable backups, patching, MFA, EDR, segmentation (any two). 7 Insecure Direct Object Reference, changing an identifier to access another user's data. 8 White box.

---

*End of file 02. Continue with 03-network-security.md*
