# Análisis de Protocolos Específicos (HTTP, DNS, SMB)

Además de filtrar por la presencia de un protocolo, es crucial entender qué buscar *dentro* de esos protocolos para identificar actividad maliciosa. Aquí nos enfocamos en HTTP, DNS y SMB/SMB2.

## 🌐 HTTP / HTTPS (Tráfico Web)

* **Valor Forense:** El protocolo base de la web. Usado para navegación, descarga de archivos, APIs, y frecuentemente por malware para C2 y descarga de payloads.
* **Campos Clave a Examinar:**
    * **Petición (Request):**
        * `http.request.method`: `GET` (pedir recurso), `POST` (enviar datos), `HEAD`, etc.
        * `http.request.uri`: La ruta específica solicitada en el servidor (ej. `/download/payload.exe`).
        * `http.host`: El nombre de dominio al que se dirige la petición.
        * `http.user_agent`: Identifica el navegador o cliente que realiza la petición (malware a menudo usa UAs personalizados o genéricos).
        * `http.referer`: Página anterior desde la que se llegó (si aplica).
        * `http.cookie`: Cookies enviadas al servidor.
    * **Respuesta (Response):**
        * `http.response.code`: Código de estado (ej. `200` OK, `301`/`302` Redirección, `404` No Encontrado, `500` Error Servidor).
        * `http.content_type`: Tipo de contenido devuelto (ej. `text/html`, `application/json`, `application/octet-stream` para descargas binarias).
        * `http.server`: Información sobre el software del servidor web.
        * `http.set_cookie`: Cookies que el servidor pide al cliente guardar.
* **Qué Buscar (Señales de Alerta):**
    * Peticiones `GET` descargando archivos ejecutables (`.exe`, `.dll`), scripts (`.ps1`, `.js`, `.vbs`), o archivos comprimidos sospechosos (`.zip`, `.rar`, `.iso`).
    * Peticiones `POST` enviando datos a URLs/dominios desconocidos o sospechosos (podría ser exfiltración de datos o check-in de C2). Analiza los datos enviados si no están cifrados.
    * `User-Agent` inusuales, genéricos (ej. `curl`, `wget` en contextos inesperados) o que intentan simular navegadores legítimos pero tienen errores.
    * Patrones de `beaconing` en C2: Peticiones `GET` o `POST` regulares y periódicas a la misma URL/dominio.
    * Redirecciones (`301`/`302`) que lleven a sitios maliciosos.
    * Errores `404` masivos pueden indicar escaneo de directorios web.
* **HTTPS:** Recuerda que el contenido (payload) está cifrado. Sin embargo, aún puedes ver: IPs, puertos, volumen de datos, duración, y a menudo el nombre del dominio de destino a través de la extensión **SNI (Server Name Indication)** en el handshake TLS (filtro: `tls.handshake.extensions_server_name`).
* **Herramientas:** Wireshark (`Follow > HTTP Stream`, `Export Objects > HTTP`), tshark (`-e http...`).

## ❓ DNS (Sistema de Nombres de Dominio)

* **Valor Forense:** Traduce nombres de dominio legibles (ej. `www.google.com`) a direcciones IP. Es fundamental para casi toda comunicación en internet y es **abusado frecuentemente** por malware para C2, DGA y tunneling.
* **Campos Clave a Examinar:**
    * `dns.qry.name`: El nombre de dominio que se está consultando.
    * `dns.qry.type`: El tipo de registro solicitado ( `1`=A (IPv4), `28`=AAAA (IPv6), `5`=CNAME, `15`=MX (Mail), `16`=TXT, `6`=SOA).
    * `dns.resp.name`, `dns.resp.addr`, `dns.resp.ttl`: Nombre, dirección IP y Time-To-Live en la respuesta.
    * `dns.flags.rcode`: Código de respuesta ( `0`=No Error, `3`=NXDomain - Non-Existent Domain).
* **Qué Buscar (Señales de Alerta):**
    * Consultas a dominios conocidos como maliciosos o sospechosos (listas CTI).
    * **Volumen Alto de NXDomain:** Muchas consultas a dominios que no existen puede indicar actividad de **DGA (Domain Generation Algorithms)**, donde el malware genera muchos dominios esperando que uno esté activo para C2.
    * Consultas a dominios muy largos o con apariencia aleatoria (posible DGA).
    * Consultas por tipos de registro inusuales (especialmente `TXT`) que podrían usarse para C2 o exfiltración (**DNS Tunneling**).
    * Patrones de **Fast Flux:** Un dominio que resuelve a múltiples IPs diferentes en cortos períodos de tiempo.
    * Uso de servidores DNS no estándar o sospechosos.
* **Herramientas:** Wireshark (filtros `dns`), tshark (`-e dns...`), Passive DNS.

## 💻 SMB / SMB2 (Server Message Block)

* **Valor Forense:** Protocolo principal de Windows para compartir archivos, impresoras y comunicación entre procesos en una red local. Es legítimo, pero **muy abusado por atacantes** para movimiento lateral, acceso a archivos remotos, ejecución remota y exfiltración interna.
* **Campos Clave a Examinar:**
    * `smb2.cmd` o `smb.cmd`: El comando SMB ejecutado (ej. `CREATE` (Abrir/Crear), `READ`, `WRITE`, `CLOSE`, `TREE_CONNECT` (Conectar a share), `IOCTL`).
    * `smb2.filename` o `smb.file`: Nombre del archivo o directorio al que se accede.
    * `smb2.tree` o `smb.share`: Nombre del recurso compartido al que se conecta (ej. `\\SERVER\IPC$`, `\\SERVER\C$`, `\\SERVER\Share`).
    * `smb2.user` o `smb.user`: Nombre de usuario que realiza la acción (si la sesión está autenticada).
    * `smb2.status` o `smb.nt_status`: Código de estado de la operación (ej. `STATUS_SUCCESS`, `STATUS_LOGON_FAILURE`, `STATUS_ACCESS_DENIED`).
* **Qué Buscar (Señales de Alerta):**
    * Conexiones a recursos compartidos administrativos (`IPC$`, `ADMIN$`, `C$`, etc.) entre estaciones de trabajo o desde servidores inesperados (posible movimiento lateral).
    * Autenticaciones fallidas repetidas (`STATUS_LOGON_FAILURE`) a recursos compartidos.
    * Acceso o modificación de archivos sensibles (ej. `sam`, `system`, `ntds.dit`, scripts de logon).
    * Transferencia de archivos ejecutables (`.exe`, `.dll`, `.ps1`) entre máquinas.
    * Uso de herramientas de ejecución remota como PsExec (a menudo deja rastros como conexiones a `IPC$` y creación/ejecución de un servicio llamado `PSEXESVC` en el objetivo, aunque los nombres pueden cambiar).
    * Comandos `IOCTL` sospechosos, a veces usados para enumeración o explotación.
* **Herramientas:** Wireshark (filtros `smb` o `smb2`), tshark (`-e smb...`), NetworkMiner (puede extraer archivos transferidos por SMB).

---
*Analizar el contenido y patrones dentro de estos protocolos clave a menudo revela la naturaleza exacta de una actividad maliciosa que un simple análisis de IPs y puertos podría pasar por alto.*
