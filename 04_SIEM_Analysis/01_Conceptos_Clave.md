# Análisis SIEM: Conceptos Clave

## 📊 ¿Qué es un SIEM?

Un **SIEM (Security Information and Event Management)** es una solución tecnológica que **agrega, normaliza y analiza datos de logs** generados por múltiples fuentes dentro de la infraestructura de TI de una organización (servidores, firewalls, endpoints, aplicaciones, dispositivos de red, etc.).

**Objetivo Principal:** Proporcionar una **visibilidad centralizada** sobre los eventos de seguridad, permitiendo la **detección temprana de amenazas**, la **investigación de incidentes** y el **cumplimiento normativo** a través del análisis y correlación de estos datos.

## ✨ Funciones Clave de un SIEM

Un SIEM moderno integra varias funciones esenciales:

1.  **Agregación de Logs (`Log Aggregation`):**
    * Recopila logs de una amplia variedad de fuentes heterogéneas en un repositorio centralizado. Fuentes comunes incluyen: Windows Event Logs, Linux syslog, logs de Firewall, Proxy, VPN, DNS, DHCP, Antivirus/EDR, aplicaciones web, bases de datos, logs de Cloud (AWS CloudTrail, Azure Activity Log, etc.).

2.  **Normalización (`Normalization`):**
    * Los logs vienen en formatos muy diferentes. La normalización **parsea** (analiza y extrae) estos logs y los convierte a un **formato común y estructurado**. Esto implica identificar y etiquetar campos clave (timestamp, IP origen, IP destino, usuario, acción, etc.), lo cual es fundamental para poder buscar y correlacionar datos de diferentes fuentes de manera efectiva.

3.  **Correlación (`Correlation`):**
    * Es el "cerebro" del SIEM. Busca **relaciones y patrones** entre eventos de diferentes fuentes que, aisladamente, podrían no parecer sospechosos, pero juntos indican una posible amenaza. Se basa en **reglas de correlación** predefinidas o personalizadas (ej. "Múltiples logins fallidos desde una IP seguidos de un login exitoso" o "Comunicación desde un servidor interno a una IP de C&C conocida").

4.  **Alertas (`Alerting`):**
    * Cuando una regla de correlación se cumple o se detecta un evento crítico predefinido, el SIEM genera una **alerta** para notificar al equipo de seguridad (SOC) para su investigación.

5.  **Dashboards y Visualización:**
    * Presenta la información de seguridad de forma gráfica y resumida a través de **dashboards** (paneles de control), gráficos y tablas. Permite monitorizar el estado de la seguridad, ver tendencias, alertas activas y métricas clave de un vistazo.

6.  **Retención de Logs (`Log Retention`):**
    * Almacena los logs (crudos y/o normalizados) durante un período de tiempo definido. Esto es crucial para cumplir con normativas (ej. PCI DSS, GDPR) y para poder realizar investigaciones forenses sobre incidentes pasados.

7.  **Búsqueda y Análisis (`Querying`):**
    * Ofrece un lenguaje de consulta potente (como SPL en Splunk, KQL en Elastic/Sentinel) que permite a los analistas buscar terabytes de datos de logs de forma eficiente para investigar alertas, realizar **threat hunting** (búsqueda proactiva de amenazas) y generar informes personalizados.

## ✅ ¿Por Qué es Importante un SIEM?

* **Visibilidad Centralizada:** Rompe los silos de logs y ofrece una visión unificada.
* **Detección Rápida:** Reduce el tiempo para detectar incidentes mediante correlación y alertas.
* **Respuesta a Incidentes Eficaz:** Proporciona un punto central para investigar qué ocurrió, cuándo y cómo.
* **Cumplimiento Normativo:** Ayuda a cumplir requisitos de auditoría y retención de logs.
* **Threat Hunting:** Permite buscar proactivamente amenazas que no hayan generado alertas.

## 🎯 Enfoque BTL1

En escenarios como BTL1, es muy probable que te encuentres con una interfaz SIEM (frecuentemente **Splunk**) que contiene logs relevantes para un incidente. Deberás usar las capacidades de búsqueda y filtrado del SIEM para encontrar evidencia específica, responder preguntas y reconstruir la actividad del atacante basándote en los logs disponibles. Se espera que sepas cómo construir consultas básicas y filtrar grandes cantidades de datos para encontrar la información pertinente.

---
*Entender estas funciones clave te ayudará a comprender qué esperar de un SIEM y cómo abordar el análisis de logs en plataformas como Splunk o Elastic Stack.*
