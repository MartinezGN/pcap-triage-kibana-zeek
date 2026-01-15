# Case 001 — PCAP Triage (http_gzip.cap)

## Summary
Triage de un PCAP de entrenamiento con Kibana, pivotando Zeek (HTTP/Conn/Files) y revisando alertas NIDS (Snort). El caso contiene una única transacción HTTP (GET) con respuesta 200 OK por puerto 80.

## Scope / Time Range
Kibana: 2004-10-29 00:00:00 → 2004-10-30 00:00:00 (UTC)

## Data Sources
- Zeek: bro_http, bro_conn, bro_files
- NIDS: Snort (1 alert; classification: policy-violation)

## Triage Approach (what I did first)
1) Ajusté el time range al indicado por el import para evitar falsos “no results”.
2) Fui a Zeek HTTP para identificar el/los requests y extraer IOCs base.
3) Pivoté a Connections para validar que el flujo es consistente.
4) Revisé NIDS para ver si el alerta agregaba contexto o cambiaba la severidad.

## Key Findings
- HTTP (bro_http)
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
Por el tamaño del PCAP y la evidencia observada, lo traté como bajo o informativo: no hay indicadores fuertes de comportamiento malicioso, solo una request HTTP con 200 OK. El objetivo del caso es demostrar el flujo de triage, pivots y extracción de IOCs con evidencia reproducible.

## Recommended Actions (real environment)
- Si este patrón aparece repetido (múltiples GET a la misma URI/host), crear un hunt rápido por `uri + source_ip` y medir recurrencia.
- Correlacionar con telemetría de endpoint (procesos/red) en la misma ventana temporal para descartar tooling no autorizado.
