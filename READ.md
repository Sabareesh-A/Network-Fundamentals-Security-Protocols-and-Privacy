# Week 13 – Network Fundamentals, Security Protocols, and Privacy

Concise notes plus interview‑style mini answers for revision.

---

## 1. IP Addressing, Subnetting, CIDR, and MAC

### 1.1 IPv4 and IPv6

- IPv4 uses 32‑bit addresses (about 4.3 billion) in dotted‑decimal form such as 192.168.1.10.[web:2][web:19]  
- IPv6 uses 128‑bit hexadecimal addresses like 2001:db8::1, giving an enormous address space and removing IPv4 exhaustion issues.[web:3][web:13]  
- IPv6 adds features such as simpler headers, better multicast, stateless address autoconfiguration, and native IPsec support.[web:18][web:21]  

**Interview – “Difference between IPv4 and IPv6”**  
IPv4 is a 32‑bit protocol with limited addresses and heavy NAT usage, while IPv6 is 128‑bit with virtually unlimited addresses, cleaner headers, and modern features like autoconfiguration and built‑in IPsec; most networks currently run both in dual‑stack mode.[web:3][web:21]  

---

### 1.2 Subnetting

- Subnetting splits one IP network into smaller sub‑networks by borrowing bits from the host part, improving address usage, performance, and logical separation.[web:44][web:52]  
- A subnet is described by mask or prefix length, for example 255.255.255.0 = /24, which decides network vs host bits.[web:19][web:55]  

#### Example 1 – 192.168.1.0/24 into /26

- /24 has 256 addresses; /26 has 64 addresses per subnet, so 4 subnets.[web:42][web:57]  

Subnets:  

- 192.168.1.0/26 → network 192.168.1.0, broadcast 192.168.1.63, hosts .1–.62.[web:42][web:49]  
- 192.168.1.64/26 → network 192.168.1.64, broadcast 192.168.1.127, hosts .65–.126.[web:42][web:49]  
- 192.168.1.128/26 → network 192.168.1.128, broadcast 192.168.1.191, hosts .129–.190.[web:42][web:49]  
- 192.168.1.192/26 → network 192.168.1.192, broadcast 192.168.1.255, hosts .193–.254.[web:42][web:49]  

#### Example 2 – 10.0.0.0/20

- /20 gives \(2^{12} = 4096\) addresses (4094 usable) covering 10.0.0.0–10.0.15.255.[web:44][web:57]  
- First subnet: network 10.0.0.0, broadcast 10.0.15.255, hosts 10.0.0.1–10.0.15.254.[web:44][web:52]  

---

### 1.3 CIDR (Classless Inter‑Domain Routing)

- CIDR writes networks as address/prefix, for example 192.168.10.0/24, where /24 means first 24 bits are network bits.[web:16][web:108]  
- CIDR supports flexible subnetting and route aggregation (supernetting), reducing global routing table size and giving finer‑grained address allocation.[web:7][web:100]  

**Interview – “Explain CIDR with an example”**  
CIDR represents networks like 172.16.0.0/22, where /22 means 22 bits of network; that block has 1024 addresses from 172.16.0.0 to 172.16.3.255 and can replace four /24 routes, so routing stays simpler and address usage is more efficient.[web:7][web:16]  

---

### 1.4 MAC Addresses and ARP

- A MAC address is a Layer‑2 hardware identifier (commonly 48 bits, e.g., 00:1B:44:11:3A:B7) that switches use to forward frames in a LAN.[web:13][web:19]  
- ARP (Address Resolution Protocol) maps IPv4 addresses to MAC addresses by broadcasting “who has IP X?” and caching the reply in an ARP table.[web:17][web:20]  

#### ARP Spoofing

- ARP spoofing (ARP poisoning) is when an attacker sends forged ARP replies so victims map the attacker’s MAC to a trusted IP such as the default gateway, enabling man‑in‑the‑middle attacks or traffic redirection.[web:51][web:59]  
- Defenses include static ARP entries for critical hosts, Dynamic ARP Inspection on switches, segmentation with VLANs, ARP/IDS monitoring, and encrypting sensitive traffic using HTTPS, SSH, or VPNs.[web:48][web:54][web:56]  

**Interview – “What is ARP spoofing and how do you prevent it?”**  
ARP spoofing is the forgery of ARP replies so that hosts associate the attacker’s MAC with a legitimate IP (like the gateway), letting the attacker intercept or alter LAN traffic; mitigation uses static ARP on key systems, Dynamic ARP Inspection, good segmentation, and encryption so captured traffic is unreadable.[web:48][web:51][web:54]  

---

## 2. Encryption Protocols, IDS, and IPS

### 2.1 SSL/TLS

- SSL/TLS secure application traffic (HTTPS, SMTPS, etc.) by providing encryption, integrity, and server authentication with X.509 certificates.[web:22][web:109]  
- In the TLS handshake, client and server exchange hellos, negotiate cipher suites, validate the server certificate, agree on shared session keys via asymmetric methods such as Diffie‑Hellman, then use symmetric encryption for bulk data.[web:87][web:90][web:95]  

**Interview – “How does TLS work?”**  
TLS runs above TCP and protects a specific connection; during the handshake the server proves its identity with a certificate and both sides agree on a cipher and shared keys, then all application data is encrypted and integrity‑checked with those symmetric keys.[web:81][web:92]  

---

### 2.2 IPsec

- IPsec is a network‑layer security suite that protects IP packets independent of the applications using AH (Authentication Header) and ESP (Encapsulating Security Payload).[web:30][web:39]  
- AH provides integrity and origin authentication; ESP adds encryption plus integrity; IKE negotiates keys and Security Associations between peers.[web:36][web:101][web:105]  
- Transport mode encrypts only the payload and keeps the original IP header; tunnel mode encapsulates the entire original packet in a new encrypted IP header and is widely used for site‑to‑site VPNs.[web:30][web:36][web:105]  

**Interview – “What is IPsec and where is it used?”**  
IPsec is a set of protocols that encrypts and authenticates IP traffic using AH, ESP, and IKE; it works in transport or tunnel mode and is mainly used to build VPNs that secure all traffic between hosts, sites, or remote users and corporate networks.[web:82][web:88][web:93]  

---

### 2.3 TLS vs IPsec

- TLS operates at the transport/application layer and typically protects individual protocols like HTTP or SMTP, while IPsec runs at the network layer and can protect all IP packets between endpoints.[web:81][web:85][web:101]  
- IPsec is common for site‑to‑site and full‑tunnel VPNs where both ends are managed by one organization; TLS is easier for public services and browser‑based “SSL VPNs.”[web:83][web:88][web:96]  

| Aspect | TLS (SSL/TLS) | IPsec |
| --- | --- | --- |
| Layer | Transport / application.[web:81][web:101] | Network (IP).[web:30][web:36] |
| Scope | Specific applications (HTTPS, SMTPS, etc.).[web:83][web:96] | All traffic between peers.[web:82][web:88] |
| Common use | Web and app security, SSL VPN portals.[web:83][web:93] | Site‑to‑site VPNs, branch links, remote‑access tunnels.[web:88][web:93] |

---

### 2.4 IDS and IPS

- An IDS (Intrusion Detection System) monitors traffic or logs and raises alerts on suspicious or malicious activity but does not block it.[web:31][web:86]  
- An IPS (Intrusion Prevention System) sits inline, inspects live traffic, and can block, drop, or modify packets that match attack signatures or anomaly rules.[web:34][web:89][web:107]  
- Both use signature‑based and anomaly‑based detection; many products combine them into IDPS platforms.[web:34][web:86][web:94]  

**Interview – “Difference between IDS and IPS”**  
IDS is passive detection and alerting, usually off a span or tap port, while IPS is inline and actively blocks malicious traffic; IDS is mainly for visibility and investigation, IPS is a prevention control that must be carefully tuned to avoid false‑positive blocking.[web:31][web:86][web:94]  

---

## 3. ISP, VPN, Proxies, and Privacy

### 3.1 ISP

- An ISP assigns IP addresses, provides connectivity, and routes customer traffic to and from the internet.[web:2][web:100]  
- Without extra protection, the ISP can usually see destination IPs, DNS lookups it handles, and traffic volume patterns.[web:102][web:104]  

---

### 3.2 VPN

- A VPN (Virtual Private Network) creates an encrypted tunnel between the user device and a VPN server, so local networks and the ISP see only encrypted data going to the VPN endpoint.[web:75][web:79][web:104]  
- VPNs are used for secure remote work, protection on public Wi‑Fi, hiding real IP/location, and bypassing some censorship or geo‑blocks.[web:66][web:72][web:110]  

---

### 3.3 Proxy Servers

- A proxy server forwards client requests to destination servers and often returns responses, so the destination usually sees the proxy’s IP instead of the client’s.[web:76][web:78]  
- Types include forward proxies used by clients to reach the internet, reverse proxies in front of servers for load balancing and protection, and transparent proxies that intercept traffic for caching or filtering.[web:67][web:70][web:73]  

---

### 3.4 VPN vs Proxy

- Both can mask a user’s IP, but VPNs normally encrypt all traffic at OS level, while most proxies only handle specific application flows and often do not encrypt by default.[web:61][web:62][web:110]  
- VPNs are preferred for security/privacy; proxies are convenient for caching, filtering, simple IP rotation, or load‑balancing use cases.[web:68][web:70][web:78]  

| Aspect | VPN | Proxy |
| --- | --- | --- |
| Scope | Routes most or all device traffic through a tunnel.[web:68][web:75] | Handles configured app or browser traffic.[web:62][web:73] |
| Encryption | Typically encrypts data to VPN server.[web:72][web:79] | Often none; mainly changes IP.[web:61][web:110] |
| Common use | Privacy, secure remote access, safe public Wi‑Fi.[web:66][web:72] | Caching, content filtering, geo‑unblocking, scraping.[web:70][web:78] |

