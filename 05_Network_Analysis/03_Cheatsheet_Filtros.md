# Cheatsheet de Filtros de Visualización (Wireshark / Tshark)

Los **Filtros de Visualización (`Display Filters`)** son la herramienta más potente para analizar archivos PCAP. Se aplican *después* de la captura (en la GUI de Wireshark o con `tshark -Y`) y permiten aislar exactamente los paquetes que te interesan, ocultando el resto.

**Sintaxis Básica:**

* **Comparación:** `==` (igual), `!=` (diferente), `>` (mayor que), `<` (menor que), `>=`, `<=`.
* **Contiene:** `contains` (busca una subcadena, ej. `http.host contains "google"`).
* **Coincide (Regex):** `matches` o `~` (usa expresiones regulares, ej. `frame matches "[Pp][Aa][Ss][Ss][Ww][Oo][Rr][Dd]"`).
* **Operadores Lógicos:** `and` (o `&&`), `or` (o `||`), `not` (o `!`).
* **Campos:** Se refieren a campos específicos del protocolo (ej. `ip.src`, `tcp.port`, `http.request.method`). Puedes explorar los nombres de los campos en el panel "Packet Details" de Wireshark.

A continuación, se presentan filtros útiles agrupados por categoría:

## 🌐 Filtros Basados en IP

| Objetivo                      | Filtro de Ejemplo                      | Notas                                                       |
| :---------------------------- | :------------------------------------- | :---------------------------------------------------------- |
| Tráfico hacia/desde una IP    | `ip.addr == 192.168.1.54`              | Muestra todo el tráfico donde la IP aparece como origen O destino. |
| Tráfico DESDE una IP          | `ip.src == 192.168.1.54`               | Solo paquetes originados en esa IP.                         |
| Tráfico HACIA una IP          | `ip.dst == 10.0.0.10`                  | Solo paquetes destinados a esa IP.                          |
| Tráfico dentro de una Subred  | `ip.addr == 192.168.1.0/24`            | Muestra tráfico hacia/desde cualquier IP en esa subred CIDR. |
| Excluir una IP                | `not (ip.addr == 192.168.1.1)`         | Oculta todo el tráfico relacionado con esa IP.             |

## 端口 Filtros Basados en Puerto / TCP / UDP

| Objetivo                         | Filtro de Ejemplo                      | Notas                                                                   |
| :------------------------------- | :------------------------------------- | :---------------------------------------------------------------------- |
| Tráfico en un puerto específico  | `tcp.port == 80`                       | Muestra tráfico TCP donde el puerto origen O destino es 80 (HTTP).         |
|                                  | `udp.port == 53`                       | Muestra tráfico UDP donde el puerto origen O destino es 53 (DNS).          |
|                                  | `port 443`                           | Más simple, busca el puerto 443 en TCP o UDP (menos preciso que `tcp.port`). |
| Solo tráfico TCP                 | `tcp`                                  |                                                                         |
| Solo tráfico UDP                 | `udp`                                  |                                                                         |
| Paquetes TCP SYN (inicio conex.) | `tcp.flags.syn == 1 and tcp.flags.ack == 0` | Útil para ver intentos de conexión.                                     |
| Paquetes TCP RST (reset conex.)  | `tcp.flags.reset == 1`                 | Puede indicar problemas o escaneos.                                       |
| Paquetes TCP FIN (fin conex.)    | `tcp.flags.fin == 1`                   |                                                                         |
| Tráfico de un Stream TCP         | `tcp.stream eq 5`                      | Muestra solo los paquetes del stream TCP con índice 5 (índice asignado por Wireshark). |

## 📝 Filtros por Protocolo Específico

| Protocolo | Objetivo                    | Filtro de Ejemplo                                 | Notas                                                                |
| :-------- | :-------------------------- | :------------------------------------------------ | :------------------------------------------------------------------- |
| **HTTP** | Todo tráfico HTTP           | `http`                                            |                                                                      |
|           | Solo Peticiones HTTP        | `http.request`                                    |                                                                      |
|           | Solo Respuestas HTTP        | `http.response`                                   |                                                                      |
|           | Peticiones POST             | `http.request.method == "POST"`                   | Útil para buscar envío de datos (credenciales, formularios).         |
|           | Respuestas con Error        | `http.response.code >= 400`                       | Busca errores del lado cliente (4xx) o servidor (5xx).               |
|           | Tráfico a/desde un Host     | `http.host contains "example.com"`                | Busca el nombre del host en la cabecera HTTP Host.                    |
| **DNS** | Todo tráfico DNS            | `dns`                                             |                                                                      |
|           | Solo Consultas DNS          | `dns.flags.response == 0`                         |                                                                      |
|           | Solo Respuestas DNS         | `dns.flags.response == 1`                         |                                                                      |
|           | Consulta por un Nombre      | `dns.qry.name == "malware.com"`                   | Busca consultas para un dominio específico.                           |
|           | Respuesta con una IP        | `dns.resp.addr == 8.8.8.8`                        | Busca respuestas que contengan una IP específica.                   |
| **SMB/SMB2** | Todo tráfico SMB/SMB2    | `smb or smb2`                                     | Protocolo de compartición de archivos de Windows.                    |
|           | Acceso a archivo específico | `smb2.filename contains ".exe"`                   | Busca operaciones SMB2 relacionadas con archivos `.exe`.             |
|           | Comando CREATE              | `smb2.cmd == CREATE`                              | Busca intentos de crear/abrir archivos/directorios.                  |
| **ARP** | Todo tráfico ARP            | `arp`                                             | Resolución de direcciones MAC en red local.                          |
| **ICMP** | Todo tráfico ICMP           | `icmp`                                            | Ping, traceroute, mensajes de error.                               |
| **TLS/SSL**| Todo tráfico TLS/SSL       | `ssl or tls`                                      | Muestra handshakes y tráfico cifrado (sin ver contenido).            |
|           | Handshake Específico (SNI) | `tls.handshake.extensions_server_name contains "site.com"` | Busca el nombre de dominio (SNI) en el handshake TLS (si está presente). |

## 🔍 Filtros por Contenido / Exclusión / Combinados

| Objetivo                      | Filtro de Ejemplo                             | Notas                                                                           |
| :---------------------------- | :-------------------------------------------- | :------------------------------------------------------------------------------ |
| Buscar una Cadena (Payload)   | `frame contains "password"`                   | Busca la cadena "password" (case-insensitive) en **cualquier parte** del paquete. ¡Puede ser lento! |
|                               | `tcp contains "USER"`                         | Busca la cadena "USER" solo en el payload TCP.                                 |
| Excluir Protocolos Comunes    | `not (arp or dns or icmp)`                    | Oculta tráfico ARP, DNS e ICMP para centrarse en otros protocolos.                |
| Excluir Tráfico Local         | `not (ip.addr == 192.168.1.0/24)`             | Oculta tráfico interno si la red local es 192.168.1.0/24.                        |
| Combinar Filtros (Ejemplo)    | `ip.src == 10.0.0.5 and tcp.port == 443`      | Tráfico desde 10.0.0.5 en el puerto TCP 443.                                     |
|                               | `(http.request or dns.qry.name) and ip.dst == 8.8.8.8` | Peticiones HTTP o consultas DNS destinadas a 8.8.8.8.                       |

---
*Dominar los filtros de visualización es la habilidad más importante para analizar PCAPs eficientemente. ¡Experimenta y combina filtros para aislar lo que necesitas!*
