# Cybersecurity Dashboarding — Advanced Exam Notes

**Cybersecurity dashboarding** provides real-time, visual interfaces that consolidate security metrics, alerts, risks, vulnerabilities, compliance status, and incident trends into one operational view. It enables security teams to monitor posture, prioritize incidents, and make faster, evidence-based decisions; effective dashboards use charts, heat maps, timelines, real-time alerts, incident tracking, vulnerability/patch status, and user-activity/access logs. A dashboard is a **decision-support layer**, not a substitute for SIEM analytics, SOAR automation, or incident-response process.

## 1. Purpose of Security Dashboarding

| Objective | Meaning | Airline example |
|---|---|---|
| Centralize visibility | Single view across systems | Booking API, check-in, baggage, cloud, endpoints |
| Detect threats early | Spot anomalies quickly | Sudden spike in failed logins |
| Prioritize incidents | Focus on highest risk first | Critical CVE on internet-facing API |
| Monitor compliance | Track control/regulatory status | PCI DSS, GDPR/DPDP, ISO 27001 |
| Measure risk | Quantify exposure | Open critical vulnerabilities, unpatched servers |
| Support response | Guide actions during incidents | Isolate infected endpoint, revoke token |
| Communicate status | Inform executives/stakeholders | CISO dashboard for leadership |
| Improve accountability | Assign ownership and track SLAs | Patch owner and due date |

### Dashboard audiences

| Audience | Focus | Example metrics |
|---|---|---|
| SOC analyst | Live threats and alerts | Alert queue, severity, affected assets |
| Incident responder | Incident lifecycle | MTTD, MTTR, open incidents |
| Vulnerability management team | Patch/exposure status | Critical CVEs, patch SLA breaches |
| Compliance team | Regulatory/control status | PCI controls, audit findings |
| Cloud/platform team | Infrastructure risk | Public buckets, IAM anomalies |
| CISO/executive | Business risk and trends | Risk score, incident trend, compliance % |
| Audit team | Evidence and traceability | Control evidence, log coverage |

## 2. Dashboard Data Sources

| Source | Data provided | Airline example |
|---|---|---|
| SIEM | Correlated security events | Failed logins, malware, suspicious API activity |
| EDR/XDR | Endpoint/process alerts | Malware detected on agent laptop |
| IDS/IPS/NDR | Network alerts | Port scan, lateral movement |
| WAF/API gateway | Web/API threats | SQLi, XSS, rate-limit breaches |
| IAM/IdP | Authentication and authorization | MFA failures, privilege changes |
| Vulnerability scanner | CVEs and exposure | Critical vulnerability in booking service |
| Patch management | Update status | Missing OS/library patches |
| CSPM | Cloud misconfiguration | Public S3 bucket, open security group |
| CIEM | Cloud identity entitlements | Overprivileged service role |
| CMDB/asset inventory | Asset ownership/context | Booking DB owner and criticality |
| Ticketing/ITSM | Incident/workflow status | Open P1 incident, assignee |
| GRC platform | Compliance/control status | Control test results |
| Threat intelligence | IOC/actor/context | Known malicious IP/domain |
| DLP | Data-loss events | Passenger-manifest upload blocked |
| Cloud logs | Infrastructure activity | VPC flow logs, CloudTrail/Azure Activity Logs |

## 3. Core Dashboard Components

| Component | Purpose | Airline example |
|---|---|---|
| KPI cards | Show high-level status | Critical incidents: 2 |
| Charts | Show trends/distributions | Alerts by severity over 24 hours |
| Heat maps | Highlight concentration/risk | Failed logins by airport/location |
| Timelines | Show event sequence | Attack chain from phishing to exfiltration |
| Tables | Show detailed records | Top 10 vulnerable assets |
| Maps | Geographic context | Attacks by source country/airport |
| Gauges/scores | Show posture/risk level | Security posture score: 82/100 |
| Trend lines | Compare over time | Malware detections week-over-week |
| Filters | Narrow scope | Filter by environment, severity, owner |
| Drill-down | Investigate details | Click alert to view raw logs |
| Alerts/notifications | Trigger action | PagerDuty/Teams/Slack alert |
| Incident panel | Track response status | P1 ransomware incident in progress |
| Compliance panel | Show control status | 94% PCI controls passing |
| Asset panel | Show affected systems | Booking API, payment service, kiosks |

## 4. Key Security Metrics

### Operational metrics

| Metric | Meaning | Airline example |
|---|---|---|
| Total alerts | Number of generated alerts | 1,240 alerts/day |
| Open alerts | Alerts not yet closed | 85 open alerts |
| Critical alerts | High-priority alerts | 3 critical alerts |
| Alert backlog | Unhandled alert volume | 40 alerts older than SLA |
| False-positive rate | Incorrect alerts proportion | 18% false positives |
| True-positive rate | Valid detections proportion | 72% true positives |
| MTTD | Mean time to detect | 12 minutes |
| MTTR | Mean time to respond/resolve | 45 minutes |
| MTTC | Mean time to contain | 20 minutes |
| Dwell time | Attacker presence before detection | 3 days |
| Alert-to-incident ratio | Alerts converted into incidents | 100:1 |
| Escalation rate | Alerts escalated to incidents | 5% |

### Vulnerability and patch metrics

| Metric | Meaning | Airline example |
|---|---|---|
| Open vulnerabilities | Unresolved findings | 320 findings |
| Critical vulnerabilities | High-impact findings | 12 critical CVEs |
| Exploitable vulnerabilities | Known exploit available | 4 exploitable CVEs |
| Internet-exposed vulnerabilities | Publicly reachable risk | 2 exposed API servers |
| Patch compliance | Percentage patched within SLA | 92% |
| Patch SLA breaches | Missed remediation deadlines | 8 assets |
| Mean time to remediate | Average fix time | 6 days |
| Vulnerability aging | How long findings remain open | 30-day-old critical finding |
| Scan coverage | Percentage of assets scanned | 97% |
| SBOM coverage | Known software components | 88% of services |

### Identity and access metrics

| Metric | Meaning | Airline example |
|---|---|---|
| Failed login attempts | Authentication failures | 3,000/day |
| MFA failures | MFA verification failures | 120/day |
| Privileged accounts | High-risk accounts | 45 admin accounts |
| Stale accounts | Inactive accounts | 18 accounts |
| Orphaned accounts | Accounts without owner | 5 accounts |
| Overprivileged identities | Excess permissions | 12 service roles |
| Password/token anomalies | Suspicious credential use | Token replay detected |
| Access-review completion | Recertification progress | 88% complete |
| Break-glass usage | Emergency access events | 2 uses this month |

### Cloud and infrastructure metrics

| Metric | Meaning | Airline example |
|---|---|---|
| Public storage buckets | Exposed cloud storage | 1 public bucket |
| Open security groups | Overly permissive rules | 0.0.0.0/0 on DB port |
| Unencrypted data stores | Missing encryption | 2 databases |
| IAM policy violations | Excess permissions | 9 roles |
| Certificate expiry | Expiring TLS certs | 3 certificates in 14 days |
| Container image vulnerabilities | Image findings | 45 findings |
| Kubernetes policy violations | Misconfigured workloads | Privileged pod detected |
| API endpoints exposed | Public API surface | 48 endpoints |
| Deprecated API usage | Old versions still used | `/v1/flights` traffic |

## 5. Risk Scoring and Prioritization

A dashboard should not show all alerts equally; it should prioritize using **severity, exploitability, exposure, asset criticality, and business impact**.

### Risk-score formula

\[
Risk\ Score = Likelihood \times Impact
\]

More advanced prioritization considers:

```text
Risk = Vulnerability severity
     x Exploit availability
     x Internet exposure
     x Asset criticality
     x Threat intelligence
     x Compensating controls
```

| Factor | High-risk condition | Airline example |
|---|---|---|
| Severity | CVSS 9+ | Critical RCE vulnerability |
| Exploitability | Public exploit exists | Exploit available for API flaw |
| Exposure | Internet-facing | Public booking API |
| Asset criticality | Business-critical | Payment/booking service |
| Data sensitivity | PII/PCI | Passenger and card data |
| Threat intelligence | Active exploitation | Actor targeting aviation |
| Compensating controls | Weak/missing | No WAF or MFA |

### Prioritization matrix

| Severity | Exposure | Asset criticality | Priority |
|---|---|---|---|
| Critical | Internet-facing | High | P1 |
| Critical | Internal | High | P1/P2 |
| High | Internet-facing | Medium | P2 |
| Medium | Internal | Low | P3/P4 |
| Low | Internal | Low | P4 |

**Airline example:** A critical RCE in the internet-facing booking API should outrank a medium vulnerability in an isolated internal reporting server.

## 6. Visualization Types

| Visualization | Best used for | Airline example |
|---|---|---|
| Line chart | Trends over time | Daily alert volume |
| Bar chart | Comparison across categories | Alerts by service |
| Stacked bar chart | Composition over time | Alerts by severity per week |
| Pie/donut chart | Simple proportion | Alert distribution by type |
| Heat map | Density/hotspots | Failed logins by airport and hour |
| Geographic map | Location-based threats | Attack-source countries |
| Timeline | Chronological investigation | Phishing -> login -> API access |
| Scatter plot | Relationship between variables | Vulnerability age vs CVSS score |
| Gauge | Current status/target | Patch compliance 92% |
| Table | Detailed actionable records | Top vulnerable assets |
| Funnel | Stage-wise reduction | Alerts -> triaged -> incidents -> closed |
| Sankey diagram | Flow between categories | Alert sources to incident categories |
| Tree map | Hierarchical proportion | Vulnerabilities by service/team |
| Network graph | Relationships/connections | Compromised host connections |

### Heat-map example

**Failed login attempts by airport and hour:**

| Airport | 00–04 | 04–08 | 08–12 | 12–16 | 16–20 | 20–24 |
|---|---:|---:|---:|---:|---:|---:|
| MAA | 2 | 5 | 12 | 8 | 4 | 18 |
| DEL | 1 | 3 | 9 | 7 | 6 | 11 |
| BLR | 0 | 2 | 6 | 4 | 3 | 7 |

A sudden red zone at MAA during 20:00–24:00 may indicate credential-stuffing or brute-force activity.

### Timeline example

```text
08:02  Phishing email delivered to finance user
08:17  User clicks malicious link
08:19  Credentials entered on fake SSO page
08:21  Attacker logs in from unusual IP
08:23  MFA fatigue prompt approved
08:26  Attacker accesses booking-export API
08:31  Large passenger-data export triggered
08:33  DLP/SIEM alert raised
08:36  Account disabled and session revoked
```

## 7. Real-Time Alerts and Notifications

A dashboard should provide **actionable alerts**, not raw noise.

### Alert attributes

| Attribute | Example |
|---|---|
| Alert ID | `ALRT-88231` |
| Severity | Critical |
| Confidence | High |
| Title | Possible passenger-data exfiltration |
| Description | Bulk export from booking API |
| Asset | Booking service |
| User/identity | `AGT-4412` |
| Source IP | `203.0.113.45` |
| MITRE tactic | Collection / Exfiltration |
| Risk score | 94/100 |
| Recommended action | Disable account, revoke token, isolate host |
| Status | New / Triaged / Investigating / Contained / Closed |
| Assignee | SOC Tier 2 |
| SLA | Respond within 15 minutes |
| Correlation ID | `req-77123` |

### Alert severity model

| Severity | Definition | Response SLA | Airline example |
|---|---|---|---|
| Critical/P1 | Immediate business/security impact | 15 minutes | Ransomware in booking DB |
| High/P2 | Significant risk, needs urgent action | 1 hour | Public bucket with passenger data |
| Medium/P3 | Important but limited impact | 24 hours | Repeated failed logins |
| Low/P4 | Informational/minor issue | 7 days | Policy violation on test system |

### Notification channels

| Channel | Use |
|---|---|
| Email | Non-urgent summaries/reports |
| Slack/Teams | Team collaboration |
| PagerDuty/Opsgenie | On-call escalation |
| SMS | Critical outage/incident |
| Mobile push | On-call analyst alert |
| Ticketing system | Incident workflow/tracking |
| Webhook | Automated SOAR response |

### Alert fatigue

**Alert fatigue** occurs when analysts receive too many low-value alerts, causing important alerts to be missed.

| Cause | Mitigation |
|---|---|
| Duplicate alerts | Deduplicate/correlate events |
| Low-quality detections | Tune rules, suppress known benign patterns |
| Excessive thresholds | Use adaptive baselines |
| Poor context | Enrich alerts with asset/user risk |
| No ownership | Auto-assign by service/team |
| No automation | Use SOAR for repetitive actions |

## 8. Incident Tracking and Summaries

An incident panel should show the full lifecycle and current status.

### Incident record

```json
{
  "incidentId": "INC-2026-118",
  "title": "Suspected passenger-data exfiltration",
  "severity": "P1",
  "status": "CONTAINED",
  "detectedAt": "2026-11-15T08:33:00Z",
  "containedAt": "2026-11-15T08:36:00Z",
  "assignedTo": "SOC-Tier2",
  "affectedSystems": [
    "booking-api",
    "passenger-data-export-service"
  ],
  "affectedData": [
    "Passenger PII",
    "Booking records"
  ],
  "mitreTactics": [
    "Credential Access",
    "Collection",
    "Exfiltration"
  ],
  "riskScore": 94,
  "actions": [
    "Disabled compromised agent account",
    "Revoked API tokens",
    "Blocked source IP",
    "Isolated affected endpoint"
  ],
  "notifications": [
    "CISO",
    "DPO",
    "Legal",
    "Airline Security Operations"
  ]
}
```

### Incident workflow

```text
New alert
   -> Triage
   -> Confirm incident
   -> Assign owner
   -> Contain
   -> Investigate
   -> Eradicate
   -> Recover
   -> Close
   -> Post-incident review
```

### Incident statuses

| Status | Meaning |
|---|---|
| New | Alert received, not reviewed |
| Triaged | Initial assessment complete |
| Investigating | Active analysis |
| Contained | Spread/impact limited |
| Eradicated | Root cause removed |
| Recovered | Services restored |
| Closed | Incident resolved and documented |
| Reopened | New evidence or recurrence |

## 9. Vulnerability and Patch Dashboard

This dashboard answers: **What is vulnerable, how severe is it, who owns it, is it exposed, and when will it be fixed?**

### Asset vulnerability view

| Asset | Criticality | Exposure | CVE | CVSS | Exploit | Patch status | Owner | Due |
|---|---|---|---|---:|---|---|---|---|
| Booking API | Critical | Internet | CVE-2026-1234 | 9.8 | Yes | Missing | Platform team | 24 h |
| Check-in service | High | Internal | CVE-2026-5678 | 7.5 | No | Scheduled | App team | 7 days |
| Airport kiosk | Medium | Airport LAN | CVE-2026-9012 | 6.1 | No | Pending | Airport IT | 30 days |
| Baggage OT server | Critical | Isolated | CVE-2025-3344 | 8.1 | No | Risk accepted | OT team | Exception |

### Patch-status views

| View | Purpose |
|---|---|
| By severity | Focus on critical/high findings |
| By asset criticality | Protect business-critical systems first |
| By owner/team | Drive accountability |
| By environment | Separate prod, staging, dev |
| By SLA breach | Identify overdue remediation |
| By CVE | Track specific widespread vulnerabilities |
| By exploit availability | Prioritize actively exploited issues |
| By compensating controls | Understand residual risk |

### Patch SLA example

| Severity | Internet-facing SLA | Internal SLA |
|---|---|---|
| Critical | 24–72 hours | 7 days |
| High | 7 days | 14–30 days |
| Medium | 30 days | 60–90 days |
| Low | 90 days | Best effort |

## 10. User Activity and Access Logs

User-activity dashboards reveal **who accessed what, when, from where, and whether the action was authorized**.

### Useful log fields

| Field | Example |
|---|---|
| Timestamp | `2026-11-15T08:26:11Z` |
| User ID | `AGT-4412` |
| Role | Check-in agent |
| Source IP | `203.0.113.45` |
| Device posture | Compliant/Non-compliant |
| Location | Chennai Airport |
| Action | `READ`, `EXPORT`, `UPDATE`, `DELETE` |
| Resource | `booking/BK-90812` |
| Result | Allow/Deny |
| MFA status | Passed/Failed |
| Session ID | `sess-8891` |
| Correlation ID | `req-77123` |
| Risk score | 82 |

### User-behavior analytics

**UEBA (User and Entity Behavior Analytics)** detects deviations from normal behavior.

| Baseline behavior | Anomaly | Possible meaning |
|---|---|---|
| Agent accesses 20 bookings/day | Accesses 5,000 bookings | Data exfiltration |
| Login from Chennai | Login from unfamiliar country | Account takeover |
| Working hours 09:00–18:00 | Activity at 03:00 | Compromised account |
| Normal read-only role | Bulk export triggered | Privilege misuse |
| One MFA prompt/day | Repeated MFA prompts | MFA fatigue attack |
| API calls from known gateway | Direct DB access | Policy violation |

### Access-review dashboard

| Identity | Type | Last login | Permissions | Risk | Review status |
|---|---|---|---|---:|---|
| `AGT-4412` | Human | Today | Booking read/export | High | Pending |
| `booking-svc` | Service | Today | Booking DB read/write | Medium | Approved |
| `legacy-admin` | Human | 240 days ago | Full admin | Critical | Revoke |
| `analytics-user` | Human | Today | Pseudonymized data read | Low | Approved |

## 11. Compliance and Risk Dashboard

| Compliance area | Metric | Airline example |
|---|---|---|
| PCI DSS | Encrypted card-data stores | 100% |
| PCI DSS | Access-review completion | 95% |
| GDPR/DPDP | Data-subject requests closed on time | 98% |
| GDPR/DPDP | Retention-policy violations | 0 |
| ISO 27001 | Control implementation | 93% |
| Vulnerability management | Critical patch SLA compliance | 92% |
| Logging | Critical systems sending logs | 99% |
| Backup | Successful backup jobs | 99.5% |
| MFA coverage | Privileged accounts with MFA | 100% |
| Cloud posture | Public storage exposures | 0 |
| Incident management | P1 incidents meeting SLA | 90% |

### Compliance heat map

| Control domain | Booking | Check-in | Baggage | Payment | Cloud |
|---|---:|---:|---:|---:|---:|
| Access control | Green | Green | Amber | Green | Amber |
| Encryption | Green | Green | Red | Green | Green |
| Logging | Green | Amber | Red | Green | Green |
| Patching | Amber | Green | Red | Green | Amber |
| Backup | Green | Green | Amber | Green | Green |

Red cells should link directly to an owner, remediation ticket, risk exception, and due date.

## 12. Dashboard Design Best Practices

| Practice | Reason |
|---|---|
| Start with the decision to be made | Avoid vanity metrics |
| Use role-based views | SOC, executive, compliance needs differ |
| Show trends, not only current values | Reveals deterioration/improvement |
| Include context | Asset criticality, owner, exposure |
| Use consistent severity/color coding | Reduces misinterpretation |
| Avoid chart clutter | Improves usability |
| Enable drill-down | Move from summary to evidence |
| Make every metric actionable | Link to owner/ticket/runbook |
| Refresh at appropriate frequency | Real-time for SOC; daily for executives |
| Show data freshness | Avoid acting on stale data |
| Include exceptions | Risk acceptances and compensating controls |
| Protect dashboard access | Dashboards can reveal sensitive security data |
| Log dashboard usage | Prevent insider misuse |
| Validate data quality | Bad data leads to bad decisions |

### Color conventions

| Color | Meaning | Airline example |
|---|---|---|
| Green | Healthy/compliant | All critical patches within SLA |
| Amber | Warning/watch | Patch SLA nearing breach |
| Red | Critical/action required | Public bucket with PII |
| Grey/Blue | No data/unknown | Asset not reporting logs |

**Important:** Do not use red/green alone; add icons, text, and patterns for accessibility.

## 13. Dashboard Architecture

```text
Data sources
  SIEM, EDR, IDS/IPS, WAF, IAM, Cloud, Vulnerability Scanner,
  Patch Manager, CMDB, Ticketing, GRC, Threat Intel
        |
        v
Collection / normalisation
  Agents, APIs, syslog, Kafka, cloud log pipelines
        |
        v
Enrichment
  Asset criticality, owner, geography, threat intel, business service
        |
        v
Correlation and analytics
  Rules, UEBA, risk scoring, MITRE ATT&CK mapping
        |
        v
Storage
  Data lake / time-series database / SIEM index
        |
        v
Visualization layer
  SOC dashboard, executive dashboard, compliance dashboard
        |
        v
Action layer
  Alerts, tickets, SOAR playbooks, notifications
```

### Dashboard layers

| Layer | Audience | Refresh frequency | Content |
|---|---|---|---|
| Operational SOC | Analysts | Near real-time | Alerts, incidents, active threats |
| Tactical | Security leads | 15–60 minutes | SLA breaches, risk hotspots |
| Vulnerability management | App/infra teams | Hourly/daily | CVEs, patch status |
| Compliance | GRC/audit | Daily/weekly | Controls, evidence, exceptions |
| Executive/CISO | Leadership | Daily/weekly | Risk trend, major incidents, posture |

## 14. SOAR Integration

**SOAR (Security Orchestration, Automation and Response)** turns dashboard insight into automated response.

| Trigger | Automated action | Airline example |
|---|---|---|
| Critical malware alert | Isolate endpoint | Isolate infected agent laptop |
| Impossible travel login | Revoke session, require MFA | Block suspicious passenger/agent session |
| Public bucket detected | Change permissions, notify owner | Secure passenger-data bucket |
| Critical internet-facing CVE | Create P1 ticket, notify owner | Booking API vulnerability |
| Repeated failed logins | Enforce MFA/block IP | Credential-stuffing response |
| DLP exfiltration alert | Disable account, preserve evidence | Passenger-manifest export |
| Certificate expiring | Notify owner/create renewal ticket | API certificate expiry |

**Caution:** Automate only well-understood, reversible, low-false-positive actions. For example, automatically isolating a payment-processing server during peak operations may cause more business damage than the alert warrants.

## 15. Common Dashboard Pitfalls

| Pitfall | Consequence | Fix |
|---|---|---|
| Too many metrics | Analyst overload | Focus on actionable KPIs |
| Vanity metrics | Misleading sense of security | Measure risk reduction/response outcomes |
| No asset context | Cannot prioritize | Integrate CMDB/asset inventory |
| Poor data quality | False confidence | Validate pipelines and coverage |
| Static thresholds | Excessive false positives | Use baselines/adaptive thresholds |
| No ownership | Findings remain open | Assign owner and SLA |
| No drill-down | Slow investigation | Link to raw logs/evidence |
| Dashboard sprawl | Inconsistent truth | Standardize metrics/definitions |
| Ignoring exceptions | Hidden risk | Show risk acceptances/expiry |
| Unsecured dashboards | Sensitive data exposure | RBAC, audit, encryption |

## 16. Rapid Revision: One-Liners

- **Cybersecurity dashboard** = real-time visual interface consolidating security metrics, alerts, and trends.
- Its main purpose is **centralized visibility, prioritization, and faster response**.
- **KPI** = Key Performance Indicator.
- **KRI** = Key Risk Indicator.
- **MTTD** = Mean Time to Detect.
- **MTTR** = Mean Time to Respond/Resolve.
- **MTTC** = Mean Time to Contain.
- **Dwell time** = time an attacker remains undetected.
- **Blast radius** = scope of impact from an incident.
- **Heat map** highlights concentration of risk/events.
- **Timeline** shows chronological attack/response sequence.
- **Drill-down** moves from summary metric to detailed evidence.
- **SIEM** aggregates and correlates security events.
- **SOAR** automates security-response workflows.
- **UEBA** detects abnormal user/entity behavior.
- **CSPM** detects cloud-security misconfigurations.
- **CIEM** manages cloud identity entitlements.
- **CMDB** provides asset and ownership context.
- **GRC** = Governance, Risk, and Compliance.
- **Vulnerability aging** measures how long a finding remains open.
- **Patch compliance** measures remediation within SLA.
- **Exploit availability** increases prioritization.
- **Internet exposure** increases risk priority.
- **Asset criticality** determines business impact.
- **Alert fatigue** occurs when analysts receive too many low-value alerts.
- **False-positive rate** measures incorrect alerts.
- **Correlation ID** links related events across systems.
- **Data freshness** indicates how current dashboard data is.
- **Role-based dashboards** serve SOC, compliance, cloud, and executive audiences differently.
- **Actionable insight** means the dashboard indicates what to do, who owns it, and what the next step is.

## 17. Probable One-Mark Questions

1. **What is cybersecurity dashboarding?**  
   Real-time visual interfaces consolidating security metrics, alerts, and trends.

2. **What is the main purpose of a security dashboard?**  
   Centralize visibility and enable efficient monitoring, analysis, and response.

3. **Which visualization shows risk concentration across categories?**  
   Heat map.

4. **Which visualization shows chronological security events?**  
   Timeline.

5. **Which metric measures average time to detect an incident?**  
   MTTD.

6. **Which metric measures average time to resolve/respond?**  
   MTTR.

7. **Which metric measures average time to contain an incident?**  
   MTTC.

8. **What is alert fatigue?**  
   Reduced analyst effectiveness due to excessive low-value alerts.

9. **Which system aggregates and correlates security logs?**  
   SIEM.

10. **Which system automates security-response actions?**  
   SOAR.

11. **Which dashboard component tracks the lifecycle of a security event?**  
   Incident tracking panel.

12. **Which dashboard component shows vulnerability and remediation status?**  
   Vulnerability and patch-status panel.

13. **Which dashboard component shows who accessed what resource and when?**  
   User activity and access-log panel.

14. **Which metric measures the percentage of systems patched within the required time?**  
   Patch compliance.

15. **Which metric measures how long a vulnerability has remained open?**  
   Vulnerability aging.

16. **Which factor increases the priority of a vulnerability found on a public booking API?**  
   Internet exposure.

17. **Which factor determines the business importance of an affected system?**  
   Asset criticality.

18. **Which behavior-analytics system detects abnormal user activity?**  
   UEBA.

19. **Which cloud dashboard control detects public storage buckets and open security groups?**  
   CSPM.

20. **Which dashboard element allows an analyst to move from a summary chart to detailed logs?**  
   Drill-down.

21. **Which metric measures incorrect alerts?**  
   False-positive rate.

22. **Which metric measures the time an attacker remains undetected?**  
   Dwell time.

23. **Which dashboard is intended for senior leadership?**  
   Executive/CISO dashboard.

24. **Which dashboard element should link a critical finding to its owner and remediation ticket?**  
   Actionable insight/workflow integration.

25. **Which security framework maps attacker objectives and techniques for dashboard correlation?**  
   MITRE ATT&CK.
