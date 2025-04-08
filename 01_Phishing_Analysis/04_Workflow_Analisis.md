# Workflow Sugerido para Análisis de Phishing

Tener un proceso estructurado ayuda a analizar correos sospechosos de manera eficiente, segura y sin olvidar pasos importantes. Este es un workflow general que puedes adaptar según el caso y las herramientas disponibles.

**¡La Seguridad Primero!** Realiza siempre estos pasos en un entorno controlado (VM de análisis) y evita interactuar directamente con elementos potencialmente maliciosos en tu máquina principal. Si es posible, trabaja con el correo en formato de archivo (`.eml` o `.msg`).

## Pasos del Workflow de Análisis

1.  **Preparación y Seguridad:**
    * Asegúrate de estar en tu entorno de análisis aislado.
    * Ten a mano tus herramientas de análisis (navegador seguro, acceso a servicios online, terminal).
    * Obtén el correo sospechoso como archivo (`.eml`/`.msg`) si es posible, para preservar los encabezados completos.

2.  **Evaluación Inicial (Visual - Sin Clics):**
    * **Remitente:** ¿Reconoces la dirección? ¿El nombre mostrado coincide con la dirección real?
    * **Asunto:** ¿Es genérico, alarmista, inesperado? ¿Contiene errores?
    * **Tono y Contenido:** Lee el cuerpo buscando urgencia, amenazas, mala gramática, saludos genéricos, peticiones extrañas.
    * **Enlaces (Hover):** Pasa el cursor **sin hacer clic** sobre los enlaces. ¿La URL que se muestra en la barra de estado del navegador/cliente de correo coincide con el texto del enlace? ¿Parece sospechosa?

3.  **Análisis de Encabezados (`Headers`):**
    * Extrae los encabezados completos del correo.
    * Pégalos en un analizador de encabezados (MxToolbox, Google Messageheader).
    * **Verifica la Ruta:** Sigue las cabeceras `Received:` de abajo a arriba para identificar la IP de origen real.
    * **Analiza la Reputación IP:** Comprueba la IP de origen en servicios como AbuseIPDB, VirusTotal, OTX.
    * **Revisa Autenticación:** Presta especial atención a los resultados de `SPF`, `DKIM` y `DMARC`. Fallos aquí son fuertes indicadores de `spoofing`.
    * **Compara `From:`, `Reply-To:`, `Return-Path:`:** ¿Son diferentes? ¿Apuntan a dominios sospechosos?

4.  **Análisis Detallado del Cuerpo del Mensaje:**
    * Busca técnicas de ingeniería social específicas (ej. pretexting, baiting).
    * Identifica cualquier solicitud de información sensible (credenciales, datos personales, financieros).
    * Extrae todas las URLs mencionadas para el siguiente paso.

5.  **Análisis de URLs (Modo Seguro):**
    * **Extrae y Unifica:** Lista todas las URLs únicas encontradas en el cuerpo y encabezados.
    * **"Defang":** Conviértelas a formato `hxxp://dominio[.]com` para documentación segura.
    * **Comprueba Reputación:** Busca cada URL/dominio en VirusTotal, URLhaus, OTX.
    * **Expande Acortadores:** Usa un expansor de URLs si encuentras enlaces cortos (bit.ly, etc.) y analiza la URL final.
    * **Previsualiza con Sandboxing:** Envía las URLs sospechosas a URLScan.io para ver una captura de pantalla y análisis de la página de destino sin visitarla directamente. Puedes usar Any.Run o Triage para una interacción más profunda si es necesario.

6.  **Análisis de Adjuntos (Modo Seguro):**
    * **¡NO ABRIR DIRECTAMENTE!**
    * **Calcula Hashes:** Obtén los hashes MD5 y SHA256 del archivo (ver `03_Cheatsheet_Comandos.md`).
    * **Comprueba Reputación del Hash:** Busca los hashes en VirusTotal y OTX. Esto suele ser suficiente para identificar malware conocido.
    * **Identifica Tipo Real (Linux):** Usa el comando `file` para verificar si la extensión coincide con el tipo real de archivo.
    * **Análisis en Sandbox (Si es Necesario):** Si el hash es desconocido o sospechoso, y las políticas lo permiten, sube el archivo a un sandbox (Any.Run, Hybrid Analysis, Triage) para análisis dinámico. **¡Considera la confidencialidad de los datos del archivo!**

7.  **Extracción de IoCs y Conclusión:**
    * **Consolida Hallazgos:** Reúne todos los Indicadores de Compromiso (IoCs) identificados:
        * IPs maliciosas (origen, C&C)
        * Dominios/URLs maliciosas
        * Hashes de archivos maliciosos
        * Direcciones de correo electrónico del atacante (`From`, `Reply-To`)
        * Asuntos o patrones específicos.
    * **Veredicto:** Determina si el correo es `Malicioso`, `Sospechoso`, o `Legítimo`.
    * **Resumen:** Escribe un breve resumen explicando por qué llegaste a esa conclusión, mencionando las pruebas clave.

8.  **Documentación / Escalado:**
    * Documenta tus hallazgos de forma clara y estructurada (¡tus notas son clave aquí!).
    * Sigue los procedimientos de tu organización para reportar el incidente, bloquear los IoCs, o escalar el análisis si es necesario.

---

