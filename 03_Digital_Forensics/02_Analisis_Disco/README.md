# Análisis Forense de Disco

El **análisis forense de disco** es el proceso de examinar el contenido de medios de almacenamiento digital (discos duros HDD, unidades de estado sólido SSD, memorias USB, etc.) para extraer evidencia relevante sobre la actividad del sistema y del usuario. Es una de las áreas más fundamentales de la forense digital.

Trabajando sobre una imagen forense adquirida previamente (ver sección `01_Adquisicion.md`), el objetivo es encontrar respuestas a preguntas como: ¿Qué programas se ejecutaron? ¿Qué archivos se crearon, modificaron o eliminaron? ¿Hubo acceso a recursos externos? ¿Hay rastros de malware? ¿Cuándo ocurrieron ciertos eventos?

## 🎯 Objetivos Comunes del Análisis de Disco

* **Identificar Actividad del Usuario:** Logins, ejecución de programas, archivos abiertos/modificados/eliminados, búsquedas realizadas, historial de navegación web, comunicaciones.
* **Encontrar Artefactos de Malware:** Ejecutables, scripts, archivos de configuración, claves de registro de persistencia, indicadores de C2.
* **Recuperar Datos Eliminados:** Intentar recuperar archivos que ya no están en el sistema de archivos activo (desde espacio no asignado, slack space, etc.).
* **Establecer Líneas de Tiempo (`Timelines`):** Correlacionar marcas de tiempo (timestamps) de diferentes artefactos para reconstruir la secuencia de eventos.
* **Analizar Estructura del Sistema de Archivos:** Entender cómo se organizan los datos (NTFS, ext4, FAT32...) y extraer metadatos importantes (fechas MACB - Modify, Access, Change, Birth).

## 🧭 Enfoque Metodológico General

1.  **Montaje Seguro:** Montar la imagen forense en un entorno de análisis, preferiblemente en modo **solo lectura** para evitar modificaciones accidentales.
2.  **Herramientas:** Utilizar suites forenses integradas (como Autopsy, FTK) que automatizan muchos procesos, o herramientas especializadas para tareas concretas (TSK, KAPE, herramientas de Eric Zimmerman, etc.).
3.  **Análisis de Artefactos Específicos:** Enfocarse en artefactos conocidos del sistema operativo (Windows o Linux) que registran actividad relevante.
4.  **Búsqueda de Palabras Clave:** Buscar términos relevantes (nombres de usuario, nombres de archivo, IPs, dominios) en todo el disco o en áreas específicas.
5.  **Creación de Líneas de Tiempo:** Agregar y ordenar eventos basados en sus marcas de tiempo.
6.  **Documentación Rigurosa:** Anotar cada hallazgo, su ubicación, la herramienta utilizada y su relevancia.

## 📂 Estructura de esta Subsección

Dentro de esta carpeta (`02_Analisis_Disco`), exploraremos los siguientes temas en detalle:

* **[Artefactos Clave en Windows](./Windows_Artefactos.md):** Registro, Event Logs, Prefetch, Shellbags, LNK, Jumplists, MFT, USN Journal, SRUM, etc.
* **[Artefactos Clave en Linux](./Linux_Artefactos.md):** Logs (`/var/log`), historial de comandos, `cron jobs`, cuentas de usuario, `/proc`, etc.
* **[Herramientas Comunes de Análisis de Disco](./Herramientas_Disco.md):** Introducción a Autopsy, The Sleuth Kit (TSK), KAPE y otras.
* **[File Carving con Scalpel](./File_Carving_Scalpel.md):** Técnica para recuperar archivos basándose en sus cabeceras/pies.
* **[Análisis de Metadatos con ExifTool](./Metadata_ExifTool.md):** Extracción de información oculta dentro de los archivos.

**Nota:** Un entendimiento básico de los sistemas de archivos como **NTFS** (Master File Table - MFT, $LogFile, $UsnJrnl, Alternate Data Streams - ADS) y **ext4** (inodes, journal) es muy beneficioso para saber dónde buscar la evidencia.

---
