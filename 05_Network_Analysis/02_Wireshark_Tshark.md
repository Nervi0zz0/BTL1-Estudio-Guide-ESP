# Herramientas Clave: Wireshark y Tshark

**[Wireshark](https://www.wireshark.org/)** es el analizador de protocolos de red más utilizado y reconocido a nivel mundial. Es una herramienta **open source**, multiplataforma (Windows, Linux, macOS) con una potente interfaz gráfica (GUI) que permite capturar tráfico en vivo o analizar archivos PCAP existentes en gran detalle.

**Tshark** es la **versión de línea de comandos (CLI)** de Wireshark. Comparte el mismo motor de disección de protocolos y es ideal para scripting, análisis automatizado, captura remota o extracción rápida de información sin cargar la GUI.

## 🦈 Wireshark (Interfaz Gráfica - GUI)

La interfaz de Wireshark puede parecer abrumadora al principio, pero sus componentes principales son:

1.  **Barra de Filtro de Visualización (`Display Filter`):** **Fundamental.** Aquí introduces filtros para aislar el tráfico que te interesa ver.
2.  **Panel de Lista de Paquetes (`Packet List`):** Muestra un resumen de cada paquete capturado/cargado (número, tiempo, IPs origen/destino, protocolo, info breve). Las filas se colorean según reglas para facilitar la identificación.
3.  **Panel de Detalles del Paquete (`Packet Details`):** Vista jerárquica y expandible que muestra todas las capas y campos decodificados del paquete seleccionado en la lista. Aquí examinas las cabeceras Ethernet, IP, TCP/UDP, y de aplicación (HTTP, DNS, etc.).
4.  **Panel de Bytes del Paquete (`Packet Bytes`):** Muestra el contenido crudo del paquete seleccionado en formato hexadecimal y ASCII.

### Funcionalidades Clave para Análisis Forense/Seguridad:

* **Abrir Archivos PCAP:** `File > Open`.
* **Aplicar Filtros de Visualización:** Escribir expresiones en la barra de filtro para mostrar solo paquetes específicos (ej. `ip.addr == 192.168.1.1`, `http.request.method == "POST"`, `dns.qry.name contains "evil"`). La sintaxis se detalla en `03_Cheatsheet_Filtros.md`.
* **Seguir Conversaciones (`Follow Stream`):** **¡Esencial!** Haz clic derecho sobre un paquete de una conversación TCP, UDP, TLS, HTTP, etc., y selecciona `Follow > [Protocolo] Stream`. Wireshark reconstruirá y mostrará los datos intercambiados entre los dos endpoints en una ventana separada, facilitando la lectura de payloads de aplicación (ej. ver una petición y respuesta HTTP completa).
* **Estadísticas (`Statistics` Menu):** Muy útil para obtener resúmenes:
    * `Conversations`: Lista todas las conversaciones (Ethernet, IP, TCP, UDP) con IPs, puertos, duración, bytes.
    * `Endpoints`: Lista todos los endpoints (MACs, IPs, puertos TCP/UDP) vistos en la captura.
    * `Protocol Hierarchy`: Muestra qué porcentaje del tráfico corresponde a cada protocolo.
    * `HTTP`: Estadísticas específicas de HTTP (ej. `Requests`, `Packet Counter`).
* **Exportar Objetos (`File > Export Objects`):** Permite extraer archivos transferidos a través de ciertos protocolos como HTTP, SMB, DICOM, FTP. Muy útil para recuperar malware descargado o datos exfiltrados.
* **Buscar Paquetes (`Edit > Find Packet`):** Permite buscar paquetes que contengan una cadena específica (en formato string, hex, regex) o que coincidan con un filtro de visualización.

## 🦈 Tshark (Línea de Comandos - CLI)

Ofrece la misma potencia de análisis que Wireshark pero desde la terminal.

### Usos Comunes:

* **Captura de Tráfico:**
    ```bash
    # Capturar en interfaz eth0 y guardar en out.pcap (requiere permisos)
    sudo tshark -i eth0 -w out.pcap

    # Capturar solo tráfico del host 1.1.1.1 (filtro de captura BPF)
    sudo tshark -i eth0 -f "host 1.1.1.1" -w out.pcap
    ```
    * `-i <interfaz>`: Especifica la interfaz de red.
    * `-w <archivo>`: Escribe la captura cruda en un archivo PCAP.
    * `-f "<filtro_captura>"`: Aplica un filtro de captura (sintaxis BPF).

* **Lectura y Filtrado de PCAPs:**
    ```bash
    # Leer archivo.pcap y mostrar solo peticiones HTTP (filtro de visualización)
    tshark -r archivo.pcap -Y "http.request"

    # Leer y contar paquetes que coincidan con el filtro
    tshark -r archivo.pcap -Y "ip.addr == 10.0.0.5" -c 50
    ```
    * `-r <archivo>`: Lee desde un archivo PCAP.
    * `-Y "<filtro_visualizacion>"`: Aplica un filtro de visualización (sintaxis Wireshark).
    * `-c <N>`: Limita la lectura a N paquetes.

* **Extracción de Campos Específicos:** **¡Muy potente para scripting!**
    ```bash
    # Extraer IP origen, IP destino y URI de peticiones HTTP
    tshark -r archivo.pcap -Y "http.request" -T fields -e ip.src -e ip.dst -e http.request.uri

    # Extraer hora, IP origen/destino y nombre de consulta DNS
    tshark -r archivo.pcap -Y "dns.qry.name" -T fields -e frame.time -e ip.src -e ip.dst -e dns.qry.name
    ```
    * `-T fields`: Especifica el formato de salida como campos.
    * `-e <campo>`: Especifica el campo a extraer (puedes usar `tshark -G fields` para ver todos los campos disponibles).

* **Estadísticas:**
    ```bash
    # Mostrar estadísticas de conversaciones TCP
    tshark -r archivo.pcap -q -z conv,tcp

    # Mostrar jerarquía de protocolos
    tshark -r archivo.pcap -q -z io,phs
    ```
    * `-q`: Modo "quiet" (suprime salida de paquetes, útil con `-z`).
    * `-z <estadistica>`: Calcula diversas estadísticas (ver `tshark -z help`).

## ⚠️ Filtros de Captura vs. Filtros de Visualización

Es importante distinguirlos:

* **Filtros de Captura (`Capture Filters`):**
    * Usados por `tcpdump`, `tshark -f`.
    * Sintaxis **BPF (Berkeley Packet Filter)** (ej. `host 1.1.1.1`, `port 80`, `tcp and dst host 2.2.2.2`).
    * Se aplican **antes** de guardar los paquetes. Solo los paquetes que coinciden se guardan en el PCAP. Son menos flexibles pero más eficientes para reducir el tamaño de la captura en vivo.
* **Filtros de Visualización (`Display Filters`):**
    * Usados por **Wireshark (barra de filtro)** y `tshark -Y`.
    * Sintaxis **propia de Wireshark**, mucho más rica y consciente de los protocolos (ej. `http.request.method == "POST"`, `dns.flags.response == 0`).
    * Se aplican **después** de la captura, sobre el archivo PCAP ya existente. Simplemente **ocultan o muestran** paquetes, no los eliminan del archivo. Son los que usarás principalmente para análisis en BTL1.

## 🎯 Enfoque BTL1

Para BTL1, se espera que domines el uso de **Wireshark (GUI)** para:
* Abrir y navegar PCAPs.
* Aplicar **filtros de visualización** eficazmente para encontrar tráfico relevante.
* **Seguir streams** (TCP/UDP/HTTP) para reconstruir conversaciones.
* **Exportar objetos** (archivos) transferidos vía HTTP/SMB si es necesario.
* Utilizar el panel de **detalles del paquete** para examinar cabeceras específicas.

Conocer `tshark` es una ventaja para procesar rápidamente grandes archivos o extraer información específica, pero el dominio de la GUI de Wireshark suele ser suficiente.

---
*Wireshark y tshark son herramientas indispensables para cualquier persona que trabaje con redes. Dedica tiempo a familiarizarte con la interfaz de Wireshark y la sintaxis de los filtros de visualización.*
