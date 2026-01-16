# Caso 002 — Triage de PCAP (2025-01-22-traffic-analysis-exercise.pcap)

## Resumen
Análisis de un PCAP de entrenamiento con un stack NSM (Zeek + NIDS/Snort) visualizado en Kibana. El objetivo fue practicar un flujo de triage estilo SOC: acotar la ventana temporal, pivotar entre DNS/HTTP/Connections y revisar alertas NIDS para extraer IOCs y documentar evidencia reproducible.

## Alcance y ventana temporal
Ventana temporal utilizada en Kibana (Absolute): 2025-01-22 00:00:00 → 2025-01-23 00:00:00

## Fuentes de datos
- Zeek: `bro_dns`, `bro_http`, `bro_ssl`, `bro_conn`, `bro_files`
- NIDS: Snort (eventos `event_type: snort`)

## Enfoque de triage
1) Confirmé el rango temporal del import y lo apliqué en Kibana para evitar resultados incompletos.
2) Revisé NIDS para entender volumen de alertas y priorizar pivotes por severidad.
3) Pivoté por DNS (`bro_dns`) para identificar dominios consultados y asociarlos al host origen.
4) Pivoté por HTTP (`bro_http`) y Connections (`bro_conn`) para validar destinos, puertos y coherencia del tráfico.
5) Guardé evidencia y extraje IOCs (IPs, dominios y SIDs de reglas) para el reporte.

## Hallazgos principales
### Visión general
- Total de logs: 3,930
- Logs relevantes: `bro_dns` (817), `bro_http` (627), `bro_ssl` (232), `bro_conn` (859), `bro_files` (915)
- NIDS (Snort) presente en el dataset

### NIDS
- Alertas NIDS: 166
- Clasificaciones principales:
  - `bad-unknown` (136)
  - `policy-violation` (30)
- En Discover (tabla NIDS) se observan eventos con severidad `High` y `Medium`, con `signature_info` apuntando a EmergingThreats.
- IPs que aparecen asociadas a alertas:
  - Host interno: `10.1.17.215`
  - Destino externo: `185.188.32.26:80`
  - Otro destino observado en el caso: `5.252.153.241:80`
- Se registraron SIDs: `2009475`, `2009702`, `2021076`.

### DNS (Zeek `bro_dns`)
- Ejemplo de query observada:
  - `query`: `ping3.dyngate.com`
  - `highest_registered_domain`: `dyngate.com`
- En eventos DNS se ve el host interno `10.1.17.215` asociado a consultas, y el resolver interno `10.1.17.2` como destino de DNS (puerto 53).

### HTTP
- Tráfico HTTP observado desde el host interno `10.1.17.215` hacia IPs externas (ejemplo: `5.252.153.241:80`).
- Evento HTTP:
  - `method`: GET
  - `status_code`: 404 (`Not Found`)
  - `source_ip`: `10.1.17.215`
  - `destination_ip`: `5.252.153.241`
  - `destination_port`: 80

### Connections
- Servicios observados en conexiones: DNS, SSL, HTTP y otros (DCE/RPC, etc.), consistente con un escenario de navegación + resolución DNS y conexiones a destinos externos.

## Evidencia
Ver `screenshots/`:
- 07-overview-caso002.png
- 08-nids-caso002.png
- 09-zeek-dns-caso002.png
- 10-zeek-http-or-ssl-caso002.png
- 11-zeek-connections-caso002.png
- 12-discover-dns-filter-caso002.png
- 13-discover-http-ssl-filter-caso002.png
- 14-nids-alert-list-caso002.png
- (opcional) 15-nids-event-expanded-caso002.png

## IOCs
Ver `iocs/case-002-iocs.txt`.

## Conclusión
**Severidad:** Media en términos de investigación (por volumen de NIDS y destinos externos), pero este es un caso de entrenamiento y no permite atribución sin más contexto. Con la evidencia observada, el host interno `10.1.17.215` es el principal candidato para pivotar, y los dominios/IPs extraídos sirven como base para detecciones y bloqueos.

## Acciones recomendadas
- Identificar el endpoint `10.1.17.215` (usuario/host/proceso) y correlacionar con telemetría local en la misma ventana temporal.
- Pivotar por dominios (`dyngate.com`, `ping3.dyngate.com`) y destinos externos (`185.188.32.26`, `5.252.153.241`) para medir recurrencia, volumen y otros hosts afectados.
- Revisar reglas NIDS por SID (ej. `2009475`, `2009702`, `2021076`) y ajustar supresión/afinamiento si hay falsos positivos.
- Si hay descargas, pivotar por `bro_files` para extraer hashes y enriquecer IOCs.
