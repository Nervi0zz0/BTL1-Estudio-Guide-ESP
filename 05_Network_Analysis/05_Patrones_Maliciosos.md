# Reconocimiento de Patrones de Tráfico Malicioso

Más allá de analizar paquetes o protocolos individuales, reconocer **patrones** en el tráfico de red a lo largo del tiempo o entre múltiples conexiones es crucial para detectar actividades maliciosas que podrían pasar desapercibidas.

Estos patrones a menudo se corresponden con diferentes fases de un ciberataque (C2, movimiento lateral, exfiltración, etc.).

## 📡 Comando y Control (C2 / C&C)

Una vez que un sistema está comprometido, el malware necesita comunicarse con el atacante para recibir órdenes y/o enviar datos. Este tráfico C2 a menudo sigue patrones característicos:

* **Beaconing / Heartbeat:**
    * **Descripción:** Conexiones **regulares y periódicas** desde el host infectado hacia un servidor C2 externo. Pueden ser cada pocos segundos, minutos u horas.
    * **Qué Buscar:** Tráfico saliente repetitivo hacia la misma IP/dominio con intervalos de tiempo (delta time) consistentes. Los tamaños de los paquetes pueden ser pequeños y similares (para check-ins) o variar si se descargan comandos/envían datos. Presta atención a conexiones fuera del horario laboral o a destinos geográficamente inusuales. Protocolos comunes: HTTP/HTTPS, DNS, ICMP, o TCP/UDP personalizados.
* **Protocolos Inusuales o Encubiertos:**
    * **Descripción:** Uso de protocolos no estándar o puertos no estándar para la comunicación C2 para evadir la detección.
    * **Qué Buscar:** Tráfico en puertos comunes (80, 443, 53) que no se ajusta al protocolo esperado (ej. tráfico no-HTTP en puerto 80, tráfico no-DNS en puerto 53). Uso de puertos altos o aleatorios para comunicaciones persistentes. Tráfico con payloads que parecen ofuscados o cifrados (alta entropía) sin ser TLS/SSH estándar.
* **DNS para C2:**
    * **Descripción:** Uso de consultas/respuestas DNS para enviar y recibir comandos/datos. Incluye DGA y DNS Tunneling.
    * **Qué Buscar:** (Ver sección DNS) Alto volumen de NXDomain, dominios aleatorios/largos, uso intensivo de registros TXT o subdominios largos.

##  quét Escaneo de Red (`Scanning`)

Los atacantes (o malware como worms) escanean redes para encontrar otros sistemas vulnerables.

* **Escaneo Horizontal:**
    * **Descripción:** Un host intenta conectarse a **muchos hosts diferentes** en la red, probando el **mismo puerto** o un pequeño conjunto de puertos.
    * **Qué Buscar:** Desde una única IP origen, un gran número de intentos de conexión (paquetes TCP SYN sin respuesta SYN-ACK, o seguidos de RST) a *múltiples IPs destino* en la misma subred o rango, todos dirigidos al mismo puerto destino (ej. 445 - SMB, 3389 - RDP, 22 - SSH).
* **Escaneo Vertical:**
    * **Descripción:** Un host intenta conectarse a **muchos puertos diferentes** en un **único host destino**.
    * **Qué Buscar:** Desde una única IP origen, un gran número de intentos de conexión a una *única IP destino*, pero con *diferentes puertos destino*.

## ↔️ Movimiento Lateral

Una vez dentro, los atacantes intentan moverse a otros sistemas en la red interna.

* **Qué Buscar:**
    * Conexiones **SMB/RPC** inusuales: Entre estaciones de trabajo, desde servidores inesperados a estaciones, o dirigidas a recursos compartidos administrativos (`ADMIN$`, `C$`, `IPC$`).
    * Conexiones **RDP (puerto 3389)** entre sistemas donde no es habitual.
    * Conexiones **SSH (puerto 22)** entre sistemas internos si no es una práctica estándar.
    * Tráfico asociado a herramientas de ejecución remota como **PsExec**, WinRM, WMI. (PsExec a menudo usa SMB y crea/inicia un servicio llamado `PSEXESVC` en el destino).
    * Intentos de autenticación fallidos seguidos de éxito entre máquinas internas.

## 📤 Exfiltración de Datos

El objetivo final suele ser robar información sensible.

* **Qué Buscar:**
    * **Grandes Transferencias Salientes:** Volúmenes inusualmente grandes de datos enviados desde la red interna hacia destinos externos, especialmente si ocurren fuera de horas o hacia IPs/dominios sospechosos. Analiza la duración y el total de bytes de las conversaciones (`Statistics > Conversations`).
    * **Protocolos Inusuales para Transferencias:** Uso de **DNS Tunneling** (muchos datos en consultas/respuestas TXT/subdominios), **ICMP Tunneling** (datos en payloads ICMP), FTP, o protocolos personalizados para sacar información.
    * **Transferencias a Servicios Cloud/Públicos:** Subidas masivas a servicios de almacenamiento cloud (Mega, Dropbox, etc.) o pastebins si no es una actividad normal.
    * **Compresión/Cifrado:** El tráfico de exfiltración suele estar comprimido (`.zip`, `.rar`) y/o cifrado. Busca conexiones TLS a destinos sospechosos con grandes transferencias de datos.

## 🦠 Descarga de Malware / Componentes Adicionales

* **Qué Buscar:**
    * Conexiones HTTP/FTP/etc. que descargan archivos ejecutables, DLLs, scripts, o archivos comprimidos sospechosos desde URLs/IPs conocidas por alojar malware (CTI).
    * Patrón de "Staged Payload": El malware inicial (dropper) descarga componentes adicionales desde diferentes ubicaciones después de la infección inicial.

## 🌊 Denegación de Servicio (DoS/DDoS) - Menos Común en Análisis Forense Post-Mortem

* **Descripción:** Inundar un objetivo con tráfico para agotar sus recursos.
* **Qué Buscar:** Volumen masivo de paquetes (SYN, UDP, ICMP) desde una o muchas fuentes hacia un único destino. A menudo con IPs origen falsificadas (spoofed).

---
*Reconocer estos patrones requiere observar el tráfico en contexto, a menudo utilizando las funciones de estadísticas y filtros temporales de Wireshark/tshark, o correlacionando eventos en un SIEM o IDS.*
