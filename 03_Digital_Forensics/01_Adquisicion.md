# Adquisición de Evidencia Digital

La **adquisición** es el proceso de crear una copia forense (imagen) de la evidencia digital original (ej. disco duro, memoria RAM, unidad USB) de manera que se preserve el estado original tanto como sea posible. Es el pilar sobre el que se construye toda la investigación forense.

**Objetivo Principal:** Crear una copia bit a bit verificable del original para poder analizarla sin alterar la fuente primaria.

## 🧱 Tipos de Imágenes Forenses

Existen diferentes formatos y tipos de imágenes, cada uno con sus pros y contras:

1.  **Imagen Física (Bitstream / Bit-a-Bit):**
    * **Descripción:** Copia sector por sector todo el dispositivo físico, incluyendo el espacio asignado, no asignado (donde pueden residir datos borrados) y el espacio "slack". Es la imagen más completa.
    * **Formatos Comunes:**
        * **RAW / DD (`.dd`, `.img`, `.bin`):** Formato simple, salida directa bit a bit. Compatible con muchas herramientas, pero no incluye metadatos, compresión ni hashing interno. Puede dividirse en segmentos (`.001`, `.002`...).
        * **EnCase (`.E01`, `.Ex01`):** Formato propietario (pero ampliamente soportado). Incluye metadatos (nombre del caso, examinador, notas), compresión opcional y hashing interno (MD5 y/o SHA1) por bloques para verificación de integridad. Es un estándar de facto en muchos entornos.
    * **Ventajas:** Máxima completitud, permite recuperar archivos borrados y analizar artefactos en espacio no asignado.
    * **Desventajas:** Archivos de imagen muy grandes (iguales al tamaño del disco original si no se comprimen).

2.  **Imagen Lógica:**
    * **Descripción:** Copia solo los archivos y directorios activos dentro de un sistema de archivos o partición seleccionada. No incluye espacio no asignado ni archivos borrados (a menos que estén en la papelera de reciclaje, por ejemplo).
    * **Formatos Comunes:** Formatos propietarios (`.L01`, `.AD1`), o simplemente contenedores de archivos (ej. `.zip`, `.tar` - aunque estos son menos forenses al no preservar metadatos del sistema de archivos de forma robusta).
    * **Ventajas:** Más rápido de adquirir, tamaño de imagen considerablemente menor.
    * **Desventajas:** Incompleta, no permite recuperar la mayoría de datos borrados ni analizar artefactos fuera del sistema de archivos activo.

**Enfoque BTL1:** Generalmente trabajarás con imágenes de disco (físicas, probablemente `.E01` o `.dd`) y volcados de memoria (`.raw`, `.vmem`, `.dmp`) que te proporcionarán como parte del escenario. Rara vez tendrás que realizar la adquisición tú mismo, pero entender los formatos y la importancia del hashing es crucial.

## 💿 Adquisición de Disco

Proceso para crear una imagen forense de un medio de almacenamiento (HDD, SSD, USB).

* **Principio Clave:** Utilizar un **bloqueador de escritura (`write-blocker`)** (hardware o software) para conectar el disco original al sistema de adquisición. Esto previene cualquier escritura accidental que altere la evidencia.
* **Herramientas Comunes:**
    * **FTK Imager (AccessData/Exterro):** Herramienta GUI gratuita para Windows muy popular. Permite crear imágenes RAW(dd), E01, montar imágenes, previsualizar archivos. Incluye hashing MD5/SHA1 durante la adquisición. *(Aunque la adquisición se hace vía GUI, conceptualmente realiza la copia sector a sector).*
    * **`dd` / `dcfldd` (Linux CLI):** `dd` es la herramienta clásica de Linux para copia bit a bit. `dcfldd` es una versión mejorada para forense que añade hashing sobre la marcha y mejor feedback.
        * Sintaxis básica `dd`: `sudo dd if=/dev/sdX of=/ruta/destino/imagen.dd bs=4M status=progress` (¡CUIDADO con `if` y `of`!)
        * Sintaxis básica `dcfldd`: `sudo dcfldd if=/dev/sdX hash=md5,sha256 md5log=hash_md5.txt sha256log=hash_sha256.txt of=/ruta/destino/imagen.dd status=on`
    * **Guymager (Linux GUI):** Interfaz gráfica amigable para adquisición en Linux, soporta E01 y dd, incluye hashing y verificación.
* **Proceso General:**
    1.  Conectar disco original vía write-blocker.
    2.  Iniciar herramienta de adquisición.
    3.  Seleccionar disco fuente (`if=/dev/sdX`).
    4.  Seleccionar destino y formato de imagen (`of=/ruta/imagen.e01`).
    5.  Configurar opciones (compresión, tamaño de segmento, metadatos del caso).
    6.  **Activar cálculo de hash (MD5 y/o SHA256) durante la adquisición.**
    7.  Iniciar el proceso.
    8.  **Verificar:** Al finalizar, comparar el hash calculado por la herramienta con un hash calculado manualmente sobre la imagen resultante. Deben coincidir.

## 🧠 Adquisición de Memoria (RAM)

Captura el contenido volátil de la memoria RAM de un sistema *en ejecución*. Debe hacerse en el sistema vivo antes de apagarlo.

* **Importancia:** Contiene datos cruciales: procesos en ejecución (incluso malware oculto), conexiones de red activas/pasadas, comandos ejecutados, claves de registro cargadas, credenciales en memoria, etc.
* **Herramientas Comunes:**
    * **FTK Imager (Windows GUI):** Opción "Capture Memory".
    * **DumpIt (Magnet Forensics - Windows CLI):** Ejecutable simple y rápido.
    * **Belkasoft Live RAM Capturer (Windows GUI/CLI):** Herramienta gratuita.
    * **LiME (Linux Memory Extractor - Linux Kernel Module):** Estándar de facto en Linux. Requiere cargar un módulo del kernel (compilado para la versión específica del kernel o usando versiones precompiladas).
* **Proceso General:**
    1.  Ejecutar la herramienta de captura en el sistema vivo (con privilegios de administrador/root).
    2.  Especificar la ruta de salida para el archivo de volcado (generalmente `.raw`, `.mem`, `.vmem`, `.dmp`).
    3.  Esperar a que finalice la copia.
    4.  Documentar la hora, herramienta usada y cualquier mensaje relevante. Calcular hash del archivo resultante para integridad.

## #️⃣ Hashing e Integridad de la Evidencia

El hashing es **fundamental** en forense digital para verificar la integridad de la evidencia.

* **Propósito:** Asegurar que la copia (imagen) es idéntica al original en el momento de la adquisición y que no ha sido modificada posteriormente.
* **Algoritmos:** Se usan funciones hash criptográficas como **MD5**, **SHA-1** (considerado débil para criptografía, pero aún usado en forense para compatibilidad) y **SHA-256** (preferido).
* **Proceso de Verificación:**
    1.  **Adquisición:** Se calcula un hash del disco/medio original (idealmente) y/o se calcula un hash de la imagen a medida que se crea (ej. en formato E01 o con `dcfldd`).
    2.  **Post-Adquisición:** Se calcula un hash del archivo de imagen resultante. Este hash **debe coincidir** con el hash calculado durante la adquisición.
    3.  **Antes del Análisis:** Cada vez que se va a analizar la imagen, se recomienda **recalcular su hash** y compararlo con el hash original de adquisición para asegurar que no ha habido corrupción o manipulación del archivo de imagen.
* **Herramientas:** FTK Imager (función "Verify Image"), `md5sum`/`sha256sum` (Linux), `Get-FileHash` (PowerShell).

---
