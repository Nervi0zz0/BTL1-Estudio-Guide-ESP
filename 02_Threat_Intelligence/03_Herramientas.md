# Herramientas Clave para Inteligencia de Amenazas (CTI)

La Inteligencia de Amenazas se apoya fuertemente en herramientas que permiten recopilar, agregar y analizar datos sobre IoCs (Indicadores de Compromiso) y TTPs (Tácticas, Técnicas y Procedimientos). Aquí listamos algunas herramientas esenciales, muchas de ellas accesibles públicamente:

## 📊 Plataformas de Reputación y Contexto de IoCs

Estas plataformas son el punto de partida para investigar IPs, dominios, hashes o URLs.

* **[VirusTotal (VT)](https://www.virustotal.com/)**:
    * **Uso CTI:** Imprescindible. No solo te dice si algo es "malicioso", sino que proporciona **contexto crucial**:
        * **Relations Tab:** Muestra IPs que resuelven un dominio, dominios que resuelven a una IP, archivos descargados desde una URL, etc. Clave para pivotar y encontrar infraestructura relacionada.
        * **Behavior Tab:** Si es un archivo, muestra análisis de sandbox (si se ejecutó) con comportamiento y TTPs mapeadas a MITRE ATT&CK.
        * **Community Tab:** Comentarios y análisis de otros investigadores.
        * **Passive DNS:** Historial de resoluciones DNS (ver sección DNS).
        * **VT Graph (Avanzado):** Permite visualizar relaciones complejas entre indicadores.

* **[AbuseIPDB](https://www.abuseipdb.com/)**:
    * **Uso CTI:** Específico para **reputación de IPs**. Muestra si una IP ha sido reportada por actividades maliciosas, por quién, cuándo y en qué categoría (ej. `SSH`, `Scanning`, `Phishing`). Los comentarios pueden dar contexto adicional.

* **[URLhaus (abuse.ch)](https://urlhaus.abuse.ch/)**:
    * **Uso CTI:** Base de datos de **URLs asociadas a distribución de malware**. Permite buscar URLs y obtener información sobre el malware relacionado (si se conoce), estado (online/offline) y tags asociados.

* **[OTX (AlienVault Open Threat Exchange)](https://otx.alienvault.com/)**:
    * **Uso CTI:** Plataforma colaborativa. Busca cualquier IoC (IP, dominio, hash, URL, CVE) y encuentra "Pulsos" (informes/colecciones de IoCs) relacionados creados por la comunidad. A menudo incluye **TTPs asociadas (MITRE ATT&CK)**, malware relacionado y otros IoCs vinculados.

* **[URLScan.io](https://urlscan.io/)**:
    * **Uso CTI:** Analiza URLs de forma segura. Para CTI es útil porque revela:
        * **Infraestructura:** IP final, dominios contactados por la página, certificados TLS.
        * **Tecnologías:** Frameworks, librerías JS usadas (pueden indicar kits de phishing).
        * **IoCs:** IPs, dominios, hashes de archivos descargados.
        * **Captura de Pantalla:** Permite ver el contenido sin riesgo.

## 🌐 Herramientas de Análisis de Dominios e IPs

Permiten obtener información sobre el registro y la infraestructura de red.

* **Consultas WHOIS:**
    * **Uso CTI:** Obtener información sobre el **registro de un dominio** (registrante, fechas de creación/expiración, servidores de nombres). Útil para identificar dominios registrados recientemente o de forma sospechosa.
    * **Herramientas:** Múltiples sitios web (`whois.domaintools.com`, `who.is`, etc.) o el comando `whois` en Linux.
    * **Nota:** La privacidad (WHOIS privacy/proxy) a menudo oculta los datos del registrante real.

* **Consultas DNS y Passive DNS:**
    * **Uso CTI:** Investigar registros DNS (A, MX, NS, TXT) de un dominio. **Passive DNS (PDNS)** es crucial: permite ver el **historial** de qué IPs ha resuelto un dominio en el pasado, o qué dominios han resuelto a una IP. Clave para descubrir infraestructura relacionada que ya no está activa o que se usa intermitentemente.
    * **Herramientas:**
        * Comando `nslookup` (Windows/Linux) o `dig` (Linux) para consultas DNS directas.
        * Servicios online (ej. Google Public DNS, Cloudflare DNS).
        * Bases de datos PDNS: **VirusTotal** (pestaña `Relations`), RiskIQ (requiere cuenta), CIRCL Luxembourg PDNS, etc.

* **Motores de Búsqueda de Dispositivos (Shodan, Censys, Zoomeye):**
    * **[Shodan](https://www.shodan.io/)**, **[Censys](https://search.censys.io/)**, **[ZoomEye](https://www.zoomeye.org/)**:
    * **Uso CTI:** Buscan dispositivos conectados a internet por IP, puerto, servicio, banner, etc. Útiles para obtener información sobre una **dirección IP sospechosa**: ¿Qué puertos tiene abiertos? ¿Qué servicios corre? ¿Hay banners que revelen software específico (potencialmente vulnerable)? ¿Está asociada a algún servicio conocido (VPN, Tor exit node)?
    * **Nota:** Requieren interpretación cuidadosa. La versión gratuita suele ser limitada.

## 📈 Plataformas de Intercambio de Inteligencia (Threat Sharing)

* **[MISP (Malware Information Sharing Platform & Threat Sharing)](https://www.misp-project.org/)**:
    * **Uso CTI:** Es un **estándar y software open-source** para almacenar, compartir, correlacionar y colaborar en inteligencia de amenazas (eventos, IoCs, TTPs). Es muy usado en equipos profesionales (CERTs, ISACs, empresas).
    * **Relevancia BTL1:** Probablemente no usarás MISP directamente en el examen, pero es importante conocer el **concepto** de plataformas estructuradas para compartir CTI.

## 📰 Fuentes OSINT (Open Source Intelligence)

* **Blogs de Seguridad, Noticias, Twitter:** (Mencionados en Recursos de Phishing) Son vitales para obtener información sobre **amenazas emergentes**, nuevas campañas, TTPs observadas y análisis de malware que pueden proporcionar IoCs frescos y contexto. Sigue a investigadores y empresas de seguridad relevantes.

---
