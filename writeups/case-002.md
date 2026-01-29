# Case 002 — PCAP Triage (2025-01-22-traffic-analysis-exercise.pcap)

## Summary
Analysis of a training PCAP on an NSM stack (Zeek + NIDS/Snort) visualized in Kibana. The objective is to practice SOC L1 triage: time-window scoping, navigation across DNS/HTTP/Connections/NIDS views, and extraction of IOCs for reproducible documentation.

## Scope and time window
Time window used in Kibana (UTC):  
2025-01-22 00:00:00 — 2025-01-23 00:00:00

## Data sources
- Zeek: `bro_dns`, `bro_http`, `bro_conn`, `bro_files`
- NIDS: Snort (`event_type: alert`)

## Triage approach
1. Confirm the time range indicated by the PCAP and adjust the window to avoid incomplete results.
2. Review NIDS alerts to understand the alert surface and prioritize by severity.
3. Pivot to DNS (Zeek `bro_dns`) to identify queried domains and associate them with origin hosts.
4. Pivot to HTTP (Zeek `bro_http`) and Connections (`bro_conn`) to validate flows, ports, and traffic coherence.
5. Extract evidence and IOCs (IP addresses, domains, rule SIDs) for reporting.

## Key findings

### High-level overview
- Total logs: 484
- Logs by source: DNS (417), HTTP (27), Conn (21), Bro files (15)
- NIDS alerts present in the dataset

### NIDS (Snort)
- Alert SID: 1
- Primary classifications:
  - `bad-unknown` (10)
  - `policy-violation` (30)
- Alerts indicate network events with **low to medium severity**, acting as initial signals pointing to browsing activity.
- Example associated IPs:
  - Host internal: 10.11.17.35
- Domains observed in alerts:
  - `cdn.ads.example.com`
  - `images.example.net`
- Multiple DNS requests and HTTP sessions were registered, suggesting browsing behavior rather than exploit activity.

### DNS (Zeek `bro_dns`)
- Example queried domains:
  - `cdn.ads.example.com`
  - `images.example.net`
- Repeated DNS queries from internal host `10.11.17.35` associated with browsing activity.
- DNS resolution events precede HTTP connections, consistent with normal navigation flow.

### HTTP
- HTTP traffic observed from internal host `10.11.17.35` to external destinations.
- Example HTTP event:
  - `method`: GET
  - `status_code`: 404 (Not Found)
  - `source_ip`: 10.11.17.35
  - `destination_ip`: 52.85.141.213
  - `destination_port`: 80

### Connections
- Services observed across connections: DNS, SSL, HTTP
- Traffic shows consistent behavior with a browsing scenario:
  - DNS resolution
  - HTTP requests
  - Normal connection termination

## Evidence
See `screenshots/`:
- 01-overview-case002.png
- 02-nids-case002.png
- 03-zeek-dns-case002.png
- 04-zeek-http-case002.png
- 05-zeek-http-event-case002.png
- 06-zeek-conn-case002.png
- 07-zeek-files-case002.png

## IOCs
See `iocs/case-002-iocs.txt`

## Conclusion
Severity: Medium (informational / contextual).  
This is a training PCAP with multiple network interactions but no clear evidence of exploitation. NIDS alerts provide context rather than actionable incidents. The internal host `10.11.17.35` exhibits normal browsing behavior, and the observed domains and IPs are consistent with web content delivery. The case demonstrates a complete SOC L1 triage workflow with evidence-backed conclusions.

## Recommended actions
- Identify the endpoint `10.11.17.35` (asset, user, process) and correlate with endpoint telemetry in the same time window.
- Monitor recurrence of domains and IPs (`cdn.ads.example.com`, `images.example.net`) to establish baselines.
- Review NIDS rules and tuning for potential false positives in browsing scenarios.
- If required, pivot to Zeek `bro_files` to validate downloaded content and extract additional IOCs.
