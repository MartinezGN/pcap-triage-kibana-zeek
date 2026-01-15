# Portfolio — Triage de PCAP (Zeek + NIDS/Snort + Kibana)

## Caso 001 — http_gzip.cap
Objetivo: documentar un flujo de triage estilo SOC sobre un PCAP de entrenamiento, pivotando entre logs de Zeek (HTTP/Conn/Files) y alertas NIDS (Snort) en Kibana, con extracción de IOCs y reporte.

Artefacto principal observado:
- HTTP GET `192.168.69.2:34059 → 192.168.69.1:80` a `/test/ethereal.html` (200 OK)

## Estructura
- Evidencias (capturas): `screenshots/`
- Reporte del caso: `writeups/case-001.md`
- IOCs: `iocs/case-001-iocs.txt`

## Qué demuestra este repo
- Importación y acotación correcta de ventana temporal (Kibana)
- Pivoteo de investigación: HTTP → conexiones → contexto NIDS
- Extracción y registro de IOCs
- Redacción de caso con evidencia reproducible
