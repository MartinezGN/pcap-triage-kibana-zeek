# Case 002 — PCAP Triage (2025-01-22-traffic-analysis-exercise.pcap)

## Summary

Analysis of a malware traffic PCAP on an NSM stack (Zeek + NIDS/Snort) visualized in Kibana. The scenario simulates a SOC alert: a user downloaded a suspicious file after searching for "Google Authenticator" and clicking on a malicious ad. The PCAP contains traffic from a confirmed infection.

> **Note:** This PCAP is from [Malware-Traffic-Analysis.net](https://www.malware-traffic-analysis.net/2025/01/22/index.html) (2025-01-22 exercise). The scenario is based on real-world threat intelligence from Unit42.

## Scenario Background

- A user searched for "Google Authenticator" and clicked on a malicious ad
- The ad led to a fake software download page
- The user downloaded and executed malware
- C2 (Command & Control) communication was established

## Scope and Time Window

- **Time window (UTC):** 2025-01-22 16:44:00 — 2025-01-22 17:40:00
- **LAN segment:** 10.1.17.0/24
- **Domain:** bluemoontuesday.com
- **AD Domain Controller:** 10.1.17.2 (WIN-GSH54QLW48D)

## Data Sources

- **Zeek:** `bro_dns`, `bro_http`, `bro_conn`, `bro_files`
- **NIDS:** Snort (`event_type: alert`)

## Triage Approach

1. Confirm the time range indicated by the PCAP and adjust the window to avoid incomplete results.
2. Review NIDS alerts to understand the alert surface and prioritize by severity.
3. Pivot to DNS (Zeek `bro_dns`) to identify queried domains and associate them with origin hosts.
4. Pivot to HTTP (Zeek `bro_http`) and Connections (`bro_conn`) to validate flows, ports, and traffic coherence.
5. Extract evidence and IOCs (IP addresses, domains, rule SIDs) for reporting.

## Key Findings

### High-Level Overview

| Metric | Value |
|--------|-------|
| Total logs | 484 |
| DNS logs | 417 |
| HTTP logs | 27 |
| Conn logs | 21 |
| File logs | 15 |
| NIDS alerts | 166 |

### Infected Host (Victim)

| Field | Value |
|-------|-------|
| IP Address | 10.1.17.215 |
| Network | 10.1.17.0/24 (internal LAN) |
| Gateway | 10.1.17.1 |
| DNS Server | 10.1.17.2:53 |

### NIDS (Snort)

- **Total alerts:** 166
- **Classifications:** `bad-unknown`, `policy-violation`
- **Observed SIDs:**
  - `sid:2009475`
  - `sid:2009702`
  - `sid:2021076`
- Alerts indicate suspicious outbound connections consistent with C2 activity.

### DNS (Zeek `bro_dns`)

- **Suspicious domains observed:**
  - `dyngate.com`
  - `ping3.dyngate.com`
- DNS queries from infected host `10.1.17.215` resolve to external IPs associated with C2 infrastructure.

### HTTP

- HTTP traffic observed from infected host to external C2 servers.
- Connections to:
  - `185.188.32.26:80`
  - `5.252.153.241:80`

### Connections

- Services observed: DNS, SSL, HTTP
- Traffic pattern consistent with:
  1. Initial DNS resolution of malicious domain
  2. HTTP download of malware payload
  3. Persistent C2 communication post-infection

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

**Severity:** High (confirmed infection).

This PCAP captures a complete infection chain: malicious ad → fake software page → malware download → C2 communication. The infected host `10.1.17.215` shows clear indicators of compromise including DNS queries to known malicious domains (`dyngate.com`) and HTTP connections to external C2 servers.

This is not benign browsing activity—it represents a confirmed security incident requiring immediate response.

## Recommended Actions

1. **Containment:** Isolate host `10.1.17.215` from the network immediately.
2. **Identification:** Determine the user account and installed software on the affected machine.
3. **Eradication:** Perform malware removal and forensic analysis of the endpoint.
4. **Detection:** Add IOCs (domains, IPs, SIDs) to SIEM/EDR for detection of similar activity.
5. **Prevention:** Block domains `dyngate.com`, `ping3.dyngate.com` and IPs `185.188.32.26`, `5.252.153.241` at perimeter firewall.

## Lessons Learned

- Malicious ads (malvertising) remain an effective infection vector, even for users searching for legitimate software.
- NIDS alerts with classifications like `bad-unknown` warrant immediate investigation, especially when correlated with DNS queries to unfamiliar domains.
- Cross-referencing threat intelligence (Unit42, VirusTotal) during triage accelerates IOC validation and severity assessment.

## References

- [Malware-Traffic-Analysis.net - 2025-01-22 Exercise](https://www.malware-traffic-analysis.net/2025/01/22/index.html)
- [Unit42 LinkedIn Post](https://www.linkedin.com/posts/unit42_2025-01-22-wednesday-a-malicious-ad-led-activity-7288213662329192450-ky3V/)
- [Unit42 X Post](https://x.com/Unit42_Intel/status/1882448037030584611)
