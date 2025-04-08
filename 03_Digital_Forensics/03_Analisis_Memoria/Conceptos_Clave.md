# Análisis de Memoria: Conceptos y Artefactos Clave

Un volcado de memoria RAM es una mina de oro de información sobre el estado de un sistema en un momento específico. Al analizarlo con herramientas como Volatility, podemos extraer una gran variedad de artefactos que no están disponibles (o son difíciles de encontrar) en el análisis de disco.

Estos son algunos de los tipos de información clave que buscamos:

## ⚙️ Información de Procesos

Entender qué procesos estaban en ejecución es fundamental.

* **Listas de Procesos:**
    * **Qué:** Obtener la lista de procesos activos en el momento de la captura.
    * **Valor:** Identificar procesos legítimos, procesos maliciosos conocidos, procesos ocultos (rootkits) o procesos terminados recientemente. Ver relaciones padre-hijo (cómo se lanzó un proceso).
    * **Plugins Volatility (Ejemplos):** `pslist`, `psscan`, `pstree`, `psaux` (Linux).

* **Detalles del Proceso:**
    * **Qué:** Información específica sobre un proceso: argumentos de línea de comandos con los que se lanzó, variables de entorno, librerías (DLLs) cargadas, identificadores de seguridad (SID), handles abiertos (a archivos, claves de registro, etc.).
    * **Valor:** Entender qué hacía el proceso, si cargó DLLs maliciosas (DLL hijacking/injection), a qué recursos estaba accediendo, con qué configuración se ejecutó.
    * **Plugins Volatility (Ejemplos):** `cmdline`, `environ`, `dlllist`, `handles`, `getsids`, `vadinfo`, `memmap`.

* **Volcado de Memoria del Proceso:**
    * **Qué:** Extraer el espacio de memoria completo asignado a un proceso específico.
    * **Valor:** Permite un análisis más profundo del proceso sospechoso con otras herramientas (desensambladores, debuggers, análisis de strings). Crucial para analizar malware empaquetado (`packed`) o inyectado, ya que se obtiene el código "desempaquetado" tal como residía en memoria.
    * **Plugins Volatility (Ejemplos):** `procdump`, `memdump`, `vadump`.

## 🔌 Información de Red

Identificar la actividad de red del sistema en el momento de la captura.

* **Conexiones de Red:**
    * **Qué:** Listar conexiones TCP y UDP activas, así como puertos en escucha y conexiones cerradas recientemente.
    * **Valor:** Detectar conexiones a servidores de Comando y Control (C2) de malware, actividad de exfiltración de datos, conexiones a IPs/puertos maliciosos conocidos, puertos abiertos por malware para escucha. Muestra IP/puerto local y remoto, estado y el PID del proceso dueño.
    * **Plugins Volatility (Ejemplos):** `netscan` (Win/Lin), `connections` (Win), `connscan` (Win), `sockets` (Lin), `sockscan` (Lin).

## 🖥️ Actividad del Sistema Operativo

Información sobre el estado interno del kernel y los servicios.

* **Módulos del Kernel / Drivers:**
    * **Qué:** Listar los controladores (`.sys` en Win, `.ko` en Lin) cargados en memoria.
    * **Valor:** Identificar drivers legítimos, pero también rootkits (que a menudo operan como drivers maliciosos) o drivers cargados por malware. Permite detectar drivers ocultos (`modscan`).
    * **Plugins Volatility (Ejemplos):** `modules` (Win/Lin), `modscan` (Win/Lin), `driverscan` (Win).

* **Servicios:**
    * **Qué:** Enumerar los servicios registrados en el sistema (Windows).
    * **Valor:** Identificar servicios maliciosos instalados para persistencia.
    * **Plugins Volatility (Ejemplos):** `svcscan` (Win).

* **Hooks y Callbacks (Avanzado):**
    * **Qué:** Detectar modificaciones en tablas importantes del sistema (como la System Service Dispatch Table - SSDT) o puntos de intercepción (IRP hooks, callbacks) que el malware usa para interceptar o redirigir funciones del sistema operativo.
    * **Valor:** Detección de rootkits y técnicas avanzadas de evasión/ocultamiento.
    * **Plugins Volatility (Ejemplos):** `ssdt` (Win), `driverirp` (Win), `callbacks` (Win).

## 🔑 Artefactos del Registro en Memoria (Windows)

El registro de Windows se carga parcialmente en memoria. Analizarlo puede revelar información reciente o claves accedidas por malware.

* **Hives Cargados:**
    * **Qué:** Encontrar la ubicación en memoria de los archivos "hive" del registro (SAM, SYSTEM, SOFTWARE, NTUSER.DAT, etc.).
    * **Valor:** Necesario para luego poder extraer ("dumpear") estos hives.
    * **Plugins Volatility (Ejemplos):** `hivelist`.

* **Volcado de Hives:**
    * **Qué:** Extraer los hives del registro directamente desde la memoria al disco.
    * **Valor:** Permite analizar los hives offline con herramientas de análisis de registro (Registry Explorer, RegRipper) para encontrar claves de persistencia, ejecución reciente, etc., tal como estaban en memoria (podría diferir ligeramente del disco).
    * **Plugins Volatility (Ejemplos):** `hivedump`.

* **Consulta de Claves/Valores:**
    * **Qué:** Buscar y mostrar el contenido de claves o valores específicos del registro directamente en memoria.
    * **Valor:** Útil para verificar rápidamente valores específicos (ej. claves Run) sin dumpear todo el hive.
    * **Plugins Volatility (Ejemplos):** `printkey`.

## 🔒 Credenciales y Secretos (Potencial)

La memoria puede contener credenciales sensibles que el sistema necesita para operar.

* **Hashes de Contraseñas (Windows):**
    * **Qué:** Extraer los hashes LM/NTLM de las contraseñas de usuario desde el hive SAM cargado en memoria.
    * **Valor:** Útil si no se puede acceder al archivo SAM en disco o para obtener los hashes tal como estaban en memoria en ese momento. Se pueden intentar crackear offline.
    * **Plugins Volatility (Ejemplos):** `hashdump`.

* **Otros Secretos (Avanzado):** Dependiendo de los procesos y el sistema, a veces se pueden encontrar claves de cifrado, tokens de sesión, LSA secrets, etc., con plugins específicos (`lsa_secrets`, `mimikatz` plugin, etc.).

## 👤 Actividad Reciente del Usuario

Restos de la interacción del usuario con el sistema.

* **Comandos Ejecutados:**
    * **Qué:** Recuperar comandos tecleados en consolas `cmd.exe` o PowerShell.
    * **Valor:** Ver qué comandos ejecutó un usuario o un atacante recientemente.
    * **Plugins Volatility (Ejemplos):** `cmdscan`, `consoles`.

* **Portapapeles (`Clipboard`):**
    * **Qué:** Extraer el contenido que estaba en el portapapeles en el momento de la captura.
    * **Valor:** Puede contener información copiada recientemente (contraseñas, URLs, fragmentos de código).
    * **Plugins Volatility (Ejemplos):** `clipboard`.

## 🦠 Evidencia Específica de Malware

Plugins diseñados para buscar rastros comunes de malware.

* **Código Inyectado / Sospechoso:**
    * **Qué:** Buscar regiones de memoria dentro de procesos que no corresponden a un archivo en disco conocido, o que tienen permisos inusuales (ej. Ejecutable y Escribible), indicando posible inyección de código.
    * **Valor:** Detectar malware que opera inyectándose en procesos legítimos.
    * **Plugins Volatility (Ejemplos):** `malfind`, `ldrmodules`, `hollowfind` (detección de Process Hollowing).

* **Escaneo con YARA:**
    * **Qué:** Escanear todo el volcado de memoria (o la memoria de procesos específicos) usando reglas YARA personalizadas o conocidas.
    * **Valor:** Detectar patrones (strings, secuencias de bytes) asociados a familias de malware conocidas o a técnicas sospechosas.
    * **Plugins Volatility (Ejemplos):** `yarascan`.

---
*Esta lista cubre los artefactos más importantes. La herramienta principal para extraerlos es Volatility, cuyo uso detallaremos en la siguiente sección.*
