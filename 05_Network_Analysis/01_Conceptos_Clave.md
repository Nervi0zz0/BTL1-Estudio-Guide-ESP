# Análisis de Red: Conceptos Clave

El **Análisis Forense de Red** o **Análisis de Tráfico de Red (NTA - Network Traffic Analysis)** se centra en la monitorización, captura, almacenamiento y análisis de las comunicaciones de red para detectar y/o investigar incidentes de seguridad, intrusiones, actividad de malware, violaciones de políticas y otros eventos relevantes.

Mientras que el análisis de disco y memoria miran lo que ocurre *dentro* de un sistema, el análisis de red examina *cómo* se comunican los sistemas entre sí y *qué* información intercambian.

## 📦 PCAP: La Evidencia Fundamental

* **¿Qué es?:** Un archivo **PCAP (Packet Capture)** (`.pcap` o el formato más moderno `.pcapng`) es el estándar de facto para guardar paquetes de datos capturados de una red. Contiene una copia de las cabeceras y, generalmente, el contenido (payload) de los paquetes que viajaban por el cable (o aire) en un momento dado.
* **Captura:** Se usan herramientas como `tcpdump` (CLI Linux), `Wireshark` (GUI Win/Lin/Mac) o `tshark` (CLI de Wireshark) para capturar tráfico en vivo y guardarlo en formato PCAP.
* **Análisis:** Se usan herramientas como `Wireshark`, `tshark`, `Zeek` (anteriormente Bro), `Suricata`, `NetworkMiner` para leer y analizar archivos PCAP.
* **En BTL1:** Es muy probable que te proporcionen archivos PCAP relevantes para el escenario del incidente, y debas analizarlos para encontrar respuestas.

## 🌐 Modelo OSI / TCP/IP (Repaso Rápido para Análisis)

Entender las capas de red ayuda a saber dónde buscar qué información:

* **Capa 2 (Enlace - Ej. Ethernet):** Proporciona direcciones físicas **MAC**. Útil para identificar dispositivos específicos en una red local.
* **Capa 3 (Red - Ej. IP):** Direcciones lógicas **IP** (IPv4/IPv6). Fundamental para saber origen y destino de las comunicaciones entre redes.
* **Capa 4 (Transporte - Ej. TCP, UDP):** Gestiona la comunicación extremo a extremo.
    * **TCP:** Orientado a conexión (SYN, ACK, FIN), fiable, con **puertos** para identificar aplicaciones/servicios. Clave para análisis de flujos.
    * **UDP:** Sin conexión, rápido, menos fiable, también usa **puertos**. Común en DNS, DHCP, streaming.
    * **Puertos:** Indican el servicio/aplicación (ej. 80=HTTP, 443=HTTPS, 53=DNS, 22=SSH). Puertos inesperados pueden ser sospechosos.
* **Capa 7 (Aplicación):** Los protocolos que realmente usan las aplicaciones para intercambiar datos (HTTP, DNS, SMB, SMTP, FTP, etc.). Aquí reside el **contenido** real de la comunicación (si no está cifrado).

## 👀 ¿Qué Buscamos en el Tráfico de Red?

Los objetivos del análisis de tráfico de red en un contexto de seguridad suelen incluir:

1.  **Comunicaciones Maliciosas Conocidas:**
    * Conexiones a/desde IPs, dominios o URLs marcadas como maliciosas (C2 de malware, sitios de phishing, etc. - requiere CTI).
2.  **Actividad de Malware:**
    * Beacons de Comando y Control (C2): Conexiones regulares y periódicas a un servidor externo.
    * Descarga de Payloads: Tráfico HTTP/FTP/etc. descargando ejecutables, scripts.
    * Actividad de Propagación: Escaneo de puertos (TCP SYN scans), intentos de conexión a otros sistemas internos (SMB, RDP).
3.  **Exfiltración de Datos:**
    * Transferencias de grandes volúmenes de datos salientes inusuales.
    * Uso de protocolos no estándar o "encubiertos" para sacar datos (DNS tunneling, ICMP tunneling).
4.  **Violaciones de Políticas:**
    * Uso de protocolos no permitidos (P2P, Tor).
    * Acceso a sitios web/categorías prohibidas.
5.  **Anomalías y Comportamientos Sospechosos:**
    * Protocolos en puertos incorrectos (ej. SSH sobre puerto 80).
    * Patrones de tráfico inusuales (picos, conexiones de larga duración extrañas).
    * Errores de conexión excesivos (TCP Resets).
    * Uso de protocolos inseguros (Telnet, FTP en texto plano para credenciales).

## 🔒 Metadatos vs. Contenido Completo y Cifrado

* **Full Packet Capture (PCAP):** Captura todo el paquete, incluyendo la carga útil (payload). Permite ver el contenido exacto de las comunicaciones **si no están cifradas**.
* **Metadatos (NetFlow, Logs de Zeek/Bro):** Resumen las comunicaciones (IPs, puertos, duración, bytes transferidos) pero **no incluyen el payload**. Útil para análisis de alto nivel y detección de patrones, pero limitado para ver el contenido exacto.
* **Cifrado (TLS/SSL):** Gran parte del tráfico web y muchas otras comunicaciones están cifradas hoy en día. Esto significa que, aunque captures el PCAP completo, **no podrás leer el contenido** de la aplicación (ej. la página web HTTPS exacta, los comandos SSH). Sin embargo, **aún puedes ver metadatos importantes**: IPs, puertos, duración, volumen de datos, y a veces información del certificado o el SNI (Server Name Indication) en TLS, que puede revelar el dominio de destino.

## 🎯 Enfoque BTL1

En BTL1, el análisis de red generalmente implica trabajar con **archivos PCAP** usando herramientas como **Wireshark** o **tshark**. Necesitarás saber cómo aplicar filtros, seguir conversaciones (TCP/UDP Streams), identificar protocolos y extraer información relevante (archivos, credenciales en texto plano si las hay) para responder a preguntas sobre las comunicaciones asociadas a un incidente.

---
*Entender estos conceptos te prepara para usar eficazmente las herramientas de análisis y extraer la máxima información posible del tráfico de red.*
