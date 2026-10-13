# Lumma Stealer Network Forensics Lab (Wireshark) 🔍🛡️


A technical network artifact investigation based on real-world malware telemetry from **Malware-Traffic-Analysis.net** (`2026-01-31`). This project demonstrates digital forensics and incident response (DFIR) methodologies to triage an emerging infostealer infection, correlate endpoint identity in Active Directory, and extract Command and Control (C2) indicators.


---


## 📌 Incident Background
- **Triggering Alert:** `ET MALWARE Lumma Stealer Victim Fingerprinting Activity`
- **Suspect External Destination:** `153.92.1[.]49` over TCP port `80`
- **Target Network:** `10.1.21[.]0/24` (Active Directory domain: `win11office[.]com`)
- **Domain Controller:** `10.1.21[.]2` (`WIN-LU4L24X3UB7`)


---

## 🎯 Investigation Objectives & Findings

| Item | Forensic Field | Finding | Protocol / Extraction Source |
| :--- | :--- | :--- | :--- |
| **Infected Host IP** | Network Layer | `10.1.21.58` | IP Conversation with C2 |
| **MAC Address** | Data Link Layer | `00:21:5d:c8:0e:f2` | Ethernet II Frame Header |
| **Hostname** | NetBIOS / DHCP | `DESKTOP-ES9F3ML` | NBNS Name Registration / DHCP Option 12 |
| **User Account** | Identity (AD) | `gwyatt` | Kerberos `CNameString` & SAMR DCE/RPC |
| **User Full Name** | Identity (AD) | `Gabriel Wyatt` | SAMR `QueryUserInfo response` (SMB2 Frame 358) |
| **C2 Domain** | Application Layer | `whitepeper[.]su` | HTTP `Host` header over TCP/80 |

---




## 📂 Repository Structure

pcap-lumma-stealer-analysis/
├── README.md               # Project documentation and summary
├── REPORT.md               # 1-page executive & technical incident report
├── filters.txt             # Wireshark filter reference sheet
├── iocs.txt                # Defanged Indicators of Compromise for enrichment
└── screenshots/            # Visual evidence from Wireshark triage
    ├── 01_host_detection_nbns.png
    ├── 02_user_extraction_samr.png
    └── 03_c2_domain_http.png


---

## 🔗 Evidence Source
Case Reference: Malware-Traffic-Analysis.net - 2026-01-31 Exercise

File: 2026-01-31-traffic-analysis-exercise.pcap.zip