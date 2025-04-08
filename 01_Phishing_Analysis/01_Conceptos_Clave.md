# Análisis de Phishing: Conceptos Clave

El **phishing** es una de las técnicas de ingeniería social más utilizadas por los atacantes para obtener acceso inicial a una red, robar credenciales o distribuir malware. Como analista de seguridad, tu capacidad para identificar y analizar correos electrónicos, URLs y archivos adjuntos sospechosos es una habilidad de primera línea crucial.

El objetivo principal del análisis de phishing es determinar si un correo electrónico es malicioso y, en caso afirmativo, extraer **Indicadores de Compromiso (IoCs)** que puedan usarse para la detección, el bloqueo y la inteligencia de amenazas.

## 🔎 Artefactos Principales a Examinar

Al analizar un correo electrónico sospechoso, nos centraremos en varios artefactos clave:

### 1. Encabezados del Correo (`Headers`)

Los encabezados contienen metadatos vitales sobre el origen y la ruta del correo, a menudo ocultos en la vista estándar del cliente de correo. Son fundamentales para verificar la autenticidad. Elementos clave a revisar:

* **`From:` (Remitente):** Quién dice enviar el correo. Puede ser fácilmente falsificado (`spoofed`).
* **`Reply-To:` (Responder a):** Dirección a la que se enviarán las respuestas. A menudo diferente del `From:` en correos maliciosos.
* **`Return-Path:` (Ruta de Retorno):** Dirección donde rebotan los correos no entregados. También puede indicar el origen real.
* **`Received:` (Recibido):** Serie de entradas que muestran los servidores por los que pasó el correo. Se leen de abajo hacia arriba para trazar la ruta. Útil para identificar el servidor de origen real.
* **`Authentication-Results:` (Resultados de Autenticación):** Muestra los resultados de las comprobaciones de **SPF, DKIM y DMARC**. Son cruciales:
    * **SPF (Sender Policy Framework):** Verifica si la IP del servidor de envío está autorizada por el dominio del remitente. Un `FAIL` es una señal de alerta.
    * **DKIM (DomainKeys Identified Mail):** Añade una firma digital al correo, verificando que el mensaje no ha sido alterado y que proviene del dominio declarado. Un `FAIL` indica posible manipulación o `spoofing`.
    * **DMARC (Domain-based Message Authentication, Reporting & Conformance):** Política que indica qué hacer si SPF o DKIM fallan (ej. `reject`, `quarantine`, `none`). Un `FAIL` aquí es una fuerte indicación de correo ilegítimo.
* **IP de Origen:** A menudo se encuentra en las cabeceras `Received:`. Analizar la reputación de esta IP es vital.

### 2. Cuerpo del Correo (`Body`)

El contenido visible del mensaje. Busca señales de alerta como:

* **Sentido de Urgencia o Amenaza:** "Tu cuenta será bloqueada", "Factura pendiente urgente".
* **Errores Gramaticales o de Ortografía:** Especialmente en correos que simulan ser de organizaciones grandes.
* **Saludos Genéricos:** "Estimado cliente" en lugar de tu nombre.
* **Peticiones Inusuales:** Solicitudes de credenciales, información personal, transferencias bancarias.
* **Estilo Inconsistente:** Tono o formato que no coincide con comunicaciones previas de la entidad suplantada.
* **Enlaces Sospechosos:** Ver siguiente sección.

### 3. Enlaces y URLs

Los enlaces son un componente crítico. No confíes en el texto mostrado del enlace; examina siempre la URL real a la que apunta (pasa el ratón por encima sin hacer clic, o examina el código fuente HTML del correo si es posible):

* **Dominios Engañosos:** Uso de dominios similares al legítimo (ej. `paypaI.com` con 'i' mayúscula en lugar de `paypal.com`), subdominios extraños (`paypal.security-update.com`) o dominios completamente irrelevantes.
* **Acortadores de URL:** Servicios como Bitly, TinyURL pueden ocultar destinos maliciosos. Necesitan ser expandidos y analizados con herramientas de reputación antes de visitarlos.
* **Direcciones IP Directas:** Rara vez un servicio legítimo enlaza directamente a una IP en lugar de un nombre de dominio.
* **"Defanging":** Al compartir o documentar URLs sospechosas, es una buena práctica "desactivarlas" para evitar clics accidentales, reemplazando `http` por `hxxp` y `.` por `[.]` (ej. `hxxp://malicious-site[.]com/login.php`).

### 4. Archivos Adjuntos (`Attachments`)

Los adjuntos son un vector común para entregar malware. Desconfía de:

* **Tipos de Archivo Peligrosos:** `.exe`, `.scr`, `.bat`, `.ps1`, `.js`, `.vbs`. También archivos comprimidos (`.zip`, `.rar`, `.iso`, `.img`) que puedan contenerlos.
* **Documentos con Macros:** Archivos de Office (`.docm`, `.xlsm`, `.pptm`) que solicitan habilitar macros pueden ejecutar código malicioso.
* **Doble Extensión:** Archivos que intentan ocultar su tipo real (ej. `factura.pdf.exe`). Asegúrate de tener visibles las extensiones de archivo en tu sistema operativo.
* **Archivos Protegidos por Contraseña:** A menudo usados para evadir el escaneo automático de los antivirus. La contraseña suele venir en el cuerpo del correo.
* **Necesidad de Análisis:** NUNCA abras un adjunto sospechoso directamente. Usa sandboxes y herramientas de análisis de reputación (basadas en el hash del archivo).

## 💡 Indicadores de Compromiso (IoCs) Clave

Del análisis de estos artefactos, extraeremos IoCs valiosos para la defensa:

* **Direcciones IP:** Del servidor de envío (`Received:` headers), de los dominios enlazados.
* **Dominios y URLs:** Dominios de envío (`From:`, `Return-Path:`), dominios enlazados en el cuerpo, dominios de descarga de malware.
* **Hashes de Archivos:** MD5, SHA1, SHA256 de los adjuntos maliciosos.
* **Direcciones de Correo:** Del remitente (`From:`), `Reply-To`, `Return-Path`.
* **Asuntos (Subjects):** Patrones en las líneas de asunto pueden usarse para crear reglas de detección.

---
*Estos son los elementos fundamentales que buscaremos. Las siguientes s