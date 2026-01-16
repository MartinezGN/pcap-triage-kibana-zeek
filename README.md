# Portfolio — Triage de PCAP (Zeek + NIDS/Snort + Kibana)

Repositorio de práctica orientado a SOC L1: importación de PCAP, acotación de ventana temporal, análisis en Kibana (Zeek + Snort), extracción de IOCs y reporte con evidencia.

## Casos
### Caso 001 — PCAP
- Reporte: `writeups/case-001.md`
- IOCs: `iocs/case-001-iocs.txt`
- Evidencias: `screenshots/`

### Caso 002 — PCAP
- Reporte: `writeups/case-002.md`
- IOCs: `iocs/case-002-iocs.txt`
- Evidencias: `screenshots/`

## Estructura
- `writeups/`: reportes de cada caso
- `iocs/`: indicadores extraídos
- `screenshots/`: capturas de Kibana como evidencia reproducible

## Qué demuestra este repositorio
- Importación de PCAP y acotación correcta de ventana temporal en Kibana.
- Investigación basada en evidencia: navegación por dashboards (DNS/HTTP/Conn/NIDS) y validación cruzada en Discover.
- Identificación de host origen y destinos relevantes (IPs internas/externas) a partir de eventos Zeek y alertas NIDS.
- Extracción y registro de IOCs (dominios, IPs, puertos, URIs y SIDs de reglas) para detección y seguimiento.
- Documentación reproducible: reporte por caso + capturas + lista de IOCs.
