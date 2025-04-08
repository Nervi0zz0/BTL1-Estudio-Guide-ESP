# Workflow Sugerido para Investigación de IoCs con CTI

Una vez que has extraído Indicadores de Compromiso (IoCs) durante tu análisis (ya sea de un correo de phishing, logs de SIEM, análisis forense, etc.), el siguiente paso es enriquecerlos usando herramientas de Inteligencia de Amenazas (CTI) para entender su contexto, riesgo y posibles relaciones.

Este es un workflow general y flexible. A menudo tendrás que "pivotar" de un IoC a otro basándote en los hallazgos. **Documenta siempre tus pasos y resultados.**

##  investigating Dirección IP

1.  **Reputación General:**
    * Busca la IP en **VirusTotal**. Revisa la puntuación de detección, los comentarios de la comunidad y la pestaña `Relations` (dominios que han resuelto a esta IP históricamente - Passive DNS).
    * Busca la IP en **AbuseIPDB**. Revisa las categorías de abuso reportadas, el número de reportes y los comentarios.
    * Busca la IP en **OTX (AlienVault)**. ¿Está asociada a algún "Pulso" (informe)? ¿Hay IoCs o TTPs relacionados?

2.  **Información de Red/Registro (WHOIS):**
    * Realiza una consulta WHOIS para la IP (puedes usar herramientas online o `whois` en Linux). Obtén información sobre el bloque de red al que pertenece (ASN, propietario/ISP, país). ¿El país de origen es esperado o sospechoso?

3.  **Passive DNS (Historial):**
    * Profundiza en los datos de Passive DNS (vía VT u otras herramientas). ¿Qué dominios han apuntado a esta IP recientemente o en el pasado? ¿Alguno de esos dominios parece sospechoso? Esto es clave para encontrar infraestructura relacionada.

4.  **Información de Servicios (Opcional/Avanzado):**
    * Busca la IP en **Shodan / Censys**. ¿Qué puertos tiene abiertos? ¿Qué servicios está ejecutando? ¿Parecen servicios legítimos, mal configurados, o potencialmente maliciosos (ej. un puerto inusual para C2)?

## investigating Nombre de Dominio

1.  **Reputación General:**
    * Busca el dominio en **VirusTotal** (revisa detección, Passive DNS, URLs asociadas, archivos descargados).
    * Busca el dominio en **URLhaus** (si sospechas que distribuye malware).
    * Busca el dominio en **OTX** (pulsos, IoCs relacionados).

2.  **Información de Registro (WHOIS):**
    * Realiza una consulta WHOIS para el dominio. Presta atención a:
        * **Fechas:** ¿Fecha de creación muy reciente? (Sospechoso). ¿Fecha de expiración cercana?
        * **Registrador y Servidores de Nombres (NS):** ¿Son conocidos o sospechosos?
        * **Información del Registrante:** Aunque a menudo está oculta por privacidad, si es visible, ¿parece legítima?

3.  **Resolución DNS (Actual e Histórica - Passive DNS):**
    * ¿A qué direcciones IP resuelve actualmente el dominio? (Usa `nslookup`, `dig` o herramientas online).
    * Investiga esas direcciones IP usando el workflow de IPs.
    * Consulta el historial de Passive DNS (VT, etc.). ¿Ha resuelto a otras IPs en el pasado? ¿Esas IPs son sospechosas?

4.  **Dominios y URLs Relacionadas:**
    * Explora la pestaña `Relations` en VirusTotal. ¿Hay subdominios asociados? ¿URLs específicas bajo ese dominio marcadas como maliciosas?
    * Usa **URLScan.io** con el nombre de dominio para ver si hay escaneos previos o para realizar uno nuevo y analizar la página principal o rutas conocidas.

## investigating Hash de Archivo (MD5, SHA256)

1.  **Reputación Extensiva (VirusTotal es Clave):**
    * Busca el hash en **VirusTotal**. Analiza detalladamente:
        * **Detection Ratio:** ¿Cuántos motores lo detectan? ¿Con qué nombres (malware family)?
        * **First/Last Seen:** ¿Cuándo se vio por primera vez y última vez? ¿Coincide con tu incidente?
        * **Associated Names:** ¿Con qué nombres de archivo se ha visto este hash?
        * **Behavior Tab:** Si hay informes de sandbox, ¡revísalos! Mira los procesos, conexiones de red, archivos creados/modificados, TTPs de MITRE ATT&CK.
        * **Relations Tab:** ¿Dónde se ha visto este hash (URLs de descarga, IPs)? ¿Está relacionado con otros archivos (ej. un dropper y su payload)?
        * **Community Tab:** Comentarios de otros analistas.

2.  **Otras Plataformas:**
    * Busca el hash en **OTX** para encontrar pulsos relacionados.
    * Busca el hash en **Hybrid Analysis** o **Triage** para encontrar informes de sandbox adicionales si no están en VT.

3.  **Análisis en Sandbox Propio (Si es Necesario):**
    * Si el hash es desconocido ("0 detecciones" en VT) pero tienes fuertes sospechas (ej. vino de un correo de phishing confirmado), considera enviarlo a un sandbox (Any.Run, Triage, Hybrid Analysis, o tu propio Cuckoo/entorno local) para análisis dinámico, **siempre siguiendo las políticas de tu organización y consideraciones de confidencialidad.**

## investigating URL Completa

1.  **Reputación de Componentes:**
    * Extrae el **nombre de dominio** de la URL y analízalo usando el workflow de dominios.
    * Averigua a qué **dirección IP** resuelve el dominio y analízala usando el workflow de IPs.
    * Busca la **URL completa** en **VirusTotal**, **URLhaus** y **OTX**.

2.  **Análisis Seguro con URLScan.io:**
    * Envía la URL completa a **URLScan.io**. Examina los resultados:
        * Captura de pantalla (¿es una página de login falsa? ¿un exploit kit?).
        * Redirecciones (¿la URL final es diferente?).
        * IPs y Dominios contactados durante la carga de la página (¡pueden ser IoCs adicionales!).
        * Hashes de archivos descargados por la página.

3.  **Expansión de Acortadores:**
    * Si la URL original es acortada (ej. bit.ly), usa un expansor de URLs para obtener la URL final **antes** de realizar los pasos anteriores.

##  pivotamiento Pivotar y Documentar

* **Pivotar:** La clave es usar la información encontrada sobre un IoC para descubrir otros. Si una IP maliciosa alojó 5 dominios, investiga los 5. Si un hash malicioso se descarga desde una URL específica, investiga el dominio y la IP de esa URL.
* **Documentar:** Mantén un registro claro de qué IoCs has investigado, qué herramientas has usado, qué resultados has obtenido y cómo se relacionan los IoCs entre sí. Esto es esencial para tu análisis final y el informe.

---
