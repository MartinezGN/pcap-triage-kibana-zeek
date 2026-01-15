# Case 001 — PCAP Triage in Security Onion (http_gzip.cap)

## Summary
Imported a public training PCAP into Security Onion and performed SOC-style triage in Kibana using Zeek and NIDS data. Observed a single HTTP transaction (GET) returning 200 OK over port 80.

## Scope / Time Range
Kibana absolute time range: 2004-10-29 00:00:00 to 2004-10-30 00:00:00.

## Data Sources
- Zeek: bro_http, bro_conn, bro_files
- NIDS: Snort (1 alert; classification: policy-violation)

## Key Findings
- HTTP (bro_http):
  - Source: 192.168.69.2:34059
  - Destination: 192.168.69.1:80
  - Method/Status: GET / 200 (OK)
  - URI: /test/ethereal.html
  - User-Agent: Firefox/0.10.1 (Linux PPC)
- Connections: 1 HTTP service connection (normal completion).
- NIDS: 1 policy-violation alert (reviewed for context).

## Evidence
See `screenshots/`:
- 01-overview.png
- 02-zeek-http-dashboard.png
- 03-zeek-connections.png
- 04-nids-alerts.png
- 05-discover-bro_http.png
- 06-http-event-expanded.png

## IOCs
See `iocs/case-001-iocs.txt`.

## Conclusion
Training case with limited data. Traffic appears consistent with normal HTTP behavior. This case demonstrates PCAP ingestion, correct time scoping, pivoting across Zeek/NIDS views, and structured reporting.

## Recommended Actions (real environment)
Pivot HTTP → conn/files for additional artifacts, enrich IOCs, and correlate with endpoint telemetry.
