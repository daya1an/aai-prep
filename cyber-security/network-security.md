**Yes — the previous notes cover every item in that Network Security sub-topic**, but the coverage was distributed across sections rather than presented as one dedicated “Network Security” block. Here is a focused, exam-ready supplement that maps and strengthens exactly those points, with airport/airline examples.

## Coverage Check

| Required topic | Covered earlier in | Strengthened below |
|---|---|---|
| Wi-Fi, LAN, VLAN, corporate LAN, cloud transmission | Network Security, Cloud Security | Wi-Fi/LAN/VLAN details |
| Confidentiality, integrity, availability in transit | CIA Triad, Cryptography | Explicit mapping |
| Firewalls | Network Security | Firewall types/rules |
| VPN | Network Security | VPN types, split tunneling |
| IDS/IPS | Network Security | IDS vs IPS, placement |
| Encryption / HTTPS / TLS | Cryptography, TLS | Handshake, versions, mTLS |
| Network segmentation | Zero Trust, Cloud Security | VLAN, DMZ, microsegmentation |
| Endpoint security | Network Security | EDR, MDM, hardening |
| Cloud security | Cloud and Container Security | Shared responsibility, controls |

Network segmentation groups resources with similar security requirements and enforces firewall rules between segments; VLAN IDs are a common segmentation abstraction.  TLS encrypts data exchanged between a client and server across an untrusted network, which is the foundation of HTTPS. [tsapps.nist](https://tsapps.nist.gov/publication/get_pdf.cfm?pub_id=935714)

## 1. Network Security Objective

**Network security** protects data and services while they travel across wired, wireless, corporate, and cloud networks.

It preserves:

| Goal | Meaning | Airline example |
|---|---|---|
| Confidentiality | Data cannot be read by unauthorized parties | Passenger PII and payment data over HTTPS |
| Integrity | Data is not altered in transit | Flight-status update cannot be tampered with |
| Availability | Network/service remains accessible | Booking and check-in systems remain online |
| Authentication | Verifies sender/receiver identity | Airline partner API validates mTLS certificate |
| Authorization | Controls permitted access | Ground staff cannot access payroll VLAN |
| Non-repudiation | Action cannot be denied | Signed audit trail for booking cancellation |

## 2. Network Types and Risks

| Network type | Description | Airline example | Main risks |
|---|---|---|---|
| LAN | Local wired network | Airport operations office LAN | Rogue device, ARP spoofing |
| WLAN/Wi-Fi | Wireless LAN | Passenger and staff Wi-Fi | Evil twin, rogue AP, eavesdropping |
| VLAN | Logical segmentation within physical network | Separate guest, crew, ops, CCTV VLANs | VLAN hopping if misconfigured |
| Corporate LAN | Internal enterprise network | Airline HQ/airport back-office | Lateral movement, insider misuse |
| WAN | Connects geographically separate sites | Airline data centers and airports | WAN link interception/outage |
| VPN network | Encrypted remote/private network | Employee VPN to airline systems | Credential theft, split-tunnel leakage |
| Cloud VPC/VNet | Virtual cloud network | AWS VPC hosting booking service | Open security group, exposed endpoint |
| OT/ICS network | Operational technology network | Baggage handling, runway lighting, BHS | Safety impact, legacy protocols |

## 3. Firewalls

A **firewall** enforces allow/deny rules for network traffic based on criteria such as source/destination IP, port, protocol, application, identity, and content.

### Firewall types

| Type | Works at | Inspects | Airline example |
|---|---|---|---|
| Packet-filtering firewall | Layer 3/4 | IP, port, protocol | Blocks inbound access to internal DB port |
| Stateful firewall | Layer 3/4 | Connection state | Allows only responses to outbound requests |
| Application firewall | Layer 7 | HTTP/API payload | Blocks SQL-injection patterns |
| NGFW | Multiple layers | Apps, users, content, threat intel | Identifies and controls SaaS traffic |
| WAF | Layer 7 | Web/API requests | Protects airline booking API |
| Cloud security group / firewall | Virtual network | IP, port, protocol | Restricts VPC access to booking service |
| Host-based firewall | Endpoint | Per-device traffic | Blocks unauthorized inbound connection on agent laptop |

### Firewall rule example

| Source | Destination | Port | Action | Purpose |
|---|---|---:|---|---|
| Internet | API gateway | 443 | Allow | Public booking API |
| Internet | Booking DB | 5432 | Deny | Database must not be public |
| Crew VLAN | Flight-ops service | 443 | Allow | Crew operations |
| Guest Wi-Fi | Corporate LAN | Any | Deny | Isolate passengers |
| Booking service | Payment service | 443 | Allow | Payment API |
| Any | Internet | Any | Deny | Default deny |

**Best practices:** default deny, least privilege, document every rule, remove stale rules, review rules periodically, and log denied traffic. Segmentation guidance recommends separating endpoints, servers, databases, IoT/OT, and management functions, with deny-by-default rules. [privsec.harvard](https://privsec.harvard.edu/best-practices-network-security-segmentation)

## 4. VPN

A **VPN (Virtual Private Network)** creates an encrypted tunnel over an untrusted network, allowing secure remote access or site-to-site connectivity.

### VPN types

| Type | Use case | Airline example |
|---|---|---|
| Remote-access VPN | Individual user connects to corporate network | Airline employee works remotely |
| Site-to-site VPN | Connects two networks | Airline data center to airport data center |
| SSL/TLS VPN | Browser/client-based secure access | Partner portal access |
| IPSec VPN | Network-layer encrypted tunnel | Branch-office connectivity |
| WireGuard | Modern lightweight VPN protocol | Secure internal access |
| Cloud VPN | Connects on-premises to cloud | Airport systems to AWS VPC |

### Split tunnel vs full tunnel

| Mode | Internet traffic | Corporate traffic | Risk |
|---|---|---|---|
| Full tunnel | Through VPN | Through VPN | More secure, higher bandwidth use |
| Split tunnel | Directly through local ISP | Through VPN | Better performance; local device/network risk |

**Airline policy example:** full tunnel for privileged admin access; split tunnel may be allowed only for low-risk corporate browsing with endpoint compliance checks.

## 5. IDS and IPS

| Control | Full form | Action | Placement | Airline example |
|---|---|---|---|---|
| IDS | Intrusion Detection System | Detects and alerts | Inline or passive/span port | Alerts on port scan of booking API |
| IPS | Intrusion Prevention System | Detects and blocks | Inline | Drops SQL-injection payload |
| NIDS | Network IDS | Monitors network packets | Network segment | Detects lateral movement |
| HIDS | Host IDS | Monitors one host | Server/endpoint | Detects modified system files |
| NDR | Network Detection and Response | Detects/responds using network analytics | Network | Detects abnormal data transfer |

### IDS vs IPS

| Feature | IDS | IPS |
|---|---|---|
| Primary action | Alert | Block/prevent |
| Deployment | Often passive | Inline |
| False-positive impact | Alert noise | May block legitimate traffic |
| Latency impact | Low if passive | Adds inline processing |
| Airline use | Detect suspicious API scanning | Block known exploit attempts |

Firewalls enforce approved paths, IDS raises alerts, and IPS can block malicious traffic. [nutmegtech](https://nutmegtech.com/network-segmentation-best-practices/)

## 6. Encryption in Transit

**Encryption in transit** protects data moving between clients, services, networks, and cloud environments.

| Protocol/technology | Purpose | Airline example |
|---|---|---|
| TLS | Encrypts transport-layer communication | Mobile app to booking API |
| HTTPS | HTTP over TLS | Passenger books flight |
| mTLS | Mutual certificate authentication | Airline-to-airport service integration |
| SSH | Secure remote administration | Admin accesses cloud VM |
| IPsec | Network-layer VPN encryption | Site-to-site airline network |
| SFTP/FTPS | Secure file transfer | Passenger-manifest file exchange |
| WPA3 | Wi-Fi encryption/authentication | Staff and passenger Wi-Fi |
| VPN tunnel | Encrypted private network path | Remote employee access |

TLS enables secure client-server communication over an untrusted network by encrypting exchanged data. [developer.mozilla](https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/Transport_Layer_Security)

### TLS handshake essentials

```text
Client Hello
   -> Supported TLS versions, cipher suites, random value

Server Hello
   -> Selected TLS version/cipher, certificate, random value

Key exchange / authentication
   -> Server certificate verified
   -> Session keys established

Encrypted application data
   -> HTTPS request/response protected
```

### TLS best practices

- Use TLS 1.2 minimum; prefer TLS 1.3.
- Disable SSLv3, TLS 1.0, and TLS 1.1.
- Use strong cipher suites and forward secrecy.
- Use valid certificates from a trusted CA.
- Enable HSTS.
- Rotate certificates before expiry.
- Use mTLS for high-trust service-to-service communication.
- Never send secrets in URLs or logs.

## 7. Wi-Fi Security

| Wi-Fi security mode | Status | Airline use |
|---|---|---|
| WEP | Broken; never use | None |
| WPA | Deprecated | Avoid |
| WPA2-Personal | Common but shared passphrase risk | Small/legacy guest networks |
| WPA2-Enterprise | 802.1X authentication | Staff Wi-Fi |
| WPA3-Personal | Uses SAE; stronger personal Wi-Fi | Modern passenger/guest Wi-Fi |
| WPA3-Enterprise | 802.1X with stronger protections | Airline staff/privileged devices |
| Open Wi-Fi | No encryption | Avoid for corporate access |

WPA3 is mandatory for Wi-Fi CERTIFIED devices and includes additional protections for personal and enterprise networks.  WPA3-Enterprise 192-bit mode is intended for sensitive environments with higher security requirements. [wi-fi](https://www.wi-fi.org/security)

### Wi-Fi attacks

| Attack | Description | Defense |
|---|---|---|
| Evil twin | Rogue AP impersonates legitimate SSID | Certificate validation, WPA3-Enterprise |
| Rogue access point | Unauthorized AP connected to LAN | Wireless IDS, AP onboarding |
| Karma attack | AP responds to device probe requests | Disable probing, use enterprise Wi-Fi |
| Deauthentication | Forces clients off network | Protected Management Frames |
| Krack | WPA2 handshake weakness | Patch devices, use WPA3 |
| Packet sniffing | Captures wireless traffic | Encrypt all traffic with TLS/VPN |

**Airport rule:** Passenger Wi-Fi must be isolated from flight-operations, baggage, CCTV, payment, and corporate networks.

## 8. VLANs and Segmentation

A **VLAN** logically separates devices even when they use the same physical switch. VLAN tagging commonly uses IEEE 802.1Q. [tigera](https://www.tigera.io/learn/guides/microsegmentation/network-segmentation-nist/)

### Airline VLAN design

| VLAN | Users/systems | Access policy |
|---|---|---|
| Passenger Wi-Fi | Travellers | Internet only; no internal access |
| Corporate LAN | Office staff | Controlled access to business apps |
| Crew VLAN | Crew devices | Flight-ops/roster systems |
| Airport Operations | Ops staff | Flight-ops, gate, runway systems |
| Baggage/OT | Baggage handling systems | Strictly isolated; limited monitoring |
| PCI/payment | Payment systems | PCI scope, tight restrictions |
| Management | Admin/jump hosts | MFA, PAM, restricted source IPs |
| CCTV/IoT | Cameras, sensors | Isolated, no internet |
| DMZ | Public web/API | Internet-facing only |

### Segmentation benefits

- Limits lateral movement after compromise.
- Reduces attack surface.
- Contains ransomware impact.
- Separates PCI and PII data.
- Protects safety-critical OT systems.
- Enables different security policies per zone.

### Microsegmentation

**Microsegmentation** enforces fine-grained policies between individual workloads, not merely between large network zones.

```text
Booking service -> Payment service: HTTPS 443 only
Booking service -> Passenger DB: PostgreSQL 5432 only
Booking service -> Internet: Deny
Notification service -> Booking service: Deny
```

In cloud environments, implement this through security groups, network policies, service mesh authorization policies, and identity-aware access.

## 9. Endpoint Security

An **endpoint** is any user or workload device that connects to the network.

| Endpoint | Airline example |
|---|---|
| Laptop | Airline employee laptop |
| Mobile phone | Crew/passenger app device |
| Tablet | Gate agent tablet |
| Kiosk | Airport self-check-in kiosk |
| POS terminal | Airport retail/payment terminal |
| Server/VM | Booking API server |
| Container | Flight-search microservice |
| IoT device | Baggage scanner, CCTV camera |

### Endpoint controls

| Control | Purpose | Airline example |
|---|---|---|
| Antivirus/anti-malware | Detect known malware | Blocks malicious download |
| EDR | Detects/responds to advanced threats | Isolates infected laptop |
| MDM/UEM | Manages mobile devices | Enforces encryption and passcode |
| Patch management | Fixes vulnerabilities | Updates kiosk OS/browser |
| Disk encryption | Protects data at rest | BitLocker/FileVault on laptops |
| Host firewall | Controls endpoint traffic | Blocks unexpected inbound port |
| Application allowlisting | Runs only approved apps | Kiosk runs only check-in app |
| USB control | Prevents malicious removable media | Blocks unknown USB devices |
| Screen lock | Prevents shoulder surfing | Auto-lock after inactivity |
| DLP agent | Prevents data exfiltration | Blocks passenger-data upload |
| Certificate/device posture | Verifies device health before access | NAC blocks unpatched device |

## 10. Cloud Network Security

| Control | Purpose | Airline example |
|---|---|---|
| VPC/VNet | Private virtual network | Booking services in isolated VPC |
| Subnet | Network subdivision | Public subnet for gateway; private for DB |
| Security group | Instance-level firewall | Allow 443 only from gateway |
| Network ACL | Subnet-level rules | Additional subnet boundary control |
| Private endpoint | Private access to cloud service | DB accessible only inside VPC |
| NAT gateway | Controlled outbound access | Booking service fetches partner data |
| Cloud WAF | Protects web/API traffic | Blocks malicious requests |
| Cloud firewall | Central network policy | Restricts east-west traffic |
| Private link | Private service connectivity | Airline connects to payment provider |
| Flow logs | Records network traffic | Investigates suspicious connections |
| Bastion/jump host | Controlled admin access | Secure DB administration |
| IAM | Identity-based cloud access | Least-privilege service roles |

### Cloud network rule example

```text
Allow:
  Internet -> API Gateway : 443
  API Gateway -> Booking Service : 443
  Booking Service -> Payment Service : 443
  Booking Service -> Managed PostgreSQL : 5432

Deny:
  Internet -> Booking Service : Any
  Internet -> PostgreSQL : Any
  Passenger Wi-Fi -> Corporate VPC : Any
```

## 11. Network Security Best Practices

- Use **default deny** firewall policies.
- Segment passenger, corporate, payment, operations, OT, and management networks.
- Use **802.1X/NAC** before granting LAN/Wi-Fi access.
- Use **WPA3-Enterprise** for staff Wi-Fi where possible.
- Encrypt all sensitive traffic with **TLS 1.2+**, preferably TLS 1.3.
- Use **mTLS** for critical service-to-service integrations.
- Place IDS/IPS at internet, DMZ, internal, and cloud boundaries.
- Patch network devices, endpoints, and cloud images regularly.
- Disable unused ports, services, and administrative interfaces.
- Use MFA and PAM for firewall/VPN/router administration.
- Monitor flow logs, DNS logs, firewall logs, and EDR alerts in a SIEM.
- Test segmentation with penetration tests and attack-path analysis.
- Maintain documented network diagrams and asset inventory.

## 12. Rapid Revision: Network Security

- **Firewall** regulates traffic using security policies.
- **VPN** encrypts communication over public networks.
- **IDS** detects suspicious activity; **IPS** blocks it.
- **TLS/HTTPS** protects data in transit.
- **VLAN** logically segments a physical network.
- **DMZ** isolates internet-facing systems.
- **NAC/802.1X** controls device access to the network.
- **WPA3** is the modern Wi-Fi security standard.
- **EDR** protects and responds on endpoints.
- **MDM/UEM** manages mobile devices.
- **Microsegmentation** enforces workload-level network policies.
- **Security group** is a cloud instance-level virtual firewall.
- **Network ACL** is a subnet-level cloud firewall.
- **Private endpoint** keeps cloud-service traffic off the public internet.
- **Zero Trust** verifies every user, device, and request.
- **Lateral movement** is attacker movement between internal systems.
- **Evil twin** is a rogue Wi-Fi access point.
- **Rogue AP** is an unauthorized wireless access point.
- **Split tunneling** sends only corporate traffic through VPN.
- **Full tunneling** sends all traffic through VPN.

## 13. Probable One-Mark Questions

1. **Which device regulates network traffic based on security policies?**  
   Firewall.

2. **Which technology encrypts data over public networks?**  
   VPN.

3. **Which system detects suspicious network activity but does not block it?**  
   IDS.

4. **Which system detects and blocks suspicious network activity?**  
   IPS.

5. **Which protocol secures HTTP traffic in transit?**  
   TLS; HTTPS is HTTP over TLS. [developer.mozilla](https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/Transport_Layer_Security)

6. **What does VLAN stand for?**  
   Virtual Local Area Network.

7. **What is the main purpose of VLANs?**  
   Logical network segmentation.

8. **Which Wi-Fi standard is the modern recommended security standard?**  
   WPA3. [wi-fi](https://www.wi-fi.org/security)

9. **What is an evil twin attack?**  
   A rogue Wi-Fi access point impersonating a legitimate network.

10. **What is a rogue access point?**  
   An unauthorized wireless access point connected to a network.

11. **Which control verifies a device before allowing LAN access?**  
   NAC / 802.1X.

12. **Which endpoint technology detects and responds to advanced threats?**  
   EDR.

13. **Which cloud control acts as an instance-level firewall?**  
   Security group.

14. **Which cloud control acts at subnet level?**  
   Network ACL.

15. **Which network zone hosts internet-facing systems?**  
   DMZ.

16. **Which segmentation technique limits lateral movement?**  
   Network segmentation/microsegmentation. [tsapps.nist](https://tsapps.nist.gov/publication/get_pdf.cfm?pub_id=935714)

17. **Which VPN mode sends only corporate traffic through the VPN?**  
   Split tunneling.

18. **Which VPN mode sends all traffic through the VPN?**  
   Full tunneling.

19. **Which protocol provides mutual certificate-based authentication?**  
   mTLS.

20. **Which network security goal is primarily targeted by a DDoS attack?**  
   Availability.
