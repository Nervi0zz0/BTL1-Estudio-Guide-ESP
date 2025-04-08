# Forense Digital: Reconstruyendo el Incidente

La **Forense Digital** es la rama de la ciencia forense que se ocupa de la recuperación, investigación, examen y análisis de material encontrado en dispositivos digitales, a menudo en relación con incidentes de seguridad o delitos informáticos. El objetivo principal es reconstruir los eventos basándose en la evidencia digital para entender **qué ocurrió, cómo, cuándo, dónde** y, potencialmente, **quién** estuvo involucrado.

En el contexto de la ciberseguridad defensiva (Blue Team) y BTL1, la forense digital es esencial para investigar sistemas comprometidos, analizar malware, entender las acciones de un atacante post-intrusión y recuperar datos relevantes.

## 📜 Principios Fundamentales de la Forense Digital

Aunque BTL1 se enfoca en la aplicación práctica dentro de escenarios, es vital entender los principios que guían cualquier investigación forense para asegurar la integridad y fiabilidad de los resultados:

1.  **Preservación de la Evidencia:**
    * **Objetivo:** Minimizar la alteración de la evidencia original. Siempre que sea posible, se trabaja sobre una **copia bit a bit (imagen forense)** del medio original, no sobre el original directamente.
    * **Acciones:** Usar bloqueadores de escritura (hardware/software) al conectar el medio original, documentar cada paso.

2.  **Cadena de Custodia (`Chain of Custody`):**
    * **Objetivo:** Mantener un registro cronológico detallado de quién ha tenido control sobre la evidencia desde su recolección hasta su presentación final.
    * **Acciones:** Documentar la recolección (fecha, hora, persona, método), almacenamiento seguro, transferencias de custodia y cada acceso para análisis. Esto asegura que la evidencia no ha sido manipulada. (En BTL1, se traduce en **documentar meticulosamente tus pasos de análisis**).

3.  **Documentación Exhaustiva:**
    * **Objetivo:** Registrar cada acción realizada, herramienta utilizada, comando ejecutado y hallazgo observado durante el análisis.
    * **Acciones:** Tomar notas detalladas, capturas de pantalla relevantes, exportar resultados de herramientas. Una buena documentación permite que otro analista pueda reproducir tus pasos y validar tus conclusiones (reproducibilidad).

4.  **Orden de Volatilidad (`Order of Volatility`):**
    * **Objetivo:** Recolectar la evidencia comenzando por la más volátil (la que desaparece más rápidamente) y terminando por la más persistente.
    * **Orden General (Más a Menos Volátil):**
        1.  Registros de CPU, Caché
        2.  Tabla de Rutas, Caché ARP, Tabla de Procesos, Memoria RAM
        3.  Conexiones de Red Temporales
        4.  Datos en Disco Duro/SSD
        5.  Logs de Sistema Remotos
        6.  Copias de Seguridad (Backups)
    * **Importancia:** La información en RAM (procesos, conexiones) se pierde al apagar el sistema. Por eso, el análisis de memoria (si se dispone de un volcado) o la respuesta en vivo (Live Response) suelen preceder al análisis de disco offline.

## 🔍 Tipos Principales de Análisis en BTL1

Esta sección del repositorio se centrará en las áreas clave de forense digital relevantes para BTL1:

* **[Adquisición de Evidencia](./01_Adquisicion.md):** Cómo crear imágenes forenses de discos y memoria y verificar su integridad (hashing).
* **[Análisis de Disco](./02_Analisis_Disco/):** Examinar sistemas de archivos (Windows NTFS, Linux ext4), recuperar archivos borrados, analizar artefactos del sistema operativo (logs, registro, historial de usuario) y extraer metadatos. Incluye técnicas como File Carving.
* **[Análisis de Memoria](./03_Analisis_Memoria/):** Investigar volcados de memoria RAM para encontrar procesos ocultos, conexiones de red pasadas, comandos ejecutados, malware residente en memoria, etc., usando herramientas como Volatility.

## 🎯 Enfoque BTL1

BTL1 evalúa tu capacidad para **aplicar técnicas forenses específicas** y **utilizar herramientas estándar** (Autopsy, Volatility, FTK Imager, Scalpel, ExifTool, KAPE, TSK, etc.) para encontrar respuestas a preguntas concretas dentro de un escenario de incidente. No se espera un nivel de experto forense legal, pero sí una aplicación correcta de los principios y herramientas básicas.

---
