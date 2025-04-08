# File Carving con Scalpel

## 🔪 ¿Qué es File Carving?

**File Carving** es una técnica para **recuperar archivos** buscando directamente sus datos, sin usar la información del sistema de archivos (que podría estar dañada o borrada).

* **¿Cuándo se usa?:** Principalmente para buscar archivos eliminados en el espacio "vacío" (no asignado) de un disco, o en discos con sistemas de archivos corruptos.
* **¿Cómo funciona?:** Busca patrones conocidos que marcan el inicio (cabecera) y a veces el final (pie de página) de tipos de archivo comunes (como JPG, PDF, DOCX).
* **Limitaciones:** No recupera nombres ni fechas originales. Los archivos recuperados pueden estar incompletos o corruptos.

## <0xF0><0x9F><0xAA><0x9A> Scalpel: La Herramienta

**Scalpel** es una herramienta popular **open source** de línea de comandos (Linux/WSL) para hacer file carving.

### El Archivo de Configuración: `scalpel.conf`

* **¡Esencial!:** Scalpel necesita este archivo para saber qué buscar. Sin configurarlo, no encontrará nada útil.
* **Ubicación:** Normalmente en `/etc/scalpel/scalpel.conf` (puedes copiarlo y modificar tu copia).
* **¿Qué hacer?:** Tienes que **editar** el archivo `scalpel.conf` y **descomentar** (quitar el `#` del inicio) las líneas de los tipos de archivo que quieres buscar (JPG, PDF, DOCX, etc.). Cada línea define cómo reconocer un tipo de archivo por su cabecera/pie.

    **Ejemplo (buscar JPG):** Asegúrate de que la línea para `jpg` no tenga un `#` al principio:
    ```
    # Esta línea está comentada (ignorada):
    # jpg	y	200000000	\xff\xd8\xff\xe0\x00\x10\x4a\x46	\xff\xd9

    # Esta línea está descomentada (activa):
    jpg	y	200000000	\xff\xd8\xff\xe0\x00\x10\x4a\x46	\xff\xd9
    ```

### Uso Básico (Línea de Comandos)

Aquí tienes los comandos principales para ejecutar Scalpel en formato de tabla:

| Comando                                                                       | Descripción                                                                                              |
| :---------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------- |
| `scalpel -c <config> -o <output> <image>`                                     | Ejecución básica usando archivo `<config>`, guardando en `<output>`, analizando `<image>`.                 |
| `scalpel -b -c <config> -o <output> <image>`                                  | **Recomendado (imágenes disco):** Igual, pero `-b` busca solo en espacio no asignado.                    |

**Parámetros y Opciones Clave:**

* `-c <config>`: **Ruta** a tu archivo `scalpel.conf` editado (¡Esencial!).
* `-o <output>`: **Ruta** al directorio donde se guardarán los resultados (¡Debe ser nuevo o estar vacío!).
* `-b`: (Opcional) Le dice a Scalpel que busque **solo** en el espacio no asignado de la imagen. Muy útil para imágenes de disco.
* `<image>`: **Ruta** al archivo de imagen forense (ej. `imagen.dd`) o dispositivo (ej. `/dev/sdb`).

**Ejemplo Práctico Combinado:**

Para buscar JPGs y PDFs (definidos en `mi_scalpel.conf`) solo en el espacio no asignado de `imagen_disco.dd` y guardar los resultados en `/mnt/forense/scalpel_out`:

```bash
scalpel -b -c /etc/scalpel/mi_scalpel.conf -o /mnt/forense/scalpel_out /media/forense/imagen_disco.dd
