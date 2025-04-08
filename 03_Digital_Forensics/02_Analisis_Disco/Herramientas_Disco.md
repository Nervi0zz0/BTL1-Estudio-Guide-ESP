# Herramientas Comunes de Análisis de Disco

Existen numerosas herramientas para el análisis forense de disco, desde suites completas hasta utilidades de línea de comandos especializadas. Aquí describimos algunas de las más relevantes y comunes, especialmente en el contexto de BTL1.

## 💻 Suites Forenses Integradas (GUI)

Estas plataformas proporcionan una interfaz gráfica y combinan múltiples funcionalidades de análisis.

* **[Autopsy](https://www.sleuthkit.org/autopsy/)**:
    * **Descripción:** Una plataforma forense digital **open source** y gratuita, muy utilizada tanto por principiantes como por profesionales. Es esencialmente una interfaz gráfica para The Sleuth Kit (ver más abajo) y otras herramientas.
    * **Funcionalidades Clave:**
        * Procesamiento de imágenes de disco (RAW/dd, E01, etc.).
        * Análisis detallado de sistemas de archivos (NTFS, FAT, ext, HFS+, etc.).
        * Extracción automática de artefactos (historial web, actividad de registro, emails, metadatos Exif, etc.) mediante módulos ("Ingest Modules").
        * Búsqueda por palabras clave (indexada o literal).
        * Visor de archivos (hexadecimal, texto, imágenes, etc.).
        * Creación de líneas de tiempo (Timelines) de actividad del sistema de archivos y otros artefactos.
        * File carving integrado para recuperar archivos borrados.
        * Generación de informes.
    * **Uso Básico (Conceptual - Mayormente GUI):**
        * Crear un nuevo caso (`New Case`).
        * Añadir una fuente de datos (`Add Data Source`), seleccionando la imagen de disco.
        * Configurar y ejecutar los módulos de ingesta (`Ingest Modules`) para el análisis automático.
        * Explorar el árbol de directorios, artefactos extraídos, vistas de línea de tiempo, etc.
    * **Comandos CLI (Menos Comunes para Análisis General):** Autopsy tiene algunos módulos que pueden usarse desde línea de comandos, pero el análisis principal es vía GUI. Los comandos como `autopsy -i <image>` o `autopsy -p <project>` suelen ser para lanzar la aplicación o módulos específicos en contextos más avanzados o de scripting.

* **FTK (Forensic Toolkit - AccessData/Exterro)**:
    * **Descripción:** Una suite forense comercial muy potente y reconocida en la industria. Similar en funcionalidades a Autopsy pero con características adicionales y a menudo considerada más rápida en el procesamiento de grandes volúmenes de datos.
    * **Nota:** No confundir con **FTK Imager** (gratuito y enfocado en adquisición y previsualización), aunque se integran. El uso de FTK completo es menos probable en escenarios de aprendizaje como BTL1 debido a su coste.

## 命令行 Frameworks y Toolkits (CLI)

Herramientas de línea de comandos que ofrecen gran potencia y flexibilidad, a menudo usadas por analistas experimentados o para scripting.

* **[The Sleuth Kit (TSK)](https://www.sleuthkit.org/)**:
    * **Descripción:** El motor **open source** detrás de Autopsy. Es una colección de herramientas de línea de comandos para análisis forense profundo de sistemas de archivos e imágenes de disco. Permite acceder a datos a bajo nivel.
    * **Herramientas Clave (Ejemplos):**
        * `fsstat`: Muestra detalles del sistema de archivos (tipo, tamaño de bloque, etc.).
        * `mmls`: Lista las particiones dentro de una imagen de disco.
        * `fls`: Lista archivos y directorios (similar a `ls`), incluyendo los eliminados.
        * `icat`: Extrae el contenido de un archivo basándose en su número de inodo (metadata node).
        * `ffind`: Busca nombres de archivo que apunten a un inodo específico.
        * `ifind`: Busca el inodo que apunta a un nombre de archivo específico.
        * `tsk_recover`: Intenta recuperar archivos borrados.
        * `tsk_gettimes`: Extrae timestamps MACB.
    * **Uso:** Potente para análisis específicos o scripting, requiere un conocimiento más profundo de sistemas de archivos.

* **[Eric Zimmerman's Tools (EZ Tools)](https://ericzimmerman.github.io/)**:
    * **Descripción:** Una suite **imprescindible** de herramientas **gratuitas** y altamente eficientes desarrolladas por Eric Zimmerman, enfocadas en parsear artefactos específicos de **Windows**. Son estándar de facto para muchos análisis.
    * **Herramientas Clave (Ejemplos):** `Registry Explorer`/`RECmd` (Registro), `EvtTxECmd` (Event Logs), `PECmd` (Prefetch), `AmcacheParser`, `SrumECmd`, `LECmd` (LNK files), `JLECmd` (Jumplists), `SBECmd` (Shellbags), `MFTECmd` ($MFT, $LogFile, $UsnJrnl), `RBCmd` (Recycle Bin), y muchas más.
    * **Uso:** Se ejecutan desde la línea de comandos de Windows, son rápidas y generan salidas en formatos fáciles de procesar (ej. CSV).

## 🛠️ Herramientas Específicas y Complementarias

* **[KAPE (Kroll Artifact Parser and Extractor)](https://www.kroll.com/en/services/cyber-risk/kape)**:
    * **Descripción:** Herramienta extremadamente eficiente para **recolectar** (Targets) y/o **procesar** (Modules) artefactos forenses clave de un sistema vivo o una imagen montada.
    * **Uso:** Ideal para triaje rápido o para extraer artefactos específicos y luego analizarlos con otras herramientas (como las de Eric Zimmerman, que se pueden integrar como Módulos en KAPE).
    * **Comandos Básicos (Ejemplos):**
        * `kape.exe --tsource C: --tdest C:\KAPE_Output\ --target RegistryHives --module RegistryExplorer` (Extrae hives y los procesa con Registry Explorer).
        * `kape.exe --tsource \\ruta\a\imagen_montada --tdest C:\KAPE_Output\ --target Windows_Basic_Info` (Recolecta un conjunto básico de artefactos definidos en el Target).

* **Parsers de Logs Específicos:**
    * **LogParser (Microsoft):** Herramienta CLI potente que permite usar sintaxis tipo SQL para consultar diversos tipos de archivos de log (texto, CSV, EVTX, XML, etc.). Curva de aprendizaje, pero muy flexible.
    * Herramientas de línea de comandos estándar (`grep`, `awk`, `sed` en Linux/WSL) para filtrar logs de texto.

* **Visores Hexadecimales:**
    * **Ejemplos:** [HxD](https://mh-nexus.de/en/hxd/) (Windows, gratuito), [010 Editor](https://www.sweetscape.com/010editor/) (Comercial, potente con plantillas binarias).
    * **Uso:** Permiten ver y editar el contenido crudo (en hexadecimal y ASCII) de cualquier archivo o incluso de un disco/imagen. Útiles para buscar manualmente cabeceras/pies de archivo (file carving), analizar estructuras de archivos desconocidas, encontrar datos ocultos.

---
*La elección de la herramienta a menudo depende de la tarea específica, el sistema operativo analizado y las preferencias personales del analista. Es común usar una combinación de suites GUI y herramientas CLI especializadas.*
