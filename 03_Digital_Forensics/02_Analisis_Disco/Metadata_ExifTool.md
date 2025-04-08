# Análisis de Metadatos con ExifTool

## 📑 ¿Qué son los Metadatos?

Los **Metadatos** son, sencillamente, "datos sobre datos". Es información descriptiva sobre un archivo que no forma parte del contenido visible principal, pero que puede revelar mucho sobre su origen, historia y contexto.

Existen varios tipos, pero ExifTool se centra principalmente en los **metadatos embebidos**, que están almacenados *dentro* del propio archivo. Algunos ejemplos comunes incluyen:

* **EXIF (Exchangeable image file format):** Encontrado en imágenes JPEG y TIFF. Incluye detalles de la cámara (modelo, configuración), fecha/hora de captura, y a veces **coordenadas GPS**.
* **IPTC:** Metadatos para imágenes usados en periodismo (títulos, palabras clave, copyright).
* **XMP (Extensible Metadata Platform):** Estándar de Adobe, embebido en PDFs, imágenes, etc.
* **Metadatos de Documentos:** Información de autor, software utilizado, fechas de edición, comentarios (en PDFs, documentos de Office, etc.).
* **Tags de Audio/Video:** ID3 tags en MP3, información en MP4, AVI, etc.

## 🕵️ Valor Forense de los Metadatos

El análisis de metadatos puede proporcionar pistas cruciales en una investigación:

* **Atribución:** Identificar al autor de un documento o la cámara/dispositivo que tomó una foto/video.
* **Geolocalización:** Determinar dónde se tomó una foto o video si contiene coordenadas GPS.
* **Líneas de Tiempo:** Establecer fechas/horas de creación o modificación (que a veces difieren de las del sistema de archivos).
* **Software Utilizado:** Identificar el programa (y su versión) usado para crear o editar un archivo (puede indicar manipulación).
* **Información Oculta:** Encontrar comentarios, anotaciones o revisiones eliminadas dentro de documentos.
* **Consistencia:** Comparar metadatos entre diferentes archivos para encontrar relaciones o inconsistencias.

## <0xE2><0x9A><0x99>️ ExifTool: La Herramienta

**[ExifTool](https://exiftool.org/)** (por Phil Harvey) es una librería Perl y una aplicación de línea de comandos **gratuita y open-source**. Es extremadamente potente y versátil, capaz de leer, escribir y editar metadatos de una **enorme variedad de tipos de archivo** (imágenes, audio, video, PDF, documentos de Office, y muchos más).

* **Plataforma:** Funciona en Windows, macOS y Linux.
* **Fortaleza:** Reconoce una cantidad masiva de tags de metadatos diferentes.

### Uso Básico (Línea de Comandos)

Aquí tienes una tabla con los comandos más comunes para usar ExifTool:

| Tarea/Objetivo                       | Comando                                                     | Notas/Uso                                                                                                 |
| :----------------------------------- | :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| Ver **TODOS** los metadatos          | `exiftool <archivo>`                                        | Muestra toda la información meta encontrada. Útil para exploración inicial y ver nombres de tags disponibles. |
| Ver Metadatos Específicos          | `exiftool -Tag1 -Tag2 <archivo>`                            | Muestra solo los tags solicitados (ej. `-Author`, `-GPSPosition`). Usa `exiftool <archivo>` para ver nombres de tags. |
| Extraer Tags Específicos (Tabla) | `exiftool -T -Tag1 -Tag2 <archivo>`                         | `-T` para salida delimitada por tabuladores, fácil de importar en hojas de cálculo.                     |
| Procesar Directorio Recursivamente  | `exiftool -r <directorio>`                                  | Analiza todos los archivos dentro de un directorio y sus subdirectorios.                                 |
| Guardar Salida a Archivo             | `exiftool <archivo> > output.txt`                           | Redirección estándar de salida para guardar los resultados en un archivo de texto.                       |
| Guardar Meta por Archivo (Txt)     | `exiftool -w txt <directorio>`                              | Crea un archivo `.txt` por cada archivo procesado en `<directorio>`, conteniendo sus metadatos.         |
| **Eliminar** Todos los Metadatos      | `exiftool -all= <archivo>`                                  | **¡CUIDADO!** Elimina toda la metadata. Usar solo en copias si se preserva evidencia. Útil para sanitizar. |
| Mostrar Versión de ExifTool          | `exiftool -ver`                                             | Muestra la versión instalada.                                                                             |

* **Nota sobre Nombres de Tags:** Los nombres de los tags (`TagName`) pueden variar. Usa `exiftool <archivo>` para ver todos los tags disponibles en ese archivo específico o consulta la [documentación de ExifTool Tag Names](https://exiftool.org/TagNames/).

### Interpretación de la Salida

ExifTool generalmente muestra la salida en formato `Nombre del Tag : Valor`. Es importante entender qué significa cada tag relevante para tu investigación (ej. diferenciar entre `CreateDate` del archivo y `DateTimeOriginal` de la cámara en una foto).

### Consideraciones Forenses

* Ejecuta ExifTool sobre copias de la evidencia siempre que sea posible.
* Documenta los comandos utilizados y los metadatos relevantes encontrados.
* Ten en cuenta que algunos metadatos pueden ser fácilmente modificados por un usuario. Compara con otras fuentes de evidencia si es posible.

---
*ExifTool es una navaja suiza indispensable para extraer información oculta dentro de los propios archivos, complementando el análisis del sistema de archivos.*
