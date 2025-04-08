# Recursos y Práctica para Análisis de Red (PCAP)

El análisis de tráfico de red, especialmente de archivos PCAP, es una habilidad que mejora enormemente con la práctica sobre datos reales o de desafíos. Aquí tienes recursos clave:

## 💾 Fuentes de Archivos PCAP para Práctica

* **[Malware-Traffic-Analysis.net](https://www.malware-traffic-analysis.net/)**:
    * **¡Recurso Imprescindible!** El blog de Brad Duncan contiene análisis detallados de campañas de malware reales, y lo más importante, **proporciona los archivos PCAP asociados** para que puedas analizarlos tú mismo. A menudo incluye ejercicios o preguntas para guiar tu análisis.

* **[Wireshark Sample Captures Wiki](https://wiki.wireshark.org/SampleCaptures)**:
    * La wiki oficial de Wireshark tiene una página que recopila una gran variedad de capturas de muestra para diferentes protocolos y escenarios (tráfico normal, problemas, algunos ataques).

* **[NETRESEC - Publicly Available PCAP Files](https://www.netresec.com/?page=PcapFiles)**:
    * Página mantenida por Erik Hjelmvik (creador de NetworkMiner) que lista archivos PCAP disponibles públicamente, a menudo provenientes de CTFs, ejercicios de entrenamiento o investigaciones.

* **[Digital Corpora](https://digitalcorpora.org/)**:
    * (Mencionado en Forense) También aloja conjuntos de datos de tráfico de red, a veces relacionados con sus imágenes de disco.

* **[PacketTotal](https://packettotal.com/)**:
    * Es un analizador de PCAP online (similar a VirusTotal pero para PCAPs), pero también funciona como un repositorio donde los usuarios suben capturas. Puedes explorar PCAPs subidos por otros (¡con precaución, verifica la fuente y el contenido!).

## 💻 Plataformas CTF y Laboratorios

Muchas plataformas de desafíos incluyen análisis de PCAP como parte de sus escenarios:

* **[CyberDefenders](https://cyberdefenders.org/)**, **[Blue Team Labs Online (BTLO)](https://blueteamlabs.online/)**, **[TryHackMe](https://tryhackme.com/)**, **[Hack The Box (Sherlocks)](https://app.hackthebox.com/sherlocks)**: Busca desafíos etiquetados como "Network Forensics", "PCAP Analysis" o escenarios de respuesta a incidentes que seguramente requerirán que examines tráfico de red.

## 🛠️ Documentación de Herramientas

* **[Wireshark User's Guide & Wiki](https://www.wireshark.org/docs/)**
* **Páginas `man` de Tshark** (ejecuta `man tshark` en Linux) o documentación online de Wireshark.

## 📰 Blogs, Canales y Aprendizaje Continuo

* **[Malware-Traffic-Analysis.net Blog](https://www.malware-traffic-analysis.net/blog-entries.html)**: (De nuevo) Los write-ups son tan valiosos como los PCAPs.
* **[Wireshark Blog](https://blog.wireshark.org/)** y **[SharkFest Presentations](https://sharkfestus.wireshark.org/retrospective)** (Busca en YouTube): Presentaciones de la conferencia anual de Wireshark, a menudo con análisis profundos.
* **[SANS ISC Handlers Diary](https://isc.sans.edu/)**: A veces incluyen análisis rápidos de PCAPs relacionados con amenazas actuales.
* **Canales de YouTube:** Busca canales como **13Cubed**, **Chris Greer**, **Packet Bomb** que a menudo tienen tutoriales sobre Wireshark y análisis de paquetes.

---
*Analizar PCAPs de escenarios reales o de CTFs es la mejor manera de familiarizarse con cómo se ve el tráfico normal vs. el malicioso y de ganar velocidad usando Wireshark/tshark.*
