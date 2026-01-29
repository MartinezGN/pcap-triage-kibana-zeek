# Portfolio — PCAP Triage (Zeek + NIDS/Snort + Kibana)

SOC L1-oriented practice repository: PCAP import, time-window scoping, Kibana analysis (Zeek + Snort), IOC extraction, and case reporting with reproducible evidence.

## About This Project

This repository documents my hands-on practice with network security monitoring (NSM) tools as part of my career transition into cybersecurity. Each case represents a complete triage workflow: from PCAP import to IOC extraction and incident documentation.

The goal is to build practical skills that translate directly to SOC Analyst (L1/L2) responsibilities—not just running tools, but understanding *why* certain traffic patterns matter and *how* to communicate findings clearly.

## Lab Environment

I built this lab using VirtualBox with 8GB RAM allocated to the VM.

| Component | Details |
|-----------|---------|
| Virtualization | VirtualBox |
| Primary VM | Security Onion (NSM stack) |
| Additional VMs | REMnux (malware analysis), FLARE VM (Windows forensics) |
| NSM Stack | Zeek + Snort |
| Visualization | Kibana |
| Import Method | `so-import-pcap` |

### How I Import PCAPs

1. Download PCAP from source (Wireshark samples, Malware-Traffic-Analysis.net)
2. Transfer to Security Onion VM
3. Run `sudo so-import-pcap <filename.pcap>`
4. Adjust Kibana time window to match PCAP timestamps
5. Begin triage across dashboards (DNS → HTTP → Conn → NIDS)

## PCAP Sources

| Case | PCAP | Source |
|------|------|--------|
| 001 | `http_gzip.cap` | [Wireshark Sample Captures](https://wiki.wireshark.org/SampleCaptures) |
| 002 | `2025-01-22-traffic-analysis-exercise.pcap` | [Malware-Traffic-Analysis.net](https://www.malware-traffic-analysis.net/2025/01/22/index.html) |

## Cases

### Case 001 — HTTP Training PCAP

- **Report:** `writeups/case-001.md`
- **IOCs:** `iocs/case-001-iocs.txt`
- **Evidence:** `screenshots/`
- **Summary:** Single HTTP GET transaction with gzip-encoded response. Training PCAP for baseline triage practice.
- **Severity:** Low (informational)

### Case 002 — Malware Infection (Fake Google Authenticator)

- **Report:** `writeups/case-002.md`
- **IOCs:** `iocs/case-002-iocs.txt`
- **Evidence:** `screenshots/`
- **Summary:** User clicked malicious ad, downloaded fake software, established C2 communication. Based on Unit42 threat intelligence.
- **Severity:** High (confirmed infection)

## Repository Structure

```
├── writeups/       # Case reports with methodology and findings
├── iocs/           # Extracted indicators per case
└── screenshots/    # Kibana screenshots as reproducible evidence
```

## What This Repository Demonstrates

- **PCAP import and time-window scoping** — Correctly adjusting Kibana time ranges to match imported data.
- **Evidence-based investigation** — Navigating dashboards (DNS/HTTP/Conn/NIDS) and cross-validating in Discover.
- **Host and network identification** — Identifying source/destination hosts and relevant IPs from Zeek events and NIDS alerts.
- **IOC extraction and documentation** — Recording domains, IPs, ports, URIs, and rule SIDs for detection and tracking.
- **Severity assessment** — Differentiating between benign traffic and confirmed incidents.
- **Reproducible documentation** — Case report + screenshots + IOC list for each analysis.

## Methodology

My triage approach for each case:

1. **Scope the time window** — Adjust Kibana to the PCAP's time range to avoid empty results.
2. **Review NIDS alerts first** — Understand the alert surface and prioritize by classification/severity.
3. **Pivot to DNS** — Identify queried domains and correlate with origin hosts.
4. **Review HTTP/Connections** — Validate traffic flows, methods, status codes, and connection states.
5. **Cross-reference threat intel** — Check domains/IPs against known malicious indicators when applicable.
6. **Document and extract IOCs** — Record findings with evidence and actionable indicators.

## Key Takeaways

Through these labs, I learned:

- **Kibana time-window adjustment is critical** — Old PCAPs or imports won't show data if the time range doesn't match. This caused initial "no results" confusion until I understood the issue.
- **Zeek log separation by protocol is powerful** — Having DNS, HTTP, and connection logs separate makes pivoting between data sources intuitive once you understand the structure.
- **NIDS alerts need context** — A single `policy-violation` alert means nothing alone; correlation with DNS/HTTP data reveals whether it's noise or a real incident.
- **Finding Kibana's features took time** — The application has many capabilities that aren't immediately obvious. I spent time exploring filters, saved searches, and dashboard customization to build an efficient workflow.
- **Documentation matters** — Writing clear reports with evidence forced me to understand *why* I reached certain conclusions, not just *what* I found.

## About Me

This repository is part of my career transition into cybersecurity.

**Background:**
- Law degree (Procurador) — Argentina
- CCNA v1.7 certified
- CyberOps Associate certified
- Currently pursuing a Master's in Cybersecurity

I'm building these labs to develop practical SOC skills and demonstrate my ability to analyze network traffic, extract IOCs, and document findings professionally.

---

*Feedback welcome — feel free to open an issue or reach out.*
