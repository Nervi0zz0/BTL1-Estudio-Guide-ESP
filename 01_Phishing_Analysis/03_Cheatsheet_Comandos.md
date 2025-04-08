# Cheatsheet de Comandos para Análisis de Phishing

Esta es una referencia rápida en formato de tabla para comandos útiles en la línea de comandos durante el análisis inicial de artefactos de phishing.

**⚠️ Importante:** Ejecuta siempre estos comandos en un entorno controlado y seguro (como una máquina virtual de análisis aislada), especialmente si manejas archivos adjuntos potencialmente maliciosos. La seguridad es primordial.

## Tabla de Comandos Comunes

| Tarea/Objetivo                     | Comando                                             | Plataforma     | Notas/Uso                                                                 |
| :--------------------------------- | :-------------------------------------------------- | :------------- | :------------------------------------------------------------------------ |
| **Hashing de Archivos** |                                                     |                | **Fundamental para buscar reputación en VirusTotal, etc.** |
| Calcular Hash MD5                  | `md5sum <archivo>`                                  | Linux          | Obtiene hash MD5.                                                         |
| Calcular Hash MD5                  | `Get-FileHash -Algorithm MD5 <archivo>`             | Windows (PS)   | Obtiene hash MD5. (PS = PowerShell)                                       |
| Calcular Hash SHA256               | `sha256sum <archivo>`                               | Linux          | Obtiene hash SHA256 (generalmente preferido sobre MD5).                   |
| Calcular Hash SHA256               | `Get-FileHash -Algorithm SHA256 <archivo>`          | Windows (PS)   | Obtiene hash SHA256 (generalmente preferido sobre MD5).                   |
| **Identificación de Archivos** |                                                     |                | **Útil para detectar extensiones falsas.** |
| Identificar Tipo Real de Archivo   | `file <archivo>`                                    | Linux          | Determina el tipo de archivo por contenido ("magic numbers"), no extensión. |
| **Descarga de Artefactos (¡RIESGO!)** |                                                     |                | **¡¡SOLO en entornos aislados (sandbox/VM)!! NUNCA ejecutar.** |
| Descargar Archivo (con curl)       | `curl -O <URL_sospechosa>`                          | Linux          | Guarda el archivo remoto con su nombre original.                          |
| Descargar Archivo (con wget)       | `wget <URL_sospechosa>`                             | Linux          | Descarga el archivo desde la URL.                                         |
| **Análisis Básico de Contenido** |                                                     |                | **Puede revelar indicadores incrustados.** |
| Extraer Cadenas de Texto Legibles | `strings <archivo>`                                 | Linux/Win* | Busca y muestra texto legible (URLs, IPs, frases) dentro de un archivo (incluso binario). Puede generar mucho ruido. (*Requiere `strings` en Windows, ej. de Sysinternals). |

---

*Este cheatsheet se centra en tareas iniciales. Comandos más avanzados de forense o análisis de malware se tratan en sus respectivas secciones.*