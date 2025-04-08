# Artefactos Forenses Clave en Windows

Windows registra una enorme cantidad de información sobre la actividad del sistema y del usuario. Conocer dónde buscar estos artefactos es fundamental para cualquier investigación forense en este sistema operativo.

## 📁 Registro de Windows (`Windows Registry`)

Una base de datos jerárquica que almacena configuraciones de bajo nivel del sistema operativo y de las aplicaciones. Es una mina de oro para la información forense.

* **Valor Forense:** Persistencia de malware, cuentas de usuario, dispositivos USB conectados, programas ejecutados, historial de red, archivos recientes, y mucho más.
* **Estructura (Hives):** Archivos principales donde se almacena el registro. Los más importantes son:
    * `SAM`: Información de cuentas de usuario y grupos locales (`C:\Windows\System32\config\SAM`).
    * `SECURITY`: Políticas de seguridad, caché de logins (`C:\Windows\System32\config\SECURITY`).
    * `SOFTWARE`: Configuraciones de software a nivel de sistema (`C:\Windows\System32\config\SOFTWARE`).
    * `SYSTEM`: Configuración del hardware y servicios del sistema (`C:\Windows\System32\config\SYSTEM`).
    * `NTUSER.DAT`: Configuración específica del perfil de usuario (por cada usuario: `C:\Users\<usuario>\NTUSER.DAT`).
    * `UsrClass.dat`: Información relacionada con COM y configuraciones de interfaz de usuario (`C:\Users\<usuario>\AppData\Local\Microsoft\Windows\UsrClass.dat`).
* **Claves/Áreas de Interés (Ejemplos):**
    * **Persistencia:** `Run`, `RunOnce`, `Services` (en `SOFTWARE` y `SYSTEM`), `Scheduled Tasks` (ver sección propia).
    * **Ejecución:** `UserAssist` (`NTUSER.DAT`), `Shimcache`/`AppCompatCache` (`SYSTEM`), `Amcache.hve` (archivo propio).
    * **Dispositivos USB:** `USBStor` (`SYSTEM`).
    * **Archivos Recientes/Accedidos:** `RecentDocs` (`NTUSER.DAT`), `OpenSavePidlMRU` (`NTUSER.DAT`), `Shellbags` (`NTUSER.DAT`, `UsrClass.dat`).
    * **Red:** `NetworkList` (`SOFTWARE`), `Interfaces` (`SYSTEM`).
    * **Historial Web (IE/Edge Legacy):** `TypedURLs` (`NTUSER.DAT`).
* **Herramientas:** Registry Editor (live), RegRipper, Registry Explorer (Eric Zimmerman), Autopsy/FTK modules.

## 📜 Registros de Eventos (`Event Logs`)

Registros cronológicos de eventos importantes del sistema, seguridad y aplicaciones.

* **Valor Forense:** Logins (éxito/fallo), creación de procesos, instalación de servicios, cambios de política, borrado de logs, errores de aplicación, etc.
* **Formato:** `.evt` (Windows XP/2003), `.evtx` (Vista y posteriores).
* **Ubicación Principal:** `C:\Windows\System32\winevt\Logs\`
* **Logs Clave:** `Security.evtx`, `System.evtx`, `Application.evtx`. Hay muchos otros específicos (PowerShell, TaskScheduler, WMI, etc.).
* **IDs de Evento Clave (Ejemplos en Security.evtx):**
    * `4624`: Logon exitoso.
    * `4625`: Logon fallido.
    * `4634`/`4647`: Logoff.
    * `4688`: Creación de proceso (requiere auditoría avanzada habilitada).
    * `4720`: Creación de cuenta de usuario.
    * `1102`: Borrado del log de auditoría de seguridad.
    * `7045` (System Log): Instalación de un servicio.
* **Herramientas:** Windows Event Viewer, EvtTxECmd (Eric Zimmerman), Chainsaw, LogParser, módulos de suites forenses, SIEMs.

## 🚀 Evidencia de Ejecución de Programas

Varios artefactos rastrean qué programas se han ejecutado, cuándo y con qué frecuencia.

* **Prefetch (`.pf`):**
    * **Valor:** Registra ejecuciones de programas para acelerar cargas futuras. Guarda: nombre del ejecutable, contador de ejecuciones, 8 últimos tiempos de ejecución, archivos cargados por el programa.
    * **Ubicación:** `C:\Windows\Prefetch\`
    * **Herramientas:** PECmd (Eric Zimmerman), Autopsy.

* **Superfetch / SysMain (`Ag*.db`):**
    * **Valor:** Similar a Prefetch, busca optimizar la carga de aplicaciones basándose en patrones de uso. Su base de datos puede contener evidencia de ejecución.
    * **Ubicación:** `C:\Windows\Prefetch\`. Formato de base de datos ESE.
    * **Herramientas:** SrumECmd puede parsear datos relacionados.

* **Amcache / AppCompatCache (Shimcache):**
    * **Valor:** Rastrean la ejecución de programas para temas de compatibilidad. Pueden contener información sobre ejecutables incluso si Prefetch está deshabilitado o el archivo ha sido borrado.
    * **Ubicación:** Shimcache (en memoria y en el hive `SYSTEM` del Registro), Amcache (hive propio en `C:\Windows\AppCompat\Programs\Amcache.hve`).
    * **Herramientas:** AppCompatCacheParser, AmcacheParser (Eric Zimmerman), RegRipper, Autopsy.

* **SRUM (`SRUDB.dat`):**
    * **Valor:** Monitor de Uso de Recursos del Sistema. Base de datos muy detallada sobre ejecuciones de procesos, uso de red por proceso/usuario, etc. Extremadamente útil pero compleja.
    * **Ubicación:** `C:\Windows\System32\sru\SRUDB.dat` (Base de datos ESE).
    * **Herramientas:** SrumECmd (Eric Zimmerman).

## 🔗 Evidencia de Acceso a Archivos y Carpetas

Artefactos que indican qué archivos, carpetas o aplicaciones ha utilizado un usuario.

* **Archivos LNK (`.lnk`):**
    * **Valor:** Accesos directos creados (automática o manualmente) al acceder a archivos/programas. Almacenan la ruta original del objetivo, sus timestamps MACB, e información del volumen donde residía (tipo, número de serie). Evidencia de acceso a archivos/rutas (incluso en unidades extraíbles).
    * **Ubicación:** Escritorio, Menú Inicio, `%UserProfile%\AppData\Roaming\Microsoft\Windows\Recent\`, y embebidos en Jumplists.
    * **Herramientas:** LECmd (Eric Zimmerman), Autopsy.

* **Jumplists:**
    * **Valor:** Listas de archivos/destinos recientes asociadas a aplicaciones (ej. los archivos recientes de Word, las carpetas recientes del Explorador). Contienen datos similares a los LNK.
    * **Ubicación:** `%UserProfile%\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations\` y `CustomDestinations\`.
    * **Herramientas:** JLECmd (Eric Zimmerman), Autopsy.

* **Shellbags:**
    * **Valor:** El Registro almacena las preferencias de visualización de carpetas del Explorador (tamaño de iconos, vista...). Crucialmente, registra las carpetas que un usuario ha navegado, ¡incluso si la carpeta ya no existe o estaba en un medio extraíble! Evidencia de conocimiento/acceso a rutas específicas.
    * **Ubicación:** Hives `NTUSER.DAT` y `UsrClass.dat`.
    * **Herramientas:** SBECmd (Eric Zimmerman), Registry Explorer, Autopsy.

## 🗑️ Papelera de Reciclaje (`$Recycle.Bin`)

* **Valor:** Contiene archivos eliminados por el usuario a través del Explorador de Windows. Permite recuperar archivos y obtener metadatos sobre la eliminación.
* **Ubicación:** Carpeta oculta `$Recycle.Bin` en la raíz de cada volumen, con subcarpetas por SID de usuario. Dentro, archivos `$I` (metadatos: ruta original, fecha de borrado) y `$R` (contenido del archivo).
* **Herramientas:** Navegadores de sistemas de archivos (Autopsy, FTK Imager), RCmd (Eric Zimmerman).

## ⏰ Tareas Programadas (`Scheduled Tasks`)

* **Valor:** Mecanismo legítimo para automatizar tareas, pero muy usado por malware para persistencia (ejecutarse al inicio, periódicamente).
* **Ubicación:** `C:\Windows\System32\Tasks\` (archivos XML que definen la tarea), también en el Registro (versiones antiguas).
* **Herramientas:** Visor de Tareas Programadas (live), análisis directo de los XML, Autoruns (Sysinternals), Autopsy.

## 📄 Artefactos del Sistema de Archivos NTFS

El propio sistema de archivos NTFS guarda metadatos muy valiosos.

* **$MFT (Master File Table):**
    * **Valor:** El "índice" de todo el volumen NTFS. Contiene al menos un registro para cada archivo y directorio, con sus atributos (nombre, tamaño, timestamps MACB, permisos) y, para archivos pequeños, incluso los datos mismos ("residentes"). Persiste información incluso después de borrar archivos (el registro se marca como no usado, pero no se borra inmediatamente). Fundamental para timelines y recuperación de archivos.
    * **Herramientas:** MFTECmd (Eric Zimmerman), analyzeMFT, Autopsy/TSK.

* **$LogFile:**
    * **Valor:** Log transaccional de NTFS. Registra operaciones de metadatos antes de que se escriban en la MFT. Puede contener información sobre archivos muy recientes o recién eliminados.
    * **Herramientas:** MFTECmd (Eric Zimmerman), LogFileParser.

* **$UsnJrnl (Update Sequence Number Journal):**
    * **Valor:** Log de cambios realizados en archivos y directorios (creación, borrado, renombrado, etc.). Muy útil para rastrear la actividad de archivos en un período de tiempo.
    * **Ubicación:** `$Extend\$UsnJrnl` (archivo oculto del sistema).
    * **Herramientas:** MFTECmd (Eric Zimmerman), UsnJrnl_parser, Autopsy.

* **ADS (Alternate Data Streams):**
    * **Valor:** Característica de NTFS que permite adjuntar datos a un archivo de forma "oculta" (no visible en el tamaño normal del archivo). A veces usado por malware para esconder ejecutables o configuraciones.
    * **Herramientas:** `dir /r` (CMD), `Get-Item -Stream *` (PowerShell), suites forenses suelen detectarlos.

## 🌐 Historial de Navegación Web

* **Valor:** Sitios visitados, descargas, búsquedas, cookies, caché. Crucial para rastrear actividad online.
* **Ubicación:** Varía mucho según el navegador (Chrome, Firefox, Edge...). Generalmente en subcarpetas de `%UserProfile%\AppData\Local\` o `Roaming\`. Suelen ser bases de datos SQLite.
* **Herramientas:** Herramientas específicas por navegador (ej. de NirSoft), visores de SQLite (DB Browser for SQLite), módulos de Autopsy/FTK.

---
