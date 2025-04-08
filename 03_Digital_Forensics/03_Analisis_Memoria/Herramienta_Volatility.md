# Herramienta Esencial: The Volatility Framework

**Volatility** es el framework **open source** líder y estándar de facto para el análisis forense de memoria volátil (RAM). Escrito en Python, permite extraer artefactos digitales de volcados de memoria de una gran variedad de sistemas operativos (Windows, Linux, macOS, Android).

Funciona mediante un sistema de **plugins**, cada uno diseñado para extraer un tipo específico de información del volcado de memoria.

## ⚠️ Volatility 2 vs. Volatility 3: ¡Importante!

Existen dos versiones principales de Volatility, y es crucial entender sus diferencias, ya que la sintaxis y algunos plugins varían:

* **Volatility 2 (Legacy):**
    * Basado en Python 2 (aunque existen forks compatibles con Python 3).
    * Depende **fuertemente** de **Perfiles (`Profiles`)**: Definiciones pre-construidas específicas para cada versión, Service Pack y arquitectura del Sistema Operativo (ej. `Win7SP1x64`, `Win10x64_14393`, `LinuxDebian10x64`).
    * **Es obligatorio identificar y especificar el perfil correcto** para que los plugins funcionen adecuadamente. El comando `imageinfo` (V2) se usa para identificar posibles perfiles.
    * **Sintaxis Típica:** `python vol.py --profile=<ProfileName> -f <archivo_dump> <plugin_V2> [opciones_plugin]`
    * Todavía muy usado y con mucha documentación/tutoriales online. Podría ser la versión encontrada en algunos entornos de examen/CTF.

* **Volatility 3 (Actual):**
    * Basado en Python 3.
    * **Objetivo:** Ser "sin perfiles" (`profile-less`). En lugar de perfiles, utiliza **Tablas de Símbolos (`Symbol Tables`)**. Intenta detectar el SO automáticamente y descargar/encontrar las tablas de símbolos necesarias.
    * **Sintaxis Típica:** `python vol.py -f <archivo_dump> <plugin.con.puntos> [opciones_plugin]` (ej. `windows.pslist.PsList`).
    * Es la versión en desarrollo activo y el futuro del framework.

**¿Cuál usar en BTL1?** **Consulta la documentación o el entorno proporcionado por BTL1.** Es vital saber qué versión esperan que uses, ya que afecta directamente a los comandos que ejecutarás. Esta guía mostrará principalmente la sintaxis y nombres de plugins de Volatility 3, pero mencionará los equivalentes de Volatility 2 cuando sea relevante.

## ⚙️ Instalación y Configuración Básica

* **Requisito:** Python (preferiblemente Python 3 para Volatility 3).
* **Instalación:** Generalmente vía `pip install volatility3` o clonando el repositorio desde GitHub y instalando dependencias.
* **Volatility 3 Symbol Packs:** V3 necesita tablas de símbolos. Intenta descargarlas automáticamente, pero si falla, puede que necesites descargarlas manualmente y colocarlas en la carpeta `volatility3/symbols/`.

## 🔄 Workflow Básico de Uso

1.  **Identificar Información del Volcado:**
    * **Vol3:** `python3 vol.py -f <dump> windows.info.Info` o `linux.info.Info`. Suele detectar el SO automáticamente.
    * **Vol2:** `python vol.py -f <dump> imageinfo`. **Crucial** para obtener la lista de perfiles sugeridos (`Suggested Profile(s)`).

2.  **Ejecutar Plugins:**
    * **Vol3:** `python3 vol.py -f <dump> <plugin.con.puntos> [opciones]`
    * **Vol2:** `python vol.py --profile=<PerfilElegido> -f <dump> <plugin_v2> [opciones]`

3.  **Analizar Salida:** Interpretar la información que devuelve el plugin.

4.  **Filtrar Resultados (Opcional):** Para salidas muy largas, puedes usar `grep` (Linux) o `findstr` (Windows) para filtrar por palabras clave, o procesar la salida con scripts. Ejemplo: `python3 vol.py -f <dump> windows.pslist.PsList | grep suspicious.exe`

## 🔌 Plugins Clave (Ejemplos Comunes V3 / V2)

Esta tabla lista algunos de los plugins más útiles, muchos de los cuales mencionaste en tus notas iniciales y son relevantes para BTL1. (La notación es V3, con el equivalente V2 entre paréntesis si difiere).

| Tarea/Objetivo                 | Plugin Volatility 3 (Ejemplo)              | Plugin Volatility 2 (Ejemplo) | Notas                                                                    |
| :----------------------------- | :----------------------------------------- | :---------------------------- | :----------------------------------------------------------------------- |
| **Información General** | `windows.info.Info` / `linux.info.Info`    | `imageinfo`                   | Identifica SO, versión, arch. Crucial para V2 (obtener Profile).         |
| **Procesos** | `windows.pslist.PsList` / `linux.pslist.PsList` | `pslist`                      | Lista procesos activos (simil `tasklist`/`ps`).                          |
|                                | `windows.pstree.PsTree` / `linux.pstree.PsTree` | `pstree`                      | Muestra procesos en árbol (relaciones padre-hijo).                       |
|                                | `windows.psscan.PsScan` / `linux.psscan.PsScan` | `psscan`                      | Escanea buscando estructuras de proceso (encuentra ocultos/terminados). |
|                                | `windows.cmdline.CmdLine` / `linux.cmdline.CmdLine` | `cmdline`                     | Muestra argumentos de línea de comandos.                                |
|                                | `windows.dlllist.DllList` / `linux.ldrmodules.LdrModules` | `dlllist` / `ldrmodules`    | Lista DLLs/librerías cargadas por proceso.                             |
|                                | `windows.handles.Handles`                   | `handles`                     | Lista handles abiertos por proceso (archivos, claves reg, etc.).          |
|                                | `windows.memmap.Memmap` / `linux.proc.Maps` | `memmap` / `proc_maps`      | Muestra el mapa de memoria de un proceso.                                |
|                                | `windows.procdump.ProcDump` / `linux.procdump.ProcDump` | `procdump`                    | Vuelca la memoria **ejecutable** de un proceso a un archivo.              |
| **Redes** | `windows.netscan.NetScan` / `linux.netstat.NetStat` | `netscan` / `netstat`         | Muestra conexiones (TCP/UDP) y puertos escucha.                          |
|                                | `windows.netstat.NetStat`                  | `connections`/`connscan`      | (V3 Win) Alternativa a NetScan. (V2) `connscan` busca TCP cerradas.    |
| **Registro (Windows)** | `windows.registry.hivelist.HiveList`      | `hivelist`                    | Lista hives del registro cargados en memoria.                             |
|                                | `windows.registry.printkey.PrintKey`      | `printkey`                    | Muestra valor(es) de una clave de registro específica en memoria.      |
|                                | `windows.registry.hivedump.HiveDump`      | `hivedump`                    | Vuelca un hive completo desde memoria a un archivo.                      |
| **Sistema** | `windows.modules.Modules` / `linux.lsmod.Lsmod` | `modules` / `lsmod`           | Lista módulos del kernel/drivers cargados.                             |
|                                | `windows.modscan.ModScan` / `linux.modscan.ModScan` | `modscan`                     | Escanea buscando estructuras de módulo (encuentra ocultos).           |
|                                | `windows.svcscan.SvcScan`                   | `svcscan`                     | Escanea servicios registrados (Windows).                                 |
| **Archivos en Memoria** | `windows.filescan.FileScan` / `linux.filescan.FileScan` | `filescan`                    | Escanea buscando objetos de archivo en memoria.                          |
|                                | `windows.dumpfiles.DumpFiles` / `linux.dumpfiles.DumpFiles` | `dumpfiles`                   | Vuelca archivos cacheados/mapeados desde memoria al disco.               |
| **Malware/Varios** | `windows.malfind.Malfind` / `linux.malfind.Malfind` | `malfind`                     | Busca código inyectado/sospechoso en memoria de procesos.               |
|                                | `windows.cmdscan.CmdScan`                   | `cmdscan`/`consoles`          | Busca comandos en consolas (`cmd.exe`).                                  |
|                                | `windows.clipboard.Clipboard`               | `clipboard`                   | Extrae contenido del portapapeles.                                     |
|                                | `timeliner.Timeliner`                     | `timeliner`                   | Crea una línea de tiempo con eventos de varios plugins.                  |
|                                | `yarascan.YaraScan`                        | `yarascan`/`linux_yarascan`   | Escanea memoria con reglas YARA.                                        |
|                                | `windows.hashdump.Hashdump`                 | `hashdump`                    | Vuelca hashes LM/NTLM desde memoria (Windows).                           |

*(Nota: La disponibilidad exacta y nombres de plugins pueden variar ligeramente entre versiones menores. Consulta siempre la ayuda del propio Volatility: `python3 vol.py -h` o `python vol.py -h`)*

---
*Dominar Volatility es esencial para el análisis de memoria. Empieza por los plugins básicos (`pslist`, `netscan`, `cmdline`, `dlllist`) y ve explorando otros según lo necesites.*
