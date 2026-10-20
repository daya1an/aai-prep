# Common Elements in Data Protection Frameworks — Advanced Notes

A data-protection framework combines **classification, access control, encryption, minimization, monitoring, incident response, and data-subject rights** to protect personal and business data throughout its lifecycle. In an airline context, it protects passenger identities, travel history, payment details, loyalty data, crew records, and operational data while still enabling analytics and real-time processing. Data minimization limits collection and retention to what is necessary; windowing is a stream-processing technique that divides continuous data into manageable subsets for analysis. [nist](https://www.nist.gov/blogs/taking-measure/identify-protect-detect-respond-and-recover-nist-cybersecurity-framework)

## 1. Data Lifecycle

Data protection applies across the full lifecycle:

```text
Collect -> Classify -> Store -> Process -> Share
        -> Retain -> Archive -> Delete/Destroy
```

| Stage | Airline example | Protection control |
|---|---|---|
| Collection | Passenger name, passport, contact, payment details | Consent, purpose limitation, secure forms |
| Classification | Mark data as Public/Internal/Confidential/Restricted | Labels and metadata |
| Storage | Booking database, data lake, backups | Encryption, access control |
| Processing | Fare analytics, fraud detection | Pseudonymization, role-based access |
| Sharing | Travel agency, airport, payment gateway | DPA, tokenization, secure API |
| Retention | Booking/tax records | Retention schedule |
| Archival | Old flight records | Encrypted cold storage |
| Deletion | Expired passenger data | Secure deletion/erasure |

## 2. Data Classification

**Data classification** organizes data according to sensitivity, criticality, regulatory requirements, and business impact, then applies protection controls appropriate to each class.

### Typical classification levels

| Level | Description | Airline examples | Controls |
|---|---|---|---|
| Public | No harm if disclosed | Airport map, flight timetable | Integrity/availability controls |
| Internal | Limited internal use | Operational reports, internal policies | Authentication, access control |
| Confidential | Business-sensitive | Fare strategy, contracts, route plans | Encryption, restricted access, DLP |
| Restricted / Highly confidential | Legal, financial, personal, safety-critical | Passport, payment card, medical assistance, security data | Strong encryption, MFA, audit, DLP, minimal access |
| Personal data / PII | Identifies a person | Name, email, phone, passport, travel history | Privacy controls, consent, retention limits |
| Sensitive personal data | Higher-risk personal data | Health, biometric, religion, caste, financial data | Stronger legal/technical safeguards |
| PCI data | Cardholder data | Card number, CVV, expiry | PCI DSS controls, tokenization |
| PHI | Health information | Medical assistance request | HIPAA-like safeguards where applicable |

### Classification dimensions

| Dimension | Question | Example |
|---|---|---|
| Sensitivity | How harmful is disclosure? | Passport number is highly sensitive |
| Criticality | How important is availability? | Departure-control system is critical |
| Regulatory scope | Which laws apply? | GDPR/DPDP for personal data; PCI DSS for cards |
| Retention | How long can it be kept? | Booking records retained per tax/legal rules |
| Sharing | Who may receive it? | Airport, payment provider, travel partner |
| Purpose | Why is it processed? | Ticketing, fraud prevention, loyalty |

### Labeling and handling

| Label | Handling rule |
|---|---|
| Public | May be published; protect integrity |
| Internal | Only employees/authorized contractors |
| Confidential | Need-to-know, encrypted, no personal email |
| Restricted | MFA, PAM, DLP, audit, explicit approval |

**Airline example:** A passenger manifest is **Restricted**; an airport departure board is **Public**; internal revenue dashboards are **Internal/Confidential** depending on content.

## 3. Access Controls

**Access control** ensures that only authorized identities can perform authorized actions on authorized resources.

### Core principles

| Principle | Meaning | Airline example |
|---|---|---|
| Least privilege | Minimum access required | Baggage agent cannot access payroll |
| Need to know | Access only necessary data | Agent sees relevant flight data only |
| Separation of duties | Split critical tasks | Refund requester differs from approver |
| Deny by default | No access unless explicitly granted | New service cannot access DB by default |
| Defense in depth | Multiple controls | MFA + RBAC + network segmentation |
| Zero Trust | Verify every request | API validates token and policy each time |
| Accountability | Trace actions to identity | Audit logs identify agent |
| Time-bound access | Access expires | Temporary production access for DBA |

### Access-control models

| Model | Basis | Airline example |
|---|---|---|
| DAC | Data owner grants access | Manager shares a report |
| MAC | Central policy/security labels | Restricted government/aviation system |
| RBAC | Roles | Agent, supervisor, finance, admin |
| ABAC | Attributes and conditions | Agent can access only assigned airport/region |
| ReBAC | Relationships | Passenger can access own booking |
| PBAC | Policies | Refund allowed only before departure and for refundable fare |
| Rule-based access | Explicit rules | IP allowlist for airport ops system |

### RBAC example

| Role | Permissions |
|---|---|
| Passenger | View/cancel own booking, download boarding pass |
| Check-in agent | Check in passenger, assign seat, print boarding pass |
| Baggage agent | Update baggage status |
| Finance user | View refunds, approve refunds |
| Security admin | Manage roles, view audit logs |
| Data analyst | Access pseudonymized analytics data only |

### ABAC policy example

```text
ALLOW read_booking IF
  user.role == "CHECKIN_AGENT"
  AND booking.departure_airport == user.assigned_airport
  AND flight.departure_time BETWEEN now() AND now() + 48h
  AND device.posture == "COMPLIANT"
```

### Authentication and authorization

| Control | Purpose |
|---|---|
| MFA | Requires multiple factors |
| SSO | One identity for multiple systems |
| OAuth 2.0 | Delegated authorization |
| OpenID Connect | Authentication/identity layer |
| SAML | Enterprise federated SSO |
| JWT | Signed token carrying claims |
| PAM | Manages privileged accounts |
| JIT access | Temporary elevated access |
| Service accounts | Non-human identities |
| Access review | Periodic recertification |

## 4. Encryption: At Rest and In Transit

**Encryption** protects confidentiality by converting readable data into ciphertext using cryptographic keys.

| State | Meaning | Airline example | Controls |
|---|---|---|---|
| Data at rest | Stored data | Passenger DB, backups, S3 objects | AES-256, KMS/CMK, key rotation |
| Data in transit | Moving data | App to API, service to service | TLS 1.2+/1.3, mTLS |
| Data in use | Being processed in memory | Fraud-scoring service | Confidential computing, memory protection |
| End-to-end encryption | Only endpoints can decrypt | Secure passenger messaging | Strong key management |

### Encryption controls

| Control | Purpose |
|---|---|
| Symmetric encryption | Fast bulk encryption; AES-256 |
| Asymmetric encryption | Key exchange/signatures; RSA/ECC |
| Hashing | Integrity/fingerprinting; SHA-256 |
| Password hashing | Protect credentials; Argon2/bcrypt/scrypt/PBKDF2 |
| Digital signature | Integrity, authenticity, non-repudiation |
| Tokenization | Replaces sensitive value with non-sensitive token |
| Masking | Hides part of value; `****1234` |
| Pseudonymization | Replaces identifiers with pseudonyms |
| Anonymization | Irreversibly removes identifiability |
| KMS/HSM | Key generation, storage, rotation, use control |
| Envelope encryption | Data key encrypts data; master key encrypts data key |
| Key rotation | Limits exposure if a key is compromised |

### Airline encryption example

```text
Passenger enters card details
        |
        v
TLS protects data in transit
        |
        v
Payment gateway tokenizes card
        |
        v
Token stored in booking DB
        |
        v
Card data encrypted at rest in PCI-scoped vault
```

**Important:** Encryption protects confidentiality, but not availability; ransomware can still encrypt or delete data unless backups and access controls exist.

## 5. Data Minimization

**Data minimization** means collecting, processing, retaining, and sharing only the minimum data necessary for a specific, legitimate purpose.

| Principle | Question | Airline application |
|---|---|---|
| Purpose limitation | Why is data collected? | Collect passport only where legally required |
| Data minimization | Is every field necessary? | Do not store full DOB if age band suffices |
| Storage limitation | How long is it kept? | Delete expired marketing profiles |
| Accuracy | Is data correct/current? | Correct passenger contact details |
| Transparency | Is the person informed? | Privacy notice explains processing |
| Lawfulness | Is there a legal basis? | Contract, consent, legal obligation |
| Fairness | Is processing fair? | No discriminatory profiling |
| Accountability | Can compliance be demonstrated? | Records of processing activities |

### Techniques

| Technique | Description | Airline example |
|---|---|---|
| Field minimization | Collect fewer fields | Ask for PIN code, not full address |
| Purpose-based collection | Collect only for stated purpose | Fraud team gets limited risk fields |
| Retention scheduling | Delete after defined period | Delete abandoned cart data after 30 days |
| Data masking | Hide sensitive values | Mask card number in support console |
| Tokenization | Replace sensitive value | Replace card number with token |
| Pseudonymization | Replace direct identifiers | Replace passenger ID with hashed ID |
| Aggregation | Report groups, not individuals | Monthly revenue by route |
| Differential privacy | Add calibrated noise to aggregate results | Public travel-pattern statistics |
| Synthetic data | Generate artificial data | ML training data without real passenger records |
| Data windows | Process only relevant time slices | Analyze last 24 hours of booking events |

## 6. Windowing in Data Protection and Stream Processing

**Windowing** divides a continuous or large dataset into smaller, manageable chunks called **windows**, allowing processing or analysis over defined time intervals or record counts.

**Airline use cases:**
- Detect abnormal booking velocity in the last 5 minutes.
- Calculate average check-in time every 15 minutes.
- Track baggage scans during a flight’s departure window.
- Identify repeated failed login attempts in a 10-minute session.
- Analyze flight-delay events in rolling 1-hour windows.

### Types of windows

| Window type | Definition | Airline example |
|---|---|---|
| Tumbling window | Fixed, non-overlapping intervals | Count bookings from 10:00–10:15, then 10:15–10:30 |
| Sliding window | Overlapping intervals moving forward incrementally | Detect fraud in every rolling 10-minute period |
| Session window | Activity periods separated by inactivity timeout | Group a passenger’s search, select, and payment actions |
| Count-based window | Fixed number of events | Analyze every 100 baggage-scan events |
| Global window | All data in one window | Entire day’s audit report |
| Hopping window | Sliding window with fixed hop/slide interval | Every 5-minute hop over a 30-minute window |

### Tumbling window

Fixed-size, **non-overlapping** windows.

```text
10:00–10:15  [event, event, event]
10:15–10:30  [event, event]
10:30–10:45  [event]
```

**Example:** Count bookings per 15-minute interval.

```python
(
    bookings
    .groupByWindow("15 minutes")
    .count()
)
```

### Sliding window

Windows **overlap** and move forward by a slide interval.

```text
Window 1: 10:00–10:10
Window 2: 10:05–10:15
Window 3: 10:10–10:20
```

**Example:** Detect more than 20 failed logins in any rolling 10-minute period.

```python
(
    failed_logins
    .groupByWindow("10 minutes", "1 minute")
    .count()
    .filter("count > 20")
)
```

### Session window

Groups events with **inactivity gaps**. A new session begins after a defined inactivity timeout.

```text
Events: 10:00, 10:02, 10:04, 10:20, 10:22
Inactivity gap: 10 minutes

Session 1: 10:00–10:04
Session 2: 10:20–10:22
```

**Airline example:** A passenger searches flights, views fare rules, selects a seat, and pays. If there is no activity for 30 minutes, the session ends.

### Count-based window

Groups a fixed number of events rather than time.

```text
Events 1–100   -> Window 1
Events 101–200 -> Window 2
```

**Example:** Analyze every 100 baggage-scan events for abnormal routing.

### Windowing terms

| Term | Meaning |
|---|---|
| Window size | Length/duration of each window |
| Slide interval | How often a sliding window starts |
| Event time | Time when event actually occurred |
| Processing time | Time when system processes event |
| Watermark | Progress marker for event-time completeness |
| Late data | Event arrives after its window has closed |
| Allowed lateness | Period during which late data is still accepted |
| Trigger | Condition that emits window result |
| State store | Stores intermediate window state |

### Windowing and privacy

Windowing can support data minimization by processing only relevant slices, but it does **not** by itself anonymize data.

| Practice | Purpose |
|---|---|
| Retain only aggregate window output | Avoid storing raw PII |
| Use pseudonymous IDs in windows | Reduce direct identification |
| Apply short retention to raw streams | Limit exposure |
| Encrypt window state stores | Protect intermediate data |
| Restrict access to windowed analytics | Prevent misuse |
| Use k-anonymity/differential privacy for published aggregates | Prevent re-identification |

## 7. Audit and Monitoring

**Audit and monitoring** continuously track data access, changes, exports, administrative actions, and security events to detect misuse and support investigations.

### What to audit

| Event type | Airline example |
|---|---|
| Authentication events | Failed logins, MFA failures |
| Authorization failures | Agent tries to access another passenger’s booking |
| Data reads | Bulk export of passenger records |
| Data changes | Fare, booking, or refund modification |
| Administrative changes | New admin role granted |
| Configuration changes | Firewall or IAM policy change |
| Data exports | Passenger manifest download |
| API usage | Unusual flight-search volume |
| Encryption/key operations | KMS key use or rotation |
| Deletion events | Passenger-data erasure request |

### Monitoring signals

| Signal | Possible issue |
|---|---|
| Access outside working hours | Compromised account |
| Large bulk export | Data exfiltration |
| Repeated authorization failures | Privilege escalation attempt |
| Unusual source IP/country | Account takeover |
| Sudden API traffic spike | Scraping or DoS |
| Many deleted records | Insider misuse/ransomware |
| Service account accessing human data | Broken access control |
| Key accessed by unusual service | Credential misuse |

### Audit-log requirements

| Requirement | Description |
|---|---|
| Immutability | Logs cannot be silently altered |
| Centralization | Logs sent to SIEM |
| Time synchronization | Use NTP for accurate timelines |
| Retention | Retain per legal/policy requirement |
| Integrity | Protect against tampering |
| Least-privilege access | Only authorized security/audit roles |
| Correlation IDs | Link events across services |
| Alerting | Generate actionable alerts |
| Privacy | Do not log passwords, card numbers, tokens |

### Example audit event

```json
{
  "eventId": "audit-99182",
  "timestamp": "2026-11-15T08:12:33Z",
  "actor": {
    "type": "USER",
    "id": "AGT-4412",
    "roles": ["CHECKIN_AGENT"]
  },
  "action": "READ",
  "resource": "booking/BK-90812",
  "result": "DENIED",
  "reason": "OBJECT_LEVEL_AUTHORIZATION_FAILED",
  "sourceIp": "10.20.4.18",
  "correlationId": "req-77123"
}
```

## 8. Incident Response

**Incident response** is the structured process for detecting, containing, eradicating, recovering from, and learning from security incidents such as breaches, leaks, ransomware, and insider misuse.

### Incident-response phases

| Phase | Objective | Airline data-breach example |
|---|---|---|
| Preparation | Playbooks, roles, backups, training | Tested breach-response plan |
| Identification/Detection | Confirm and scope incident | SIEM detects bulk passenger-data export |
| Containment | Stop spread and limit damage | Revoke API keys, isolate system |
| Eradication | Remove root cause | Patch vulnerability, remove malware |
| Recovery | Restore safe operations | Restore from clean backup, monitor |
| Post-incident | Learn and improve | RCA, control improvements |

### Breach-response actions

1. Activate incident-response team.
2. Preserve logs and evidence.
3. Determine affected data, people, systems, and time period.
4. Contain compromised accounts, endpoints, API keys, and sessions.
5. Rotate secrets, certificates, and credentials.
6. Notify legal, privacy, security, communications, and leadership teams.
7. Assess regulatory notification obligations.
8. Notify affected individuals/regulators where required.
9. Restore services from verified clean backups.
10. Conduct root-cause analysis and update controls.

### Key incident metrics

| Metric | Meaning |
|---|---|
| MTTD | Mean Time to Detect |
| MTTR | Mean Time to Respond/Resolve |
| MTTC | Mean Time to Contain |
| RPO | Acceptable data loss |
| RTO | Acceptable downtime |
| Blast radius | Scope of affected systems/data |
| Dwell time | Time attacker remained undetected |

## 9. Data Subject Rights

**Data subject rights** give individuals control over their personal data under privacy laws such as GDPR and India’s DPDP Act.

| Right | Meaning | Airline example |
|---|---|---|
| Right to be informed | Know how data is used | Privacy notice at booking |
| Right of access | Obtain copy/details of personal data | Passenger requests booking history |
| Right to rectification | Correct inaccurate data | Correct misspelled name |
| Right to erasure | Request deletion, subject to legal exceptions | Delete marketing profile |
| Right to restrict processing | Limit certain processing | Pause marketing profiling |
| Right to data portability | Receive/export data in usable format | Export booking/loyalty data |
| Right to object | Object to certain processing | Opt out of targeted marketing |
| Rights related to automated decisions | Human review for significant automated decisions | Challenge automated fraud rejection |
| Withdraw consent | Withdraw consent for consent-based processing | Stop promotional messages |

### Rights-request workflow

```text
Passenger submits request
        |
        v
Identity verification
        |
        v
Classify request: access / correction / deletion / portability
        |
        v
Search systems: booking, CRM, loyalty, support, analytics
        |
        v
Apply legal exceptions and retention rules
        |
        v
Fulfill, partially fulfill, or reject with reason
        |
        v
Record evidence and response deadline
```

**Important:** The right to erasure is not absolute. Airlines may retain data required for taxation, legal claims, safety, fraud prevention, or regulatory obligations.

## 10. Data-Protection Practices and Policies

| Practice | Purpose | Airline example |
|---|---|---|
| Privacy by design | Build privacy into systems | Minimize passenger fields at design stage |
| Privacy by default | Use protective defaults | Marketing opt-in rather than opt-out |
| Data-protection impact assessment | Assess high-risk processing | New facial-recognition boarding system |
| Records of processing activities | Document processing | Maintain ROPA |
| Data-processing agreement | Govern processor obligations | Contract with cloud/vendor |
| Retention schedule | Define deletion timelines | Delete abandoned bookings after 90 days |
| Consent management | Record and honor consent | Marketing-consent preference center |
| Vendor risk management | Assess third parties | Assess baggage-system vendor |
| Secure SDLC | Build securely | Threat-model booking API |
| DLP | Prevent unauthorized exports | Block manifest upload to personal cloud |
| Backup and recovery | Ensure availability/resilience | Encrypted, tested backups |
| Security awareness | Reduce human risk | Phishing simulation for staff |
| Data-loss response plan | Prepare for breach | Breach-notification playbook |
| Data ethics | Fair, responsible use | Avoid discriminatory pricing/profiling |

## 11. Privacy-Enhancing Technologies

| Technology | Purpose | Airline example |
|---|---|---|
| Masking | Hide part of sensitive value | `Ravi*****` in support console |
| Tokenization | Replace sensitive value with token | Card token replaces PAN |
| Pseudonymization | Replace identifiers with pseudonyms | `PAX-77341` instead of name |
| Anonymization | Remove identifiability | Aggregate route statistics |
| Encryption | Protect confidentiality | Encrypted passenger database |
| Differential privacy | Add noise to aggregate statistics | Public travel-demand report |
| Federated learning | Train without centralizing raw data | Fraud model trained across airlines |
| Secure multi-party computation | Compute jointly without revealing inputs | Joint fraud intelligence |
| Homomorphic encryption | Compute on encrypted data | Encrypted risk scoring |
| Synthetic data | Generate artificial data | ML training datasets |
| K-anonymity | Ensure individuals are indistinguishable in a group | Published travel-pattern dataset |

## 12. Rapid Revision: One-Liners

- **Data classification** organizes data by sensitivity and applies suitable controls.
- **Access control** prevents unauthorized use of data.
- **RBAC** grants permissions through roles.
- **ABAC** grants access using attributes/conditions.
- **Least privilege** grants only required access.
- **Encryption at rest** protects stored data.
- **Encryption in transit** protects moving data.
- **TLS/HTTPS** protects data during transmission.
- **Tokenization** replaces sensitive data with a non-sensitive token.
- **Masking** hides part of a sensitive value.
- **Pseudonymization** replaces identifiers but may allow re-identification with additional information.
- **Anonymization** aims to remove the ability to identify a person.
- **Data minimization** collects and retains only necessary data.
- **Purpose limitation** restricts use to the stated purpose.
- **Storage limitation** restricts retention duration.
- **Windowing** divides continuous data into manageable subsets.
- **Tumbling window** = fixed, non-overlapping intervals.
- **Sliding window** = overlapping intervals.
- **Session window** = activity separated by inactivity.
- **Count-based window** = fixed number of events.
- **Audit and monitoring** detect anomalous access and misuse.
- **Immutable audit logs** support investigation and non-repudiation.
- **Incident response** handles breaches and leaks systematically.
- **RPO** = acceptable data loss; **RTO** = acceptable recovery time.
- **Data subject rights** include access, correction, deletion, portability, objection, and restriction.
- **Right to erasure** is not absolute; legal retention obligations may apply.
- **Privacy by design** embeds privacy from the beginning.
- **DPIA** assesses privacy risk in high-risk processing.
- **DLP** prevents unauthorized data movement.
- **KMS/HSM** manage encryption keys securely.

## 13. Probable One-Mark Questions

1. **What is data classification?**  
   Organizing data by sensitivity and applying appropriate protection.

2. **Which access model assigns permissions using roles?**  
   RBAC.

3. **Which access model uses attributes such as location, device, and department?**  
   ABAC.

4. **Which principle grants users only the minimum required access?**  
   Least privilege.

5. **Which principle separates critical tasks among different people?**  
   Separation of duties.

6. **What is encryption at rest?**  
   Protecting stored data.

7. **What is encryption in transit?**  
   Protecting data while it moves across networks.

8. **Which protocol commonly protects data in transit for web APIs?**  
   TLS/HTTPS.

9. **What is data minimization?**  
   Collecting and retaining only necessary data.

10. **What is windowing?**  
   Dividing continuous/large data into smaller manageable chunks for processing.

11. **Which window type uses fixed, non-overlapping intervals?**  
   Tumbling window.

12. **Which window type uses overlapping intervals?**  
   Sliding window.

13. **Which window type is based on activity separated by inactivity?**  
   Session window.

14. **Which window type uses a fixed number of records?**  
   Count-based window.

15. **What is a watermark in stream processing?**  
   A progress indicator used to handle event-time completeness/late data.

16. **What is audit monitoring used for?**  
   Tracking access and detecting anomalies or misuse.

17. **What is the first phase of incident response?**  
   Preparation.

18. **What does RPO mean?**  
   Recovery Point Objective — maximum acceptable data loss.

19. **What does RTO mean?**  
   Recovery Time Objective — maximum acceptable downtime.

20. **Which right allows a person to obtain their personal data?**  
   Right of access.

21. **Which right allows correction of inaccurate personal data?**  
   Right to rectification.

22. **Which right allows deletion of personal data, subject to legal exceptions?**  
   Right to erasure.

23. **Which right allows receiving personal data in a portable format?**  
   Right to data portability.

24. **Which practice builds privacy into systems from the design stage?**  
   Privacy by design.

25. **Which technology replaces a card number with a non-sensitive surrogate value?**  
   Tokenization.
