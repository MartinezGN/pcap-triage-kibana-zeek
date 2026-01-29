# Case 001 — PCAP Triage (http_gzip.cap)

## Summary
Analysis of a training PCAP on an NSM stack (Zeek + NIDS/Snort) visualized in Kibana. The case contains a single HTTP transaction (GET) with a 200 OK response over port 80.

## Scope and time window
Time window: 2004-10-29 00:00:00 — 2004-10-30 00:00:00 (UTC)

## Data sources
- Zeek: `bro_http`, `bro_conn`, `bro_files`
- NIDS: Snort (1 alert; classification `policy-violation`)

## Triage approach
1. Adjust the time window to the range indicated by the import to avoid false “no results” findings.
2. Review Zeek HTTP (`bro_http`) to identify the transaction and extract IOCs if applicable.
3. Pivot to Zeek Connections (`bro_conn`) to validate flow consistency (origin/destination/service/state).
4. Review NIDS (Snort) to see if the alert adds context or changes severity.

## Key findings

### HTTP (Zeek `bro_http`)
- `source_ip`: 192.168.69.2
- `source_port`: 34059
- `destination_ip`: 192.168.69.1
- `destination_port`: 80
- `method`: GET
- `status_code`: 200 (`status_message`: OK)
- `uri`: /test/ethereal.html
- `useragent`: Mozilla/5.0 (X11; U; Linux ppc; rv:1.7.3) Gecko/20041004 Firefox/0.10.1
- `host`: 56c0aaf26f
- `virtual_host`: cerberus

### Connections (Zeek `bro_conn`)
- 1 HTTP connection, normal termination (`normal completion`).

### NIDS (Snort)
- 1 alert with classification `policy-violation` (reviewed as context).

## Evidence
See `screenshots/`:
- 01-overview.png
- 02-zeek-http-dashboard.png
- 03-zeek-connections.png
- 04-nids-alerts.png
- 05-discover-bro_http.png
- 06-http-event-expanded.png

## IOCs
See `iocs/case-001-iocs.txt`

## Conclusion
Severity: Low (informational). Given the PCAP size and the observed evidence, this appears to be normal HTTP traffic, as there is only a single request with a 200 OK response. The goal of the case is to document the triage flow: time-window scoping, pivots between Zeek (HTTP/Conn/Files), Snort review, and IOC extraction with reproducible evidence.

## Recommended actions
- If the pattern repeats (multiple GETs to the same URI/host), create a quick query filtering by `uri` and `source_ip` to measure recurrence and volume.
- Correlate with endpoint telemetry (processes and network connections) in the same time window to rule out unauthorized software or automated activity.
