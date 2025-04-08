# Recursos y Práctica para SIEM y Análisis de Logs

El análisis de logs en un SIEM es una habilidad que se perfecciona con la práctica. Aquí tienes recursos para entrenar con Splunk, explorar otras plataformas y encontrar conjuntos de datos para analizar:

## 📚 Recursos Específicos de Splunk

* **[Documentación Oficial de Splunk](https://docs.splunk.com/Documentation/Splunk)**:
    * Imprescindible. Consulta el **Search Manual** y el **SPL Reference** para entender todos los comandos y funciones.

* **[Formación Gratuita de Splunk](https://www.splunk.com/en_us/training/free-courses/overview.html)**:
    * Splunk ofrece cursos gratuitos online. El **"Splunk Fundamentals 1"** es un excelente punto de partida para familiarizarse con la interfaz y SPL básico.

* **[Splunk Boss of the SOC (BOTS)](https://www.splunk.com/en_us/campaigns/boss-of-the-soc.html)**:
    * Es un CTF (Capture The Flag) creado por Splunk. Publican los conjuntos de datos (`datasets`) de versiones anteriores (v1, v2, v3...). Puedes descargar estos datasets e ingerirlos en una instancia gratuita de Splunk para practicar resolviendo las preguntas del CTF. Busca "Splunk BOTS dataset download" para encontrar versiones disponibles.

## 🧅 Plataformas SIEM/Log Analysis para Laboratorio

Montar tu propio entorno es una gran forma de aprender.

* **[Security Onion](https://securityonionsolutions.com/)**:
    * Distribución Linux **gratuita y open source** diseñada para threat hunting, monitorización de seguridad y gestión de logs. Incluye el Elastic Stack (Elasticsearch, Logstash, Kibana) o Wazuh como SIEM, además de NIDS (Suricata, Zeek) y otras herramientas. Ideal para un home lab.

* **[Wazuh](https://wazuh.com/)**:
    * Plataforma **open source** de seguridad unificada (XDR y SIEM). Ofrece detección de intrusiones basada en host, monitorización de integridad de archivos, análisis de logs y más. Se puede integrar con Elastic Stack.

* **[Elastic Stack (ELK)](https://www.elastic.co/es/training/free)**:
    * Si quieres explorar la alternativa a Splunk, Elastic ofrece formación gratuita sobre su stack (Elasticsearch, Kibana) y su lenguaje de consulta KQL (Kibana Query Language).

## 💾 Conjuntos de Datos Públicos para Práctica

Analizar datos pre-generados de ataques simulados es muy útil.

* **[Mordor Datasets (SpecterOps)](https://github.com/hunters-forge/mordor)**:
    * Colección de conjuntos de datos de seguridad pre-grabados basados en la ejecución de técnicas adversarias específicas, **mapeadas a MITRE ATT&CK**. Disponibles en formatos JSON, CSV, EVTX, etc., listos para importar en Splunk/Elastic.

* **[EVTX-ATTACK-SAMPLES (SBousseaden)](https://github.com/sbousseaden/EVTX-ATTACK-SAMPLES)**:
    * Repositorio con muestras de logs de eventos de Windows (`.evtx`) relacionados con técnicas específicas de ATT&CK. Útil para practicar la detección de artefactos concretos.

* **Búsqueda General:** Busca online "sample security logs", "sysmon logs dataset", "apache access log sample", etc., para encontrar datos específicos de diferentes fuentes.

## 💻 Plataformas CTF (Reiteración)

* **[CyberDefenders](https://cyberdefenders.org/)**, **[Blue Team Labs Online (BTLO)](https://blueteamlabs.online/)**, **[TryHackMe](https://tryhackme.com/)**: Sus desafíos frecuentemente incluyen análisis de logs en Splunk, Elastic u otros formatos como parte central de la investigación.

## 📰 Blogs y Aprendizaje Continuo

* **[Splunk Blogs (Security)](https://www.splunk.com/en_us/blog/platform.html)**: Artículos sobre uso de Splunk para casos de uso de seguridad.
* **Blogs de Detección/Threat Hunting:** Sigue blogs que se centren en crear detecciones en SIEM y buscar amenazas (ej. SpecterOps, Red Canary, Nasreddine Bencherchali, Olaf Hartong).

---
*La mejor manera de dominar el análisis SIEM es pasar tiempo buscando en datos reales o simulados, probando diferentes consultas y aprendiendo a interpretar los resultados.*
