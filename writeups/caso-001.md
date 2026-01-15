# Caso 001 — Triage de PCAP (http_gzip.cap)

## Resumen
Análisis de un PCAP de entrenamiento con un stack NSM (Zeek + NIDS/Snort) visualizado en Kibana. El caso contiene una única transacción HTTP (GET) con respuesta 200 OK por puerto 80.

## Alcance y ventana temporal
Ventana temporal (Kibana, modo Absolute): 2004-10-29 00:00:00 → 2004-10-30 00:00:00 (UTC)

## Fuentes de datos
- Zeek: `bro_http`, `bro_conn`, `bro_files`
- NIDS: Snort (1 alerta; clasificación `policy-violation`)

## Enfoque de triage
1) Ajusté la ventana temporal al rango indicado por el import para evitar falsos “sin resultados”.
2) Revisé Zeek HTTP (`bro_http`) para identificar la transacción y extraer IOCs base.
3) Pivoteé a Zeek Connections (`bro_conn`) para validar coherencia del flujo (origen/destino/servicio/estado).
4) Revisé NIDS (Snort) para ver si la alerta aportaba contexto adicional o modificaba la severidad.

## Hallazgos principales
### HTTP (Zeek `bro_http`)
- `source_ip`: 192.168.69.2
- `source_port`: 34059
- `destination_ip` / `destination_ips`: 192.168.69.1
- `destination_port`: 80
- `method`: GET
- `status_code`: 200 (`status_message`: OK)
- `uri`: /test/ethereal.html
- `useragent`: Mozilla/5.0 (X11; U; Linux ppc; rv:1.7.3) Gecko/20041004 Firefox/0.10.1
- `host`: 56cbaaf2f6f
- `virtual_host`: cerberus

### Conexiones (Zeek `bro_conn`)
- 1 conexión con servicio HTTP, finalización normal (normal completion).

### NIDS (Snort)
- 1 alerta con clasificación `policy-violation` (revisada como contexto).

## Evidencia
Ver `screenshots/`:
- 01-overview.png
- 02-zeek-http-dashboard.png
- 03-zeek-connections.png
- 04-nids-alerts.png
- 05-discover-bro_http.png
- 06-http-event-expanded.png

## IOCs
Ver `iocs/caso-001-iocs.txt`.

## Conclusión
**Severidad:** Baja, informativo. Por el tamaño del PCAP y la evidencia observada, lo traté como tráfico HTTP normal hay solo una sola request con 200 OK. El objetivo del caso es documentar el flujo de triage: ajuste de ventana temporal, pivoteo entre Zeek (HTTP/Conn/Files), revisión de NIDS y extracción de IOCs con evidencia reproducible.

## Acciones recomendadas (en un entorno real)
- Si el patrón se repite (varios GET a la misma URI/host), crear una búsqueda rápida filtrando por `uri` y `source_ip` para medir recurrencia y volumen.
- Correlacionar con telemetría del endpoint (procesos y conexiones de red) en la misma ventana temporal para descartar software no autorizado o actividad automatizada.

