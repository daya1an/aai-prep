# 05. Cybersecurity Dashboarding

Subject: Cybersecurity Fundamentals. Part 5 of 5.

Optional install for the chart code: `pip install matplotlib`

---

## 1. What is a Cybersecurity Dashboard

**Analogy.** A car dashboard. You do not read raw engine data. You see speed, fuel, warning lights, and a map, and you act quickly. A security dashboard does the same for an organization's cyber health.

**Definition (syllabus).** Real-time visual interfaces that consolidate critical security metrics, alerts, and trends so that security teams can monitor, analyze, and respond to threats efficiently.

### 1.1 Goals from the syllabus, explained

| Goal | Meaning | Example |
|---|---|---|
| Centralize security posture visibility | One place shows overall security status across firewall, endpoints, cloud, identity, and vulnerabilities | A SOC wall screen shows the status of every office and data centre |
| Rapidly identify and prioritize incidents | Critical items rise to the top, noise is filtered | A ransomware alert on a file server outranks a blocked port scan |
| Monitor compliance and risk metrics | Track against policies and laws, show risk scores | Patch compliance vs target, audit findings open |
| Facilitate informed decision-making with actionable insights | Data leads to a decision | "Phishing clicks are up 12 percent, run training this week" |

### 1.2 Why they matter

* Humans cannot read millions of log lines per day.
* Faster detection and response reduces damage (lower MTTD and MTTR).
* Shows management where to spend money.
* Evidence for auditors and regulators.
* Shared picture during an incident.

### 1.3 Terms

| Term | Meaning |
|---|---|
| Security posture | Overall strength of defenses and readiness |
| KPI | Key Performance Indicator. Measures how well something works |
| KRI | Key Risk Indicator. Early signal that risk is rising |
| Metric | Any measurement |
| Widget or panel | One chart or table on the dashboard |
| Drill-down | Click a number to see underlying details |
| Threshold | Value at which a status changes or an alert fires |
| Baseline | Normal level for comparison |
| RAG | Red, Amber, Green status coding |
| SOC | Security Operations Center |
| SIEM | Security Information and Event Management |
| SOAR | Security Orchestration, Automation and Response |

---

## 2. Where the Data Comes From

| Source | Typical data |
|---|---|
| Firewalls and NGFW | Allowed and blocked traffic, top talkers |
| IDS/IPS and NDR | Attack signatures, anomalies |
| EDR and antivirus | Malware detections, isolated hosts |
| Email security | Phishing blocked, malicious attachments |
| Web proxy and DNS filter | Blocked sites, suspicious domains |
| Identity systems (AD, SSO, MFA) | Logins, failures, privileged use |
| VPN and remote access | Sessions, geolocation |
| Vulnerability scanners | Open vulnerabilities, severity |
| Patch tools | Patch status |
| Cloud audit logs and CSPM | Misconfigurations, API activity |
| Application and database logs | Errors, access |
| Physical access and CCTV analytics | Door events, alarms |
| Ticketing or ITSM | Incident status, SLA |
| Threat intelligence feeds | Known bad IPs, domains, hashes |
| GRC tools | Policy exceptions, audit findings |
| OT monitoring | Industrial controller anomalies |

---

## 3. How a Dashboard Is Built (Data Pipeline)

```
Sources -> Collect -> Normalize -> Enrich -> Store -> Correlate -> Visualize -> Alert -> Act
```

| Stage | What happens | Example |
|---|---|---|
| Collect | Agents, syslog, and APIs gather logs | Firewall sends syslog |
| Parse and normalize | Convert different formats to a common schema (fields like src_ip, user, action, time) | Two vendors' logs become one format |
| Enrich | Add context | An IP gets geolocation, asset owner, threat reputation |
| Store | Index in a SIEM or data lake with retention | 90 days hot, 1 year cold |
| Correlate | Rules link events | Failed logins, then a successful login, then a data export |
| Visualize | Charts, maps, tables | Dashboard |
| Alert and notify | Rules and thresholds | Email, SMS, ticket |
| Automate and respond | SOAR playbooks | Auto-block an IP, disable an account |

**Log formats.** Syslog, CEF (Common Event Format), LEEF, JSON, Windows Event Log. **Time sync** (NTP) is essential. **Windowing** (file 04) is used to compute rolling counts such as failed logins per minute.

**SIEM vs SOAR vs XDR.**

| Tool | Main job |
|---|---|
| SIEM | Collect, correlate, alert, search, dashboards |
| SOAR | Automate response workflows |
| XDR | Unified detection across endpoint, network, email, cloud |
| UEBA | Behavior analytics on users and devices |
| TIP | Threat Intelligence Platform |

---

## 4. Types of Dashboards

| Type | Audience | Time scale | Content |
|---|---|---|---|
| Executive or strategic | CEO, board, directors | Monthly or quarterly | Overall risk score, trend, compliance, major incidents, cost and business impact. Few numbers, plain language |
| Management or tactical | CISO, IT manager | Weekly | KPI trends, SLA, vulnerability backlog, project status |
| Operational (SOC) | Analysts | Real time | Live alerts, open incidents, attack map, queue |
| Analytical or investigative | Threat hunters, responders | On demand | Deep search, timelines, drill-down |
| Compliance | Auditors, compliance officers | Periodic | Control status vs ISO 27001, PCI DSS, DPDP |
| Vulnerability and patch | IT operations | Daily or weekly | Open vulnerabilities, ageing, patch status |
| Identity and access | IAM team | Daily | Privileged accounts, failed logins, dormant accounts |
| Network | Network team | Real time | Traffic, top talkers, blocked attacks |
| Cloud security | Cloud team | Real time | Misconfigurations, public exposure |
| Third-party risk | Procurement, CISO | Quarterly | Vendor ratings and open findings |
| OT security | OT engineers | Real time | Controller anomalies, unauthorized changes |

---

## 5. Components Explained (syllabus items)

### 5.1 Visualizations: charts, heat maps, timelines

| Visual | Best for | Example | Avoid when |
|---|---|---|---|
| Single big number with trend arrow (KPI tile) | Headline status | "Open critical vulnerabilities: 7, down 3" | Many numbers crowd the view |
| Line chart | Change over time | Attack attempts per hour | Few data points |
| Bar or column chart | Compare categories | Incidents by type (phishing, malware, DDoS) | Too many categories (limit to around 10) |
| Stacked bar | Parts of a whole across categories | Alerts per day split by severity | Exact comparison of parts is needed |
| Pie or donut | A few parts of a whole | Share of incident types (up to about 5 slices) | More than 5 slices |
| Heat map | Intensity across two dimensions | Failed logins by hour and weekday | Few cells |
| Geo map | Locations of sources | Origin countries of blocked traffic | Location is not meaningful |
| Timeline | Sequence of events | Incident timeline from first alert to recovery | Many overlapping events |
| Funnel | Stages with drop-off | Alerts, then investigated, then incidents, then confirmed breaches | Non-sequential data |
| Gauge or RAG status | Compare a value to a target | Patch compliance 94 percent vs 95 percent target | Precision is needed |
| Table with sorting | Detailed lists | Top 10 vulnerable assets | Trends |
| Sankey or flow diagram | Flow of traffic between zones | Traffic from the guest VLAN to destinations | Simple data |
| Treemap | Hierarchical proportions | Alerts by system and subsystem | Small data |
| Network graph | Relationships | Hosts talking to a malicious IP | Large graphs without filtering |
| MITRE ATT&CK matrix heat map | Coverage and observed techniques | Which attacker techniques were detected | Non-technical audiences |
| Sparkline | Tiny trend next to a number | Next to a KPI tile | Detailed analysis |

### 5.2 Real-time alerts and notifications

An alert is a message triggered when data meets a rule.

| Element | Meaning |
|---|---|
| Trigger | Rule or threshold, such as 5 failed logins in 60 seconds |
| Severity | Critical, High, Medium, Low, Info |
| Context | Who, what, where, which asset, link to details |
| Deduplication | Merge repeated alerts of the same cause |
| Correlation | Combine alerts into one incident |
| Suppression and tuning | Reduce false positives |
| Escalation | If not acknowledged in time, notify the next person |
| Channels | Dashboard banner, email, SMS, mobile push, phone call, chat, ticket, pager |
| Acknowledge and assign | Someone owns it |
| Runbook link | What to do |
| Automation | SOAR can contain automatically |

**Alert fatigue.** Too many low-value alerts make analysts ignore real ones. Fix by tuning, prioritizing, grouping, and using risk-based scoring.

**Sample escalation matrix**

| Severity | Notify | If no response |
|---|---|---|
| Critical | SOC lead, IR commander, CISO by phone and SMS | After 10 minutes call management |
| High | SOC lead by SMS and email | After 30 minutes escalate |
| Medium | Analyst queue | Next business day |
| Low | Weekly review | None |

### 5.3 Incident tracking and summaries

| Field | Example |
|---|---|
| Incident ID | INC-2026-0142 |
| Title and type | Ransomware attempt on a file server |
| Severity | Critical |
| Status | New, Triage, In progress, Contained, Eradicated, Recovered, Closed |
| Detected time and source | 10:05 by EDR |
| Affected assets | FILE-SRV-04, head office |
| Owner and team | SOC analyst Asha |
| Actions taken | Host isolated, accounts disabled |
| SLA timers | Time to acknowledge, time to contain |
| Root cause | Phishing email |
| Lessons learned | Block macros, add training |

**Incident summary widgets.** Open vs closed counts, by severity, by type, by status, SLA breaches, average age of open incidents, top affected assets, repeat incident count.

### 5.4 Vulnerability and patch status

| Metric | Meaning | Formula or note |
|---|---|---|
| Open vulnerabilities by severity | Critical, High, Medium, Low counts | Using CVSS bands |
| Vulnerability ageing | How long open | Buckets 0 to 7, 8 to 30, 31 to 90, over 90 days |
| Mean time to remediate | Average fix time | Sum of days to fix / number fixed |
| Patch compliance | Systems fully patched | Patched systems / total systems x 100 |
| Scan coverage | Assets scanned | Scanned assets / total assets x 100 |
| Exceptions and risk acceptances | Items knowingly not fixed | Count with expiry |
| Exploited in the wild | Items on known exploited lists | Top priority |
| End-of-life systems | No vendor patches | Count and plan |
| SLA compliance | Fixed within policy time | Fixed in time / total x 100 |

Show the **top 10 riskiest assets**, the **exposure of internet-facing assets**, and the **trend of the backlog** (is it shrinking?).

### 5.5 User activity and access logs

| Widget | What it shows |
|---|---|
| Failed vs successful logins over time | Brute force spikes |
| Logins by country and impossible travel | Account compromise |
| Privileged account usage | Admin activity, outside change windows |
| New or dormant accounts | Orphan accounts to disable |
| MFA coverage and MFA failures | Enrollment gaps, fatigue attacks |
| Data access and exports | Insider threat signs |
| Access to restricted systems | Server room and admin console entries |
| Top users by risk score (UEBA) | Who needs review |
| Account lockouts | Password attacks or user trouble |
| Physical access events | Door forced, tailgating alarms |

---

## 6. KPIs and Formulas

### 6.1 Detection and response

| Metric | Meaning | Formula |
|---|---|---|
| MTTD | Mean Time To Detect | Average of (detection time minus start time) |
| MTTA | Mean Time To Acknowledge | Average of (acknowledge time minus alert time) |
| MTTR | Mean Time To Respond or Recover (define which one in your report) | Average of (resolved time minus detection time) |
| MTTC | Mean Time To Contain | Average of (contained time minus detection time) |
| Dwell time | How long an attacker stayed unnoticed | Detection time minus intrusion time |
| False positive rate | Share of alerts that were not real | FP / total alerts x 100 |
| Alert volume and backlog | Load on the SOC | Count per day and open queue |
| Incident count by severity and type | Threat picture | Count |
| Incident SLA compliance | Handled in time | In time / total x 100 |

### 6.2 Preventive and posture

| Metric | Meaning |
|---|---|
| Patch compliance % | As above |
| Endpoint protection coverage % | Devices with active EDR |
| MFA adoption % | Users with MFA |
| Privileged accounts count | Fewer is better |
| Phishing simulation click rate | Staff who clicked, trend down |
| Phishing report rate | Staff who reported, trend up |
| Security training completion % | Awareness |
| Backup success and restore test success % | Recovery readiness |
| Encryption coverage % | Devices and databases encrypted |
| Third-party risk ratings | Vendor security |
| Audit findings open and overdue | Governance |
| Security risk score | Weighted combination |
| Cost of incidents | Business impact |

### 6.3 Leading vs lagging

* **Lagging** indicators tell what already happened (incident count, breach cost).
* **Leading** indicators warn early (patch backlog growing, phishing click rate rising, privileged accounts increasing).

Good dashboards show both.

### 6.4 SMART metrics

Each metric should be Specific, Measurable, Achievable, Relevant, and Time-bound, and have an **owner** and a **target**.

---

## 7. Design Principles

| Principle | Meaning |
|---|---|
| Know the audience | Executives want risk and trend. Analysts want detail |
| Answer a question | Each panel answers one question ("Are we being attacked right now?") |
| 5-second rule | The main message should be clear within 5 seconds |
| Hierarchy | Most important top-left, big, bold |
| Less is more | Remove decoration. Limit to about 5 to 9 key panels per view |
| Right chart for the data | See the table in 5.1 |
| Consistent colors | Red, Amber, Green for status. Do not use red for decoration. Support color-blind viewers with labels and icons |
| Context | Show targets, baselines, and trend vs last period |
| Drill-down | From summary to detail in a click |
| Real-time where needed | Do not refresh a monthly executive view every second |
| Accurate time range | Show the period clearly and allow filters |
| Accessible and readable | Large fonts on SOC walls, high contrast |
| Actionable | Every alert should link to what to do |
| Role-based access | Dashboards are sensitive. Restrict and log who views them |
| Data quality | Wrong data is worse than none. Check sources and time sync |
| Review and iterate | Remove unused panels |

**Common mistakes**
1. Vanity metrics (number of attacks blocked, with no meaning).
2. Too many charts and colors.
3. No thresholds or targets.
4. Showing only counts and not rates or trends.
5. Stale or incomplete data sources.
6. Alerts without owners.
7. No link between the dashboard and the response process.
8. Using one dashboard for every audience.

---

## 8. Tools (awareness)

| Category | Examples |
|---|---|
| SIEM | Splunk, Microsoft Sentinel, IBM QRadar, Elastic Security, Wazuh, ArcSight |
| Open source stack | Elasticsearch, Logstash, Kibana (ELK), Grafana, Prometheus, Graylog |
| BI tools | Power BI, Tableau, Qlik |
| Vulnerability management | Tenable Nessus, Qualys, Rapid7, OpenVAS |
| EDR and XDR consoles | CrowdStrike, Microsoft Defender, SentinelOne |
| SOAR | Cortex XSOAR, Splunk SOAR |
| Threat intelligence | MISP, OpenCTI |
| Cloud native | AWS Security Hub, Azure Defender, Google Security Command Center |
| ITSM and ticketing | ServiceNow, Jira |

You are not expected to master tools, but you should know what each category does.

---

## 9. Example: SOC Dashboard Layout

```
+--------------------------------------------------------------------------------+
| Overall Risk: AMBER      Open Critical Incidents: 1      MTTD 9 min   MTTR 70 m|
+----------------------+------------------------+--------------------------------+
| Attacks per hour     | Incidents by severity  | Live alert feed                |
| (line chart)         | (stacked bar)          | 10:05 CRIT EDR ransomware FS04 |
|                      |                        | 10:03 HIGH 12 failed logins ADM|
+----------------------+------------------------+--------------------------------+
| Attack origin map    | Failed logins heat map | Vulnerabilities by severity    |
| (geo map)            | (hour x weekday)       | (bar) + ageing (buckets)       |
+----------------------+------------------------+--------------------------------+
| Patch compliance 94% | Endpoint coverage 98%  | Privileged logins today: 14    |
| (gauge, target 95)   | (gauge)                | Off-hours: 2 (table)           |
+----------------------+------------------------+--------------------------------+
| Zone traffic: Guest | DMZ | Corporate | Servers | Restricted | OT (flow chart)  |
+--------------------------------------------------------------------------------+
```

---

## 10. Python: Build the Numbers Behind a Dashboard

### 10.1 Incident KPIs

```python
from datetime import datetime
from collections import Counter

FMT = "%Y-%m-%d %H:%M"

incidents = [
    {"id": "INC-1", "type": "Phishing", "sev": "High",     "start": "2026-10-01 09:00", "detected": "2026-10-01 09:20", "resolved": "2026-10-01 13:20"},
    {"id": "INC-2", "type": "Malware",  "sev": "Critical", "start": "2026-10-01 10:00", "detected": "2026-10-01 10:05", "resolved": "2026-10-01 12:05"},
    {"id": "INC-3", "type": "DDoS",     "sev": "Medium",   "start": "2026-10-01 14:00", "detected": "2026-10-01 14:02", "resolved": "2026-10-01 15:02"},
    {"id": "INC-4", "type": "Insider",  "sev": "Low",      "start": "2026-10-01 08:00", "detected": "2026-10-01 11:00", "resolved": "2026-10-01 12:00"},
]

def minutes(a, b):
    return (datetime.strptime(b, FMT) - datetime.strptime(a, FMT)).total_seconds() / 60

mttd = sum(minutes(i["start"], i["detected"]) for i in incidents) / len(incidents)
mttr = sum(minutes(i["detected"], i["resolved"]) for i in incidents) / len(incidents)

print(f"MTTD = {mttd:.2f} min")    # 51.75 min
print(f"MTTR = {mttr:.2f} min")    # 120.00 min
print(Counter(i["sev"] for i in incidents))
print(Counter(i["type"] for i in incidents))
```

Trace: detection delays are 20, 5, 2, and 180 minutes. The total is 207, divided by 4 gives 51.75. Resolution times after detection are 240, 120, 60, and 60. The total is 480, divided by 4 gives 120.

### 10.2 Patch and vulnerability metrics

```python
from collections import Counter

patched, total = 188, 200
print(f"Patch compliance: {patched / total:.0%}")          # 94%

vulns = [
    {"id": "V1", "cvss": 9.8, "age_days": 3},
    {"id": "V2", "cvss": 7.5, "age_days": 45},
    {"id": "V3", "cvss": 5.3, "age_days": 120},
    {"id": "V4", "cvss": 3.1, "age_days": 10},
]

def band(score):
    if score >= 9.0: return "Critical"
    if score >= 7.0: return "High"
    if score >= 4.0: return "Medium"
    return "Low"

def age_bucket(days):
    if days <= 7: return "0-7"
    if days <= 30: return "8-30"
    if days <= 90: return "31-90"
    return "90+"

print(Counter(band(v["cvss"]) for v in vulns))
print(Counter(age_bucket(v["age_days"]) for v in vulns))
```

### 10.3 Alert rule engine with severity and correlation

```python
from collections import defaultdict, deque

events = [
    {"t": 0,   "user": "ravi", "type": "login_failed", "country": "IN"},
    {"t": 10,  "user": "ravi", "type": "login_failed", "country": "IN"},
    {"t": 20,  "user": "ravi", "type": "login_failed", "country": "IN"},
    {"t": 30,  "user": "ravi", "type": "login_failed", "country": "IN"},
    {"t": 40,  "user": "ravi", "type": "login_ok",     "country": "RU"},
    {"t": 50,  "user": "ravi", "type": "bulk_export",  "country": "RU"},
]

WINDOW = 60
fails = defaultdict(deque)
alerts = []
success_after_fails = set()

for e in events:
    u = e["user"]
    if e["type"] == "login_failed":
        q = fails[u]
        q.append(e["t"])
        while q and e["t"] - q[0] > WINDOW:
            q.popleft()
        if len(q) >= 4:
            alerts.append(("HIGH", e["t"], f"{u}: {len(q)} failed logins in {WINDOW}s"))
    elif e["type"] == "login_ok" and len(fails[u]) >= 4:
        success_after_fails.add(u)
        alerts.append(("CRITICAL", e["t"], f"{u}: success after brute force from {e['country']}"))
    elif e["type"] == "bulk_export" and u in success_after_fails:
        alerts.append(("CRITICAL", e["t"], f"{u}: bulk export after suspicious login"))

order = {"CRITICAL": 0, "HIGH": 1, "MEDIUM": 2, "LOW": 3}
for sev, t, msg in sorted(alerts, key=lambda a: (order[a[0]], a[1])):
    print(sev, t, msg)
```

This is **correlation**: three separate events together form one serious story.

### 10.4 Text dashboard (no extra libraries)

```python
def bar(value, maximum, width=30):
    filled = int(width * value / maximum) if maximum else 0
    return "#" * filled + "." * (width - filled)

def rag(value, target):
    if value >= target:
        return "GREEN"
    if value >= target - 5:
        return "AMBER"
    return "RED"

mttd, mttr = 51.75, 120.0          # from section 10.1
sev_counts = {"Critical": 1, "High": 1, "Medium": 1, "Low": 1}
print("=== SECURITY DASHBOARD ===")
print(f"MTTD {mttd:.1f} min | MTTR {mttr:.1f} min")
print(f"Patch compliance 94% target 95% -> {rag(94, 95)}")
print("Incidents by severity")
for sev, n in sev_counts.items():
    print(f"  {sev:9s} {bar(n, 5)} {n}")
```

### 10.5 Real chart with matplotlib (optional)

```python
import matplotlib.pyplot as plt

hours = list(range(24))
attacks = [12, 9, 7, 6, 5, 8, 14, 22, 30, 35, 40, 38, 36, 33, 31, 29, 28, 26, 24, 20, 18, 16, 14, 13]

fig, ax = plt.subplots(figsize=(8, 3))
ax.plot(hours, attacks, marker="o")
ax.axhline(30, linestyle="--", label="Alert threshold")
ax.set_title("Blocked attack attempts per hour")
ax.set_xlabel("Hour of day")
ax.set_ylabel("Attempts")
ax.legend()
plt.tight_layout()
plt.savefig("attacks_per_hour.png")
```

### 10.6 Risk score for a single asset

```python
def risk_score(vuln_score, threat_level, asset_value, controls_effect):
    """vuln_score, threat_level, asset_value on 1 to 5. controls_effect from 0 to 1."""
    raw = vuln_score * threat_level * asset_value          # up to 125
    return round(raw * (1 - controls_effect), 1)

print(risk_score(4, 4, 5, 0.5))   # 40.0 for a key server with decent controls
print(risk_score(4, 4, 5, 0.1))   # 72.0 when controls are weak
```

---

## 11. Putting It Together: How a Manager Uses the Dashboard

1. **Morning check.** Read the RAG summary. Anything red gets attention first.
2. **Drill into incidents.** Open the critical incident, read the timeline, confirm the owner and SLA.
3. **Review leading indicators.** Patch backlog up, phishing clicks up, a new admin account created.
4. **Decide.** Approve emergency patching, call a staff awareness drive, block a country, add budget for backups.
5. **Report.** Weekly management view and monthly executive view with trends and risk.
6. **Improve.** Remove noisy alerts, add missing data sources, update thresholds after each incident.

---

## 12. Exam Corner

### One-mark facts

* A cybersecurity dashboard is a real-time visual interface that consolidates security metrics, alerts, and trends.
* Purposes: centralize posture visibility, prioritize incidents, monitor compliance and risk, support decisions.
* Components: charts, heat maps, timelines, real-time alerts, incident tracking, vulnerability and patch status, user activity and access logs.
* SIEM collects and correlates logs. SOAR automates response.
* MTTD = Mean Time To Detect. MTTR = Mean Time To Respond or Recover.
* Patch compliance % = patched systems / total systems x 100.
* CVSS bands: Low 0.1 to 3.9, Medium 4.0 to 6.9, High 7.0 to 8.9, Critical 9.0 to 10.0.
* KPI measures performance. KRI signals rising risk.
* RAG = Red, Amber, Green.
* Alert fatigue = analysts overwhelmed by too many low-value alerts.
* Drill-down = moving from summary to detail.
* A heat map shows intensity across two dimensions (hour and weekday).
* Correlation links related events into a single incident.
* Executive dashboards show few high-level numbers. SOC dashboards show real-time detail.
* Leading indicators warn early. Lagging indicators show past results.

### Common traps

1. Confusing SIEM and SOAR.
2. Saying MTTR is always "repair". Define it as respond or recover or resolve in context.
3. Using a pie chart for many categories.
4. Reporting only counts and no trend or target.
5. Using one dashboard for all audiences.
6. Treating more alerts as better security.

### Self-check (answers below)

1. Give two goals of a security dashboard.
2. Which chart suits failed logins by hour and weekday?
3. How do you compute patch compliance?
4. What is alert fatigue and one fix?
5. What does a SOAR tool do?
6. Name two leading indicators.
7. Why must clocks be synchronized across log sources?
8. Which dashboard type suits a board meeting?

**Answers.** 1 Centralize visibility and prioritize incidents (also compliance monitoring and informed decisions). 2 Heat map. 3 Patched systems divided by total systems times 100. 4 Too many low-value alerts cause real ones to be missed. Fix by tuning rules, deduplicating, and risk-based prioritization. 5 Automates and orchestrates response steps (playbooks) such as blocking an IP or opening a ticket. 6 Patch backlog growth and phishing click rate (also count of privileged accounts). 7 So events from different systems can be correlated in the right order. 8 Executive or strategic dashboard.

---

*End of file 05. This completes the Cybersecurity Fundamentals notes.*
