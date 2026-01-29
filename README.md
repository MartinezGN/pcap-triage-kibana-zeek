# Portfolio — PCAP Triage (Zeek + NIDS/Snort + Kibana)

Hands-on repository aimed at SOC L1 practice: PCAP ingestion, narrowing the time window, analysis in Kibana (Zeek + Snort), IOC extraction, and evidence-based reporting.

## Cases

### Case 001 — PCAP
- Report: `writeups/case-001.md`
- IOCs: `iocs/case-001-iocs.txt`
- Evidence: `screenshots/`

### Case 002 — PCAP
- Report: `writeups/case-002.md`
- IOCs: `iocs/case-002-iocs.txt`
- Evidence: `screenshots/`

## Structure
- `writeups/`: per-case reports
- `iocs/`: extracted indicators
- `screenshots/`: Kibana captures as reproducible evidence

## What this repository demonstrates
- PCAP ingestion and correct time-window scoping in Kibana.
- Evidence-based investigation: dashboard navigation (DNS/HTTP/Conn/NIDS) and cross-validation in Discover.
- Identification of source hosts and relevant destinations (internal/external IPs) from Zeek events and NIDS alerts.
- Extraction and logging of IOCs (domains, IPs, ports, URIs, and rule SIDs) for detection and tracking.
- Reproducible documentation: per-case report + screenshots + IOC list.
