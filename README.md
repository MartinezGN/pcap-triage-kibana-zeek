# PCAP Triage Portfolio — Zeek + NIDS (Snort) + Kibana

## Case 001 — http_gzip.cap
Goal: document a fast SOC-style triage workflow on a small training PCAP using Kibana pivots across Zeek logs (HTTP/Conn/Files) and NIDS alerts.

Key artifact from this case:
- HTTP GET `192.168.69.2:34059 → 192.168.69.1:80` to `/test/ethereal.html` (status 200 OK)

Contents
- Evidence (screenshots): `screenshots/`
- Case report: `writeups/case-001.md`
- IOCs: `iocs/case-001-iocs.txt`
