# Herramientas Esenciales para el Análisis de Phishing

Analizar correos de phishing requiere un conjunto de herramientas que nos permitan investigar URLs, archivos, IPs y dominios sin exponernos directamente a riesgos. A continuación, se listan algunas de las más útiles, agrupadas por categoría:

## 🌐 Servicios de Reputación (URL, IP, Dominio, Hash)

Estos servicios mantienen bases de datos masivas sobre indicadores maliciosos conocidos.

* **[VirusTotal](https://www.virustotal.com/)**:
    * **Uso:** Esencial. Permite analizar URLs, IPs, dominios y hashes de archivos (MD5, SHA256). Cruza la información con decenas de motores antivirus y listas negras. Busca relaciones entre indicadores.
    * **¡Importante!**: Ten cuidado con la confidencialidad al subir archivos.

* **[URLhaus](https://urlhaus.abuse.ch/)**:
    * **Uso:** Base de datos específica de URLs asociadas con la distribución de malware. Útil para verificar enlaces sospechosos y obtener contexto adicional (malware asociado, tags). Permite reportar URLs maliciosas.

* **[AbuseIPDB](https://www.abuseipdb.com/)**:
    * **Uso:** Centrado en la reputación de direcciones IP. Informa si una IP ha sido reportada por actividades maliciosas (spam, C&C, ataques, etc.). Muy útil para analizar IPs de origen de correos o IPs a las que resuelven dominios sospechosos.

* **[OTX (AlienVault Open Threat Exchange)](https://otx.alienvault.com/)**:
    * **Uso:** Plataforma de inteligencia de amenazas abierta. Busca IPs, dominios, URLs, hashes y obtén "pulsos" (informes de inteligencia) relacionados, incluyendo IoCs, TTPs (MITRE ATT&CK) y malware asociado.

## 샌드박스 Sandboxes (Análisis Dinámico Controlado)

Permiten ejecutar archivos o URLs en un entorno seguro y aislado para observar su comportamiento real.

* **[Any.Run](https://any.run/)**:
    * **Uso:** Sandbox interactivo (la versión gratuita permite cierta interacción). Permite ver en tiempo real cómo se comporta un archivo o URL en una VM Windows, incluyendo procesos creados, conexiones de red, modificaciones al sistema. Muy visual.

* **[Hybrid Analysis](https://www.hybrid-analysis.com/)**:
    * **Uso:** Sandbox gratuito que combina análisis estático y dinámico. Proporciona informes detallados con indicadores MITRE ATT&CK, capturas de pantalla, tráfico de red (PCAP) y comparación con una base de datos de malware conocido.

* **[Triage](https://tria.ge/)**:
    * **Uso:** Otro sandbox potente, con una versión gratuita generosa. Permite análisis en Windows y Linux, extracción de IoCs y mapeo con MITRE ATT&CK.

* **[Joe Sandbox Cloud](https://www.joesandbox.com/)**:
    * **Uso:** Sandbox muy avanzado, a menudo de pago, pero a veces ofrece análisis gratuitos limitados. Conocido por sus capacidades de detección profunda y evasión de técnicas anti-sandbox.

## ✉️ Análisis de Encabezados (Headers)

Herramientas para visualizar y analizar las cabeceras de correo de forma más legible.

* **[MxToolbox Email Header Analyzer](https://mxtoolbox.com/EmailHeaders.aspx)**:
    * **Uso:** Pega el encabezado completo del correo y la herramienta lo parsea, mostrando la ruta de entrega y otra información de forma más clara. Incluye análisis de SPF/DKIM/DMARC.

* **[Google Admin Toolbox - Messageheader](https://toolbox.googleapps.com/apps/messageheader/)**:
    * **Uso:** Similar al anterior, específico de Google pero funciona con cualquier encabezado. Analiza y muestra claramente la ruta, los retrasos y los resultados de autenticación (SPF, DKIM, DMARC).

## 🔗 Análisis y Expansión de URLs

Herramientas para investigar URLs de forma segura.

* **[URLScan.io](https://urlscan.io/)**:
    * **Uso:** Escanea una URL y proporciona una captura de pantalla de la página, información sobre las tecnologías que utiliza, IPs a las que resuelve, dominios contactados, IoCs encontrados y más. Muy útil para ver qué hay detrás de un enlace sin visitarlo tú mismo.

* **Expandidores de URLs:** (Busca "URL expander" online)
    * **Uso:** Para URLs acortadas (Bitly, TinyURL). Pega la URL corta y te muestra la URL final a la que redirige, permitiéndote analizarla antes de hacer clic.

* **Navegación Segura (Sandboxed Browsers):**
    * **Concepto:** Herramientas como Browserling o entornos de máquinas virtuales propios permiten abrir enlaces en un navegador aislado de tu sistema principal.

## 🛠️ Otras Herramientas Útiles

* **[PhishTool](https://www.phishtool.com/)**:
    * **Uso:** Plataforma específica para análisis de phishing que integra muchas de las funcionalidades anteriores (análisis de headers, URLs, archivos) y ayuda a automatizar el proceso. Tiene planes gratuitos y de pago.

---

**⚠️ ¡Nota de Seguridad Importante!**

* **NUNCA** hagas clic en enlaces ni abras adjuntos directamente desde un correo sospechoso en tu máquina principal.
* Utiliza **siempre** máquinas virtuales aisladas o servicios de sandbox online para cualquier análisis dinámico.
* Sé consciente de la **confidencialidad** al subir archivos o correos completos a servicios públicos online como VirusTotal. Si trabajas con información sensible, considera usar herramientas offline o sandboxes privadas.