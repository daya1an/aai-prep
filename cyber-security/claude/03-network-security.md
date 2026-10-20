# 03. Network Security

Subject: Cybersecurity Fundamentals. Part 3 of 5.

Covers: networking basics for security, firewalls, VPN, IDS/IPS, encryption, segmentation, endpoint security, cloud security, wireless, email and DNS security, Zero Trust, monitoring, and hardening.

Install once for the code that needs it: `pip install cryptography`

---

## 1. What is Network Security

**Analogy.** A city has roads (cables, Wi-Fi), junctions (switches, routers), checkpoints (firewalls), armoured tunnels (VPN), sealed packages (encryption), and security cameras (IDS). Network security is the whole traffic control system.

**Definition.** Network security protects data as it travels across networks such as Wi-Fi, LAN, VLAN, corporate networks, and cloud environments. It prevents unauthorized access, data breaches, and disruptions by keeping data **confidential, intact, and available during transmission**.

**Key components (from the syllabus).** Firewalls, VPNs, IDS/IPS, encryption, network segmentation, endpoint security, cloud security.

---

## 2. Networking Basics You Must Know

### 2.1 Network types

| Type | Meaning | Example |
|---|---|---|
| PAN | Personal Area Network | Phone to Bluetooth headset |
| LAN | Local Area Network, one building or campus | Office network |
| WLAN | Wireless LAN | Office Wi-Fi |
| MAN | Metropolitan Area Network | Links between offices in a city |
| WAN | Wide Area Network | Link between branch offices in different cities |
| VLAN | Virtual LAN. Logical split of one physical network | Guest Wi-Fi VLAN and server VLAN on the same switch |
| VPN | Virtual Private Network | Remote access to the office |
| SD-WAN | Software-defined WAN | Smart routing between sites |
| Intranet, Extranet, Internet | Internal, partner-shared, public | Extranet for vendors |

### 2.2 OSI and TCP/IP with the security view

| Layer | OSI name | Examples | Typical attacks | Security tools |
|---|---|---|---|---|
| 7 | Application | HTTP, DNS, SMTP, FTP | SQLi, XSS, phishing | WAF, proxy, secure coding |
| 6 | Presentation | TLS, encoding | Weak encryption, downgrade | Strong TLS settings |
| 5 | Session | Sessions, RPC | Session hijacking | Session timeouts, tokens |
| 4 | Transport | TCP, UDP | SYN flood, port scan | Stateful firewall |
| 3 | Network | IP, ICMP, routing | IP spoofing, Smurf, route hijack | Router ACL, IPsec |
| 2 | Data link | Ethernet, ARP, VLAN | ARP spoofing, MAC flooding, VLAN hopping | Port security, DAI, 802.1X |
| 1 | Physical | Cables, radio | Tapping, jamming | Locks, shielded cable, fibre |

Memory aid, top to bottom: **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing.

TCP/IP layers: Application, Transport, Internet, Network Access.

### 2.3 Concepts used everywhere

| Concept | Meaning |
|---|---|
| IP address | Logical address (IPv4 like 10.1.2.3, IPv6 is longer) |
| Private ranges | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 |
| MAC address | Hardware address of a network card |
| Port | Number that identifies a service on a host (0 to 65535) |
| TCP | Reliable, connection-oriented |
| UDP | Fast, connectionless |
| Three-way handshake | SYN, SYN-ACK, ACK |
| DNS | Converts names to IP addresses |
| DHCP | Gives IP addresses automatically |
| ARP | Maps IP to MAC on a LAN |
| NAT and PAT | Hide many private IPs behind one public IP |
| Router | Connects networks, forwards by IP |
| Switch | Connects devices in a LAN, forwards by MAC |
| Gateway | Exit point to another network |
| Proxy | Middle server that makes requests for clients |
| Subnet mask and CIDR | Splits address space (10.1.2.0/24 means 256 addresses) |

### 2.4 Common ports and secure alternatives

| Port | Service | Secure? | Better choice |
|---|---|---|---|
| 20, 21 | FTP | No (plain text) | SFTP (22) or FTPS (990) |
| 22 | SSH and SFTP | Yes | Keep, use keys |
| 23 | Telnet | No | SSH |
| 25 | SMTP | Plain | Submission 587 or SMTPS 465 with TLS |
| 53 | DNS | Plain | DNSSEC, DoT (853), DoH |
| 67, 68 | DHCP | Plain | DHCP snooping |
| 69 | TFTP | No | SFTP |
| 80 | HTTP | No | HTTPS |
| 88 | Kerberos | Yes | Keep |
| 110 | POP3 | Plain | POP3S 995 |
| 123 | NTP | Plain | Authenticated NTP |
| 143 | IMAP | Plain | IMAPS 993 |
| 161, 162 | SNMP | v1 and v2c weak | SNMPv3 |
| 389 | LDAP | Plain | LDAPS 636 |
| 443 | HTTPS | Yes | Keep |
| 445 | SMB | Risky if exposed | Never expose to the internet |
| 514 | Syslog | Plain | Syslog over TLS |
| 1433 | MS SQL | Internal only | Restrict |
| 3306 | MySQL | Internal only | Restrict |
| 3389 | RDP | Risky | VPN, MFA, gateway |
| 5432 | PostgreSQL | Internal only | Restrict |

---

## 3. Firewalls

### 3.1 Concept

**Analogy.** A security gate at a building entrance. It checks each visitor (packet) against rules and allows or blocks.

**Definition.** A firewall is a device or software that allows or blocks network traffic based on a set of security rules. It sits between networks of different trust levels, such as the internet and the company network.

### 3.2 Generations and types

| Type | Works at | Looks at | Pros | Cons |
|---|---|---|---|---|
| Packet filtering | Layer 3 and 4 | Source IP, destination IP, port, protocol | Fast, simple | No memory of connections, easy to fool |
| Stateful inspection | Layer 3 and 4 | Packet plus connection state | Smarter, default in most devices | Cannot read application content |
| Circuit-level gateway | Layer 5 | TCP handshake and session validity | Hides internal hosts | No content check |
| Proxy or application-level gateway | Layer 7 | Full application data | Deep control, strong logging | Slower, per-protocol |
| Next-Generation Firewall (NGFW) | Layers 3 to 7 | Stateful plus app identification, user identity, IPS, TLS inspection, threat intelligence | Rich control | Costly, needs tuning |
| WAF (Web Application Firewall) | Layer 7 for web | HTTP requests | Blocks SQLi, XSS, bots | Only web traffic |
| UTM (Unified Threat Management) | Many | Firewall, antivirus, IPS, VPN, filtering in one box | Easy for small sites | Single point of failure |
| Host-based firewall | On the endpoint | Per-device traffic | Protects even inside the LAN | Many devices to manage |
| Network firewall | At the perimeter or between zones | All traffic through it | Central | Bypass if misplaced |
| Cloud firewall (FWaaS, security groups) | Cloud | Cloud traffic | Elastic | Misconfiguration risk |

### 3.3 Rules and ACLs

A rule has: source, destination, protocol, port, action (allow, deny, drop, reject), logging flag, and order.

| # | Source | Destination | Protocol/Port | Action |
|---|---|---|---|---|
| 1 | Internet | DMZ web server | TCP 443 | Allow |
| 2 | Staff VLAN | Internet | TCP 80, 443 | Allow |
| 3 | Guest VLAN | Staff VLAN | Any | Deny |
| 4 | Any | Any | Any | Deny (default) |

**Key ideas**
* **Rules are checked from top to bottom. The first match wins.** Specific rules go above general rules.
* **Default deny** (implicit deny): anything not allowed is blocked. The opposite (default allow) is unsafe.
* **Drop vs reject.** Drop silently discards (hides existence). Reject sends back an error.
* **Ingress** filters incoming traffic. **Egress** filters outgoing traffic (stops data theft and malware calling home).
* **Stateful table.** The firewall remembers outbound connections and automatically allows the matching return traffic.
* **Rule hygiene.** Review regularly, remove unused rules, document owners, avoid "any any allow".
* **Anti-spoofing.** Drop packets from outside that claim to have inside source addresses.

### 3.4 DMZ architectures

| Design | Description |
|---|---|
| Single firewall (three-legged) | One firewall with three interfaces: Internet, DMZ, Internal |
| Dual firewall (screened subnet) | Outer firewall, DMZ in the middle, inner firewall. Stronger |

Public servers (website, mail gateway) live in the DMZ. Even if one is hacked, the attacker is still outside the internal network.

```
Internet --[Outer FW]-- DMZ (web, mail relay) --[Inner FW]-- Internal LAN (databases, file servers)
```

### 3.5 NAT and PAT

* **NAT** maps private IPs to public IPs. Side effect: hides internal addressing, but it is not a security control on its own.
* **PAT** (NAT overload) maps many private hosts to one public IP using different ports.

### 3.6 Limits of firewalls

* Cannot stop attacks that come through allowed traffic (a phishing link over HTTPS).
* Cannot stop insiders on the inside.
* Cannot inspect encrypted traffic unless it decrypts it (TLS inspection).
* Misconfiguration is the most common failure.

### 3.7 Python: a rule-based firewall

```python
import ipaddress

# Each rule: (source network, destination network, protocol, port or None, action)
rules = [
    ("0.0.0.0/0",     "203.0.113.10/32", "tcp", 443,  "allow"),   # internet to DMZ web
    ("10.10.0.0/16",  "0.0.0.0/0",       "tcp", 443,  "allow"),   # staff to internet HTTPS
    ("10.99.0.0/16",  "10.10.0.0/16",    "any", None, "deny"),    # guest to staff
]

def decide(src, dst, proto, port):
    src_ip, dst_ip = ipaddress.ip_address(src), ipaddress.ip_address(dst)
    for r_src, r_dst, r_proto, r_port, action in rules:
        if (src_ip in ipaddress.ip_network(r_src)
                and dst_ip in ipaddress.ip_network(r_dst)
                and r_proto in ("any", proto)
                and r_port in (None, port)):
            return action
    return "deny"   # default deny

print(decide("8.8.8.8", "203.0.113.10", "tcp", 443))     # allow
print(decide("10.99.1.5", "10.10.2.2", "tcp", 445))      # deny
print(decide("8.8.8.8", "10.10.2.2", "tcp", 3389))       # deny (default)
```

### 3.8 Python: stateful behavior

```python
established = set()   # remembers outbound connections

def outbound(src, sport, dst, dport):
    established.add((src, sport, dst, dport))
    return "allow"

def inbound(src, sport, dst, dport):
    # a reply packet looks like the outbound one reversed
    if (dst, dport, src, sport) in established:
        return "allow (reply to known connection)"
    return "deny (unsolicited)"

print(outbound("10.10.0.5", 50000, "93.184.216.34", 443))
print(inbound("93.184.216.34", 443, "10.10.0.5", 50000))   # allowed reply
print(inbound("93.184.216.34", 443, "10.10.0.5", 50001))   # denied
```

---

## 4. VPN (Virtual Private Network)

### 4.1 Concept

**Analogy.** A sealed armoured tunnel through a public road. People see a vehicle go in and come out but cannot see or change what is inside.

**Definition.** A VPN creates an encrypted tunnel over an untrusted network (usually the internet) so that data stays confidential and intact, and so remote users or sites behave as if they were on the private network.

### 4.2 Why

* Remote staff reach the company network safely.
* Link two offices (site-to-site) over the internet at low cost.
* Protect data on public Wi-Fi.

### 4.3 Types

| Type | Meaning | Example |
|---|---|---|
| Remote access VPN | One user to the corporate network | Engineer connects from home |
| Site-to-site VPN | Network to network | Branch office to head office |
| Client-based | Software installed on the device | IPsec or SSL VPN client |
| Clientless (browser) | Uses a web portal over HTTPS | Access to a single web app |
| Full tunnel | All traffic goes through the VPN | More control, more load |
| Split tunnel | Only company traffic goes through the VPN | Less load, more risk |
| MPLS VPN | Provider-built private network | Carrier-managed link |

### 4.4 VPN protocols

| Protocol | Notes |
|---|---|
| IPsec | Layer 3 suite. Strong. Common for site-to-site |
| SSL/TLS VPN | Uses TLS over 443, passes through most firewalls |
| OpenVPN | Open source, TLS based |
| WireGuard | Modern, small code, fast |
| L2TP/IPsec | L2TP provides the tunnel, IPsec provides the encryption |
| PPTP | Old and broken. Do not use |
| SSTP | Microsoft TLS based |
| IKEv2 | Key exchange used with IPsec, good for mobile roaming |

### 4.5 IPsec in detail

| Component | Role |
|---|---|
| AH (Authentication Header) | Integrity and authentication. No encryption |
| ESP (Encapsulating Security Payload) | Encryption plus integrity and authentication. Most used |
| IKE (Internet Key Exchange) | Negotiates keys and algorithms (UDP 500, NAT-T 4500) |
| SA (Security Association) | One-way agreement on keys and algorithms. Two are needed for two-way traffic |
| Transport mode | Protects only the payload. Host to host |
| Tunnel mode | Wraps the entire original packet in a new one. Gateway to gateway |

IKEv1 has Phase 1 (secure channel between peers) and Phase 2 (IPsec SAs). IKEv2 simplifies this.

### 4.6 VPN weaknesses and the modern direction

* Once connected, users may get too much network access. Fix with least privilege and segmentation.
* VPN gateways are attractive targets. Patch them urgently and require MFA.
* **ZTNA** (Zero Trust Network Access) gives access per application after checking user and device, instead of broad network access.

### 4.7 Python: the tunnel idea

```python
from cryptography.fernet import Fernet   # pip install cryptography

tunnel_key = Fernet.generate_key()       # agreed by both ends (IKE does this in real VPNs)
tunnel = Fernet(tunnel_key)

inner_packet = b"SRC=10.10.0.5 DST=10.20.0.9 DATA=confidential report"
outer_payload = tunnel.encrypt(inner_packet)   # what travels on the internet
print(outer_payload[:40], b"...")              # unreadable

print(tunnel.decrypt(outer_payload))           # the other end restores the original
```

---

## 5. IDS and IPS

### 5.1 Concept

**Analogy.** An IDS is a burglar alarm that rings. An IPS is a guard who rings the alarm and also shuts the door.

| Point | IDS (Intrusion Detection System) | IPS (Intrusion Prevention System) |
|---|---|---|
| Action | Detect and alert | Detect and block |
| Position | Out of band (watches a copy via SPAN or TAP) | In line (traffic passes through it) |
| Impact on traffic | None | Can delay or block good traffic if wrong |
| Failure | Misses attacks | Failure may cut the link (use fail-open or bypass settings) |

### 5.2 Types by location

| Type | Meaning | Example |
|---|---|---|
| NIDS or NIPS | Network based, watches network traffic | Sensor at the DMZ |
| HIDS or HIPS | Host based, watches logs, files, and processes on one machine | On a database server |
| Wireless IDS or IPS | Watches radio for rogue APs | Detects a fake office Wi-Fi |
| NDR | Network Detection and Response, modern analytics | Spots lateral movement |

Tools to recognize: Snort, Suricata, Zeek (network), OSSEC and Wazuh (host).

### 5.3 Detection methods

| Method | How | Good at | Weak at |
|---|---|---|---|
| Signature based | Matches known attack patterns | Known attacks, few false alarms | New (zero-day) attacks |
| Anomaly based | Learns normal behavior and flags deviations | Unknown attacks | More false positives, needs training |
| Heuristic or behavior | Rules about suspicious behavior | In between | Needs tuning |
| Stateful protocol analysis | Compares traffic with protocol standards | Protocol abuse | Resource heavy |
| Reputation based | Known bad IPs and domains | Quick wins | Attackers change IPs |

### 5.4 Alert outcomes (confusion matrix)

| | Attack really happened | No attack |
|---|---|---|
| Alert raised | True Positive (TP) | False Positive (FP) |
| No alert | False Negative (FN) | True Negative (TN) |

* **False positive** wastes analyst time and causes alert fatigue.
* **False negative** is dangerous. A real attack is missed.
* Precision = TP / (TP + FP). Recall (detection rate) = TP / (TP + FN).

### 5.5 Placement

Sensors at the internet edge, DMZ, between internal zones (east-west), on critical servers, and in the cloud. Encrypted traffic needs TLS inspection or endpoint visibility.

### 5.6 Related tools

| Tool | Purpose |
|---|---|
| Honeypot | Decoy system. Any touch is suspicious. Gives early warning and intelligence |
| Honeynet | A network of honeypots |
| SIEM | Collects logs from all sources and correlates them (file 05) |
| SOAR | Automates response playbooks |
| Sandbox | Safe space to run suspicious files |
| Deception technology | Fake credentials and fake servers to trap attackers |

### 5.7 Python: signature and anomaly detection

```python
from collections import Counter
from statistics import mean, stdev

# 1) Signature based: look for known bad patterns in request lines
signatures = ["' OR '1'='1", "../../etc/passwd", "<script>"]
requests_seen = [
    "GET /products?id=42",
    "GET /products?id=x' OR '1'='1",
    "GET /download?file=../../etc/passwd",
]
for r in requests_seen:
    for s in signatures:
        if s in r:
            print("SIGNATURE ALERT:", r)

# 2) Anomaly based: flag a count far from the normal average (z-score)
requests_per_min = [102, 98, 110, 95, 105, 99, 101, 940]
mu, sd = mean(requests_per_min[:-1]), stdev(requests_per_min[:-1])
latest = requests_per_min[-1]
z = (latest - mu) / sd
if z > 3:
    print(f"ANOMALY ALERT: {latest} req/min, z-score {z:.1f}")

# 3) Threshold rule: many failed logins from one IP
failed = ["10.0.0.5"] * 6 + ["10.0.0.9"]
for ip, n in Counter(failed).items():
    if n >= 5:
        print("BRUTE FORCE ALERT from", ip)
```

---

## 6. Encryption and Cryptography

### 6.1 Why

Encryption makes data unreadable to anyone without the key. It is the main way to guarantee **confidentiality during transmission** (and storage). Along with hashing and signatures it supports **integrity**, **authenticity**, and **non-repudiation**.

**Terms.** Plaintext (readable), ciphertext (scrambled), key (secret value), algorithm or cipher (method), encryption and decryption, cryptanalysis (breaking), key length (bigger is stronger).

### 6.2 The three families

| Family | Keys | Speed | Gives | Algorithms | Typical use |
|---|---|---|---|---|---|
| Symmetric | One shared secret | Fast | Confidentiality | AES, ChaCha20, 3DES (old), DES (broken) | Encrypt bulk data, VPN traffic, disks |
| Asymmetric (public key) | Public and private pair | Slow | Key exchange, signatures, confidentiality of small data | RSA, ECC (ECDSA, ECDH), Diffie-Hellman, DSA | Share keys, certificates, signing |
| Hashing | No key | Fast | Integrity | SHA-256, SHA-3 (MD5 and SHA-1 are broken for security) | Fingerprints, password storage with salt |

**Analogy.**
* Symmetric: one key both locks and unlocks the same padlock. The problem is handing the key over safely.
* Asymmetric: anyone can drop a letter into your public mailbox (public key), only you hold the key to open it (private key).
* Hash: a fingerprint of the file. You cannot rebuild the file from it.

**Key sizes in practice.** AES-128 and AES-256. RSA 2048 bits or more. ECC 256 bits is about equal to RSA 3072.

### 6.3 Symmetric details

* **Block cipher** (AES works on 128-bit blocks) vs **stream cipher** (ChaCha20).
* **Modes of operation.** ECB (bad, repeats patterns, never use), CBC (needs an IV and separate integrity), CTR, **GCM** (encryption plus authentication, recommended).
* **IV or nonce.** Random or unique per message.
* **Key problem.** Sharing the key. Solve it with asymmetric key exchange.

### 6.4 Asymmetric details

| Goal | Who uses which key |
|---|---|
| Confidentiality | Sender encrypts with the **receiver's public key**. Receiver decrypts with their own **private key** |
| Digital signature | Sender signs with their **own private key**. Anyone verifies with the sender's **public key** |

**Diffie-Hellman (DH) key exchange.** Two parties create the same secret over a public channel without sending the secret. Ephemeral DH gives **Perfect Forward Secrecy (PFS)**: stealing the server key later does not reveal old sessions.

**Toy DH with small numbers** (real numbers are huge):

```python
p, g = 23, 5            # public values
a, b = 6, 15            # private secrets of Alice and Bob

A = pow(g, a, p)        # Alice sends A = 8
B = pow(g, b, p)        # Bob sends B = 19

shared_alice = pow(B, a, p)
shared_bob = pow(A, b, p)
print(A, B, shared_alice, shared_bob)   # 8 19 2 2
```

### 6.5 Hashing and message authentication

| Term | Meaning |
|---|---|
| Hash properties | One-way, fixed-size output, same input gives same output, tiny change gives a totally different output (avalanche), collision resistant |
| Collision | Two different inputs with the same hash. MD5 and SHA-1 have practical collisions |
| Salt | Random data added before hashing a password so identical passwords differ |
| Pepper | Extra secret value kept outside the database |
| HMAC | Hash with a secret key. Proves integrity and authenticity |
| MAC | Message Authentication Code, the general idea |
| Checksum vs hash | A checksum catches accidents. A cryptographic hash resists attackers |

```python
import hashlib
import hmac

msg = b"ORDER-1001|Amount: 5000|Account: 111122223333"
print(hashlib.sha256(msg).hexdigest())
print(hashlib.sha256(msg + b"!").hexdigest())   # completely different

key = b"shared-secret-between-service-A-and-B"
tag = hmac.new(key, msg, hashlib.sha256).hexdigest()
received_tag = tag    # arrives with the message
print(hmac.compare_digest(tag, received_tag))   # True means untouched and from a key holder
```

### 6.6 Digital signature

Process: hash the message, then encrypt the hash with the sender's private key. The receiver recomputes the hash, decrypts the signature with the sender's public key, and compares. Result: integrity, authenticity, non-repudiation. (It does not hide the message.)

```python
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.asymmetric import padding, rsa

private_key = rsa.generate_private_key(public_exponent=65537, key_size=2048)
public_key = private_key.public_key()

message = b"Approve payment of 50000 to vendor V-102"
pss = padding.PSS(mgf=padding.MGF1(hashes.SHA256()), salt_length=padding.PSS.MAX_LENGTH)

signature = private_key.sign(message, pss, hashes.SHA256())
public_key.verify(signature, message, pss, hashes.SHA256())   # silent means valid
print("Signature valid")

try:
    public_key.verify(signature, b"Approve payment of 90000 to vendor V-102", pss, hashes.SHA256())
except Exception:
    print("Tampered message detected")
```

### 6.7 PKI and certificates

**Problem.** How do you know a public key really belongs to example.com and not an attacker?

**Solution.** A trusted **Certificate Authority (CA)** signs a **digital certificate** that binds the public key to an identity.

| Item | Meaning |
|---|---|
| Certificate (X.509) | Holds subject name, public key, validity dates, issuer, signature |
| CA | Trusted issuer (root CA, intermediate CA) |
| Chain of trust | Server certificate, intermediate CA, root CA stored in the browser |
| RA | Registration Authority verifies identity for the CA |
| CRL and OCSP | Ways to check if a certificate has been revoked |
| CSR | Certificate Signing Request sent to the CA |
| Self-signed certificate | Signed by itself. Fine for tests, not trusted publicly |
| Wildcard certificate | Covers `*.example.com` |
| Expiry | Expired certificates cause outages and warnings. Track them |
| India | The Controller of Certifying Authorities (CCA) licenses CAs under the IT Act. eSign and DSC (Digital Signature Certificate) are used in government work |

### 6.8 TLS and HTTPS step by step

**TLS** (Transport Layer Security) replaced SSL. **HTTPS = HTTP over TLS.** TLS 1.2 and TLS 1.3 are current. SSL and TLS 1.0 and 1.1 are retired.

**TLS 1.2 handshake (simplified).**
1. ClientHello: supported versions and cipher suites, random number.
2. ServerHello: chosen suite, random number, certificate, key exchange details.
3. The client verifies the certificate chain and name.
4. The client sends key exchange data. Both sides compute the same session key.
5. Both send Finished. Encrypted data starts.

**TLS 1.3.** Fewer round trips (one), only strong cipher suites, forward secrecy by default, more of the handshake is encrypted.

**What the lock icon means.** The connection is encrypted and the certificate matches the domain. It does not mean the site is trustworthy. Phishing sites also use HTTPS.

**Other protections.** HSTS (the browser must use HTTPS), certificate pinning, TLS inspection by proxies for monitoring (needs a policy and privacy review).

### 6.9 Where encryption is used (secure protocols)

| Protocol | Protects |
|---|---|
| HTTPS | Web traffic |
| SSH | Remote login and file transfer |
| IPsec and TLS VPN | Network tunnels |
| WPA2 and WPA3 | Wi-Fi |
| S/MIME and PGP | Email content |
| DNSSEC | DNS answer authenticity (signed, not encrypted) |
| Full-disk encryption (BitLocker, LUKS) | Data at rest on laptops |

### 6.10 Key management

Keys are the real secret. Lifecycle: generate, store, distribute, use, rotate, revoke, archive, destroy.

* Store in an **HSM** (Hardware Security Module) or a cloud **KMS**.
* Never hard-code keys in source code.
* Rotate regularly and after any suspected leak.
* Separate duties (key custodians).
* **Key escrow** keeps a recoverable copy under strict control.

### 6.11 Newer topics

* **Post-quantum cryptography.** Future quantum computers could break RSA and ECC. New standards are being adopted. Plan for crypto agility (the ability to swap algorithms).
* **Homomorphic encryption.** Compute on encrypted data.
* **Steganography.** Hide data inside images. It hides that data exists, unlike encryption.
* **Obfuscation vs encryption.** Obfuscation only makes code hard to read.

### 6.12 Python: encrypt and decrypt

```python
from cryptography.fernet import Fernet, InvalidToken   # symmetric, AES with authentication

key = Fernet.generate_key()
f = Fernet(key)
secret = f.encrypt(b"Customer: Ravi Kumar, Card: 4111111111111111")
print(f.decrypt(secret))

# Wrong key fails safely
try:
    Fernet(Fernet.generate_key()).decrypt(secret)
except InvalidToken:
    print("Wrong key: decryption refused")
```

---

## 7. Network Segmentation

### 7.1 Concept

**Analogy.** Watertight compartments in a ship. If one fills with water, the ship does not sink.

**Definition.** Dividing a network into smaller zones with controlled paths between them so that an attacker who enters one zone cannot freely roam everywhere (**limits lateral movement**) and so that critical systems are shielded.

### 7.2 Techniques

| Technique | Meaning |
|---|---|
| Physical segmentation | Separate switches and cables |
| VLAN | Logical separation on shared switches (802.1Q tagging). Traffic between VLANs passes a router or firewall |
| Subnetting | Different IP ranges per zone, with ACLs between them |
| Firewall zones | Interfaces labelled Untrusted, DMZ, Trusted, Restricted |
| DMZ | Zone for public-facing services |
| Micro-segmentation | Per-workload policies, often in virtual or cloud environments |
| Air gap | No network link at all. Data moves by controlled media. Real air gaps are rare because USB and maintenance links bridge them |
| One-way gateway (data diode) | Data can flow only out, never in. Used for OT |
| Jump host or bastion | Single hardened entry point for administration |
| NAC and 802.1X | Only authenticated, compliant devices join a segment |
| Zero Trust segmentation | Every request is verified regardless of zone |

**Traffic directions.** North-south = in and out of the data centre or site. East-west = between servers inside. Attackers move east-west, so inspect it.

### 7.3 Example zone design for a medium organization

| Zone | Contents | Allowed to talk to |
|---|---|---|
| Guest Wi-Fi VLAN | Visitor phones | Internet only |
| Public DMZ | Website, mail gateway | Internet inbound limited, reaches app servers via narrow rules |
| Corporate VLAN | Staff PCs, email | Internet, approved servers |
| Server VLAN | Application and database servers | Only required sources and ports |
| Restricted VLAN | Payment systems, key stores | Almost nothing else (strict) |
| OT or IoT network | Cameras, building and industrial controllers | Only their own management server |
| Security VLAN | CCTV recorders, access control, SIEM collectors | Security team only |
| Management VLAN | Switch and server admin interfaces | Jump host only |
| Vendor access zone | Remote support | Time-limited, recorded sessions |

### 7.4 Python: zone policy matrix

```python
# Allowed flows between zones. Anything not listed is denied.
allowed = {
    ("guest", "internet"),
    ("corporate", "internet"),
    ("corporate", "server"),
    ("dmz", "server"),
    ("management", "server"),
    ("management", "restricted"),
}

def flow_allowed(src_zone, dst_zone):
    return (src_zone, dst_zone) in allowed

tests = [("guest", "internet"), ("guest", "restricted"), ("corporate", "restricted"), ("management", "restricted")]
for s, d in tests:
    print(f"{s:11s} -> {d:11s} : {'ALLOW' if flow_allowed(s, d) else 'DENY'}")
```

### 7.5 Port security and Layer 2 protections

| Feature | Stops |
|---|---|
| Port security (limit MACs per port) | MAC flooding |
| DHCP snooping | Rogue DHCP servers |
| Dynamic ARP Inspection (DAI) | ARP spoofing |
| BPDU guard and root guard | Spanning tree manipulation |
| Disable unused ports | Plug-in attacks |
| Native VLAN hardening and no DTP | VLAN hopping |
| 802.1X | Unauthorized devices |

---

## 8. Endpoint Security

### 8.1 Concept

**Analogy.** Every room (laptop, server, phone) has its own door lock, not only the building entrance.

**Definition.** Protecting end-user devices (PCs, laptops, servers, mobiles, kiosks, tablets, IoT) from being the entry point for attackers.

### 8.2 Layers of endpoint protection

| Control | Purpose |
|---|---|
| Antivirus and anti-malware | Signature, heuristic, behavior detection |
| EPP (Endpoint Protection Platform) | Prevention bundle |
| EDR | Records endpoint activity, detects, investigates, responds (isolates the host) |
| XDR | Correlates endpoint, network, email, cloud |
| MDR | Managed detection and response service |
| Host firewall and HIPS | Local traffic control |
| Patch management | Close known vulnerabilities |
| Hardening and baselines | Disable unneeded services, apply CIS benchmarks |
| Application control (allow-listing) | Only approved software runs |
| Device control | Block or scan USB and removable media |
| Full-disk encryption | Protect lost or stolen devices (BitLocker, FileVault) |
| Secure boot and TPM | Ensure trusted startup, hold keys |
| Least privilege | Staff do not use admin accounts daily |
| PAM | Vault and monitor admin credentials |
| MDM and MAM | Control and wipe mobile devices and apps |
| BYOD policy | Rules for personal devices |
| Email and web protection | Block malicious attachments and sites |
| DLP | Block sensitive data leaving |
| Backup | Recover after ransomware |

### 8.3 Special endpoints

| Endpoint | Risks | Controls |
|---|---|---|
| Public kiosks and self-service terminals | Physical tampering, USB, skimmers | Locked cases, kiosk-mode OS, tamper alarms |
| Shared or pooled PCs | Multiple users, shared images | Strong user separation, reimaging |
| Handheld scanners and mobile devices | Loss, weak OS | MDM, remote wipe |
| IoT sensors and cameras | Default passwords, old firmware | Segmentation, firmware updates |
| Admin workstations | High-value target | Privileged access workstations, MFA |

### 8.4 How antivirus detects

| Method | Idea |
|---|---|
| Signature | Hash or byte pattern of known malware |
| Heuristic | Suspicious structure or instructions |
| Behavior | Watches actions such as mass file encryption |
| Sandbox | Run in a safe environment |
| Machine learning | Model trained on malware features |
| Cloud reputation | Check the file against a global database |

### 8.5 Python: file integrity monitoring

```python
import hashlib
from pathlib import Path

def file_hash(path):
    return hashlib.sha256(Path(path).read_bytes()).hexdigest()

def baseline(paths):
    return {p: file_hash(p) for p in paths}

def check(baseline_map):
    for p, old in baseline_map.items():
        new = file_hash(p)
        print(("OK       " if new == old else "CHANGED  ") + p)

# usage example (replace with your own files)
# base = baseline(["/etc/hosts", "/etc/passwd"])
# check(base)
```

---

## 9. Cloud Security

### 9.1 Concept

**Analogy.** Renting a locker in a big shared storage building. The building owner secures the building. You must lock your own locker properly.

### 9.2 Service and deployment models

| Service model | Meaning | Example |
|---|---|---|
| IaaS | Rent virtual machines and networks | Virtual servers |
| PaaS | Rent a platform to run code | Managed database |
| SaaS | Use finished software | Email, HR system |

| Deployment model | Meaning |
|---|---|
| Public cloud | Shared infrastructure from a provider |
| Private cloud | Dedicated to one organization |
| Hybrid cloud | Mix of on-premises and cloud |
| Community cloud | Shared by similar organizations such as government bodies |
| Multi-cloud | More than one provider |

In India, MeitY runs the government cloud initiative (MeghRaj) and empanels cloud providers for government use.

### 9.3 Shared responsibility model

| Layer | IaaS | PaaS | SaaS |
|---|---|---|---|
| Physical data centre | Provider | Provider | Provider |
| Network infrastructure | Provider | Provider | Provider |
| Virtualization | Provider | Provider | Provider |
| Operating system | **Customer** | Provider | Provider |
| Network controls and firewall rules | **Customer** | Shared | Provider |
| Application | **Customer** | **Customer** | Provider |
| Identity and access | **Customer** | **Customer** | **Customer** |
| Data and its classification | **Customer** | **Customer** | **Customer** |

> **Always the customer:** your data, your users, your access settings.

### 9.4 Cloud security controls

| Area | Controls |
|---|---|
| Identity | IAM roles, least privilege, MFA, no shared root accounts, short-lived credentials |
| Data | Encryption at rest and in transit, customer-managed keys (BYOK), KMS, backups, data residency rules |
| Network | Virtual private cloud, security groups, private endpoints, no public databases |
| Workloads | Hardened images, patching, container and Kubernetes security, serverless permission limits |
| Visibility | Central logs, cloud audit trails, alerts |
| Posture | **CSPM** (Cloud Security Posture Management) finds misconfigurations |
| Workload protection | **CWPP** protects VMs and containers |
| Access broker | **CASB** enforces policy between users and cloud apps |
| Unified | **CNAPP** combines CSPM and CWPP |
| Governance | Tagging, cost and policy guardrails, vendor risk review, exit plan |

### 9.5 Top cloud risks

1. Misconfiguration (public buckets, open security groups).
2. Weak identity management and leaked keys.
3. Insecure APIs.
4. Lack of visibility.
5. Data residency and legal issues.
6. Vendor lock-in and outages.
7. Insider threat and account takeover.
8. Shadow IT.

### 9.6 Python: spot risky cloud settings

```python
buckets = [
    {"name": "customer-invoice-scans", "public": True,  "encrypted": False, "logging": False},
    {"name": "internal-reports",       "public": False, "encrypted": True,  "logging": True},
]

def audit(bucket):
    issues = []
    if bucket["public"]:
        issues.append("PUBLIC ACCESS")
    if not bucket["encrypted"]:
        issues.append("NOT ENCRYPTED")
    if not bucket["logging"]:
        issues.append("NO ACCESS LOGGING")
    return issues

for b in buckets:
    print(b["name"], audit(b) or "OK")
```

---

## 10. Wireless Security

| Standard | Notes |
|---|---|
| WEP | Broken. Never use |
| WPA | Uses TKIP. Weak |
| WPA2-Personal (PSK) | AES-CCMP with a shared password. Weak if the password is guessable |
| WPA2-Enterprise | 802.1X with RADIUS, each user has their own login. Stronger |
| WPA3 | SAE handshake resists offline guessing. Stronger |
| Enhanced Open (OWE) | Encrypts guest Wi-Fi without a password |

**Practices.** Separate SSID and VLAN for guests, client isolation, disable WPS, wireless IPS to catch rogue APs, certificate validation on Enterprise Wi-Fi, strong passphrases, change default admin credentials, firmware updates, captive portal for terms and authentication.

---

## 11. Email and DNS Security

### 11.1 Email authentication

| Technique | What it does |
|---|---|
| SPF | A DNS record lists the servers allowed to send mail for a domain |
| DKIM | Mail is digitally signed by the domain, the receiver checks the signature |
| DMARC | Policy that says what to do if SPF or DKIM fail (none, quarantine, reject) and gives reports |
| TLS for mail (STARTTLS) | Encrypts mail between servers |
| S/MIME and PGP | End-to-end encryption and signing |
| Secure email gateway | Filters spam, phishing, malware |
| Sandboxing | Detonates attachments safely |

### 11.2 DNS security

| Risk | Control |
|---|---|
| Cache poisoning | DNSSEC, random source ports |
| DNS tunneling (data hidden in DNS) | DNS monitoring and filtering |
| DNS hijack | Registrar lock, MFA on the registrar account |
| Malicious domains | Protective DNS filtering |
| Privacy | DoT (DNS over TLS), DoH (DNS over HTTPS) |

---

## 12. Zero Trust, NAC, and SASE

* **Zero Trust (NIST SP 800-207).** Never trust, always verify. No automatic trust for being inside the network. Verify user, device health, and context for every request. Apply least privilege and assume breach.
* **NAC (Network Access Control).** Checks a device (identity, patch level) before allowing it onto the network (uses 802.1X).
* **SASE.** Cloud-delivered networking and security together (SD-WAN, secure web gateway, CASB, ZTNA, firewall as a service).
* **Proxy and secure web gateway.** Filters outbound web access.
* **Network Detection and Response (NDR).** Analytics on traffic.

---

## 13. Monitoring and Logging

| Item | Purpose |
|---|---|
| Syslog and flow records (NetFlow, IPFIX) | Evidence of who talked to whom |
| Packet capture (PCAP) | Deep investigation |
| SIEM | Collect, normalize, correlate, alert |
| SOC | People who watch and respond 24x7 |
| NTP time sync | Logs from different devices must align in time |
| Log protection | Send logs off-device, protect from tampering |
| Baselines | Know normal to spot abnormal |

Dashboards are covered in file 05.

---

## 14. Network Hardening Checklist

1. Maintain an up-to-date network diagram and asset inventory.
2. Change all default passwords and disable unused accounts.
3. Disable unused ports and services (Telnet, FTP, SNMPv1 and v2).
4. Use SSH, HTTPS, SNMPv3, and SFTP for management.
5. Patch network devices and firmware.
6. Back up configurations and keep them version-controlled.
7. Enforce MFA for admin and remote access.
8. Segment networks and apply default deny.
9. Log to a central server and review alerts.
10. Restrict management access to a dedicated management VLAN.
11. Review firewall rules on a schedule.
12. Test with vulnerability scans and pen tests.
13. Plan redundancy (dual links, dual firewalls) for availability.
14. Document an incident response and DDoS plan.

---

## 15. Attack to Defense Map

| Attack | Primary defenses |
|---|---|
| Port scanning | Firewall, IDS, close unused ports |
| DoS and DDoS | Rate limiting, scrubbing, CDN, redundancy |
| MitM and sniffing | TLS, VPN, WPA3, DAI |
| ARP spoofing | DAI, static entries, segmentation |
| DNS spoofing | DNSSEC, secure resolvers |
| IP spoofing | Ingress and egress filtering, anti-spoofing rules |
| Rogue AP and evil twin | WIPS, 802.1X, certificate checks |
| Lateral movement | Segmentation, east-west monitoring, least privilege |
| Data exfiltration | Egress filtering, DLP, DNS monitoring |
| Malware | EDR, email filter, patching, application control |
| Brute force on VPN | MFA, lockout, geo restrictions |
| Zero-day | Defense in depth, anomaly detection, virtual patching |

---

## 16. Exam Corner

### One-mark facts

* Firewall = rule-based traffic filter. Best rule base: **default deny**.
* Firewall rules are matched **top to bottom, first match wins**.
* DMZ = zone for public-facing servers between the internet and the internal network.
* VPN = encrypted tunnel across a public network. Types: remote access and site-to-site.
* IPsec modes: transport and tunnel. Components: AH, ESP, IKE.
* IDS detects. IPS detects and blocks and is in line.
* Signature based detects known attacks. Anomaly based can detect unknown ones.
* Symmetric uses one key (AES). Asymmetric uses a key pair (RSA). Hash is one-way (SHA-256).
* Digital signature = hash signed with a private key. Gives integrity, authenticity, non-repudiation.
* A certificate binds a public key to an identity. It is issued by a CA.
* HTTPS = HTTP + TLS. Port 443.
* PFS means old sessions stay safe even if the long-term key leaks.
* Segmentation limits lateral movement. VLAN is logical separation.
* EDR detects and responds on endpoints. XDR spans endpoint, network, cloud.
* Shared responsibility: the provider secures the cloud, the customer secures what they put in it (data, identity).
* WPA3 > WPA2 > WPA > WEP.
* SPF, DKIM, DMARC protect against email spoofing.
* Zero Trust = never trust, always verify.

### Common traps

1. Saying a firewall protects against all attacks. It does not stop allowed-channel attacks.
2. Saying NAT is a firewall. It is not a security control.
3. Saying HTTPS means a site is safe. It only means the connection is encrypted.
4. Saying encryption gives integrity. Use a hash, HMAC, authenticated modes (GCM), or signatures.
5. Mixing up public and private key use (encrypt with the receiver's public key, sign with the sender's private key).
6. Saying IDS blocks traffic.
7. Believing "air-gapped" systems are unreachable. USB drives and vendor laptops can bridge them.

### Self-check (answers below)

1. Which firewall type is aware of application content and user identity?
2. What is the default rule on a secure firewall?
3. Difference between AH and ESP?
4. Which VPN protocol is considered obsolete and weak?
5. What does a false negative mean for an IDS?
6. Which key does a sender use to produce a digital signature?
7. What does PFS protect?
8. In IaaS, who patches the guest operating system?
9. What does DMARC do?
10. Why is segmentation useful against ransomware?

**Answers.** 1 NGFW (and proxy firewalls for content). 2 Deny all not explicitly allowed. 3 AH gives integrity and authentication only. ESP adds encryption. 4 PPTP. 5 A real attack was not detected. 6 The sender's private key. 7 Past sessions when a long-term key is later stolen. 8 The customer. 9 It tells receivers what to do with mail that fails SPF or DKIM and sends reports. 10 It limits lateral movement so malware cannot easily reach all systems.

---

*End of file 03. Continue with 04-data-protection-frameworks.md*
