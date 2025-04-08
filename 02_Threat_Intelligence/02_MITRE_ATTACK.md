# El Framework MITRE ATT&CK®

El framework **MITRE ATT&CK®** se ha convertido en el estándar de facto para describir y categorizar el comportamiento de los adversarios cibernéticos. Es una **base de conocimiento globalmente accesible**, curada por [The MITRE Corporation](https://www.mitre.org/), que se basa en **observaciones del mundo real** de ciberataques.

ATT&CK significa **A**dversarial **T**actics, **T**echniques, and **C**ommon **K**nowledge (Tácticas, Técnicas y Conocimiento Común del Adversario).

## 🏗️ Estructura del Framework: Las Matrices

ATT&CK organiza el comportamiento del adversario en matrices. La más relevante para BTL1 (y la mayoría de los entornos empresariales) es la **Matriz Enterprise**, que cubre plataformas como Windows, macOS, Linux, Redes, Cloud, etc.

La matriz se estructura así:

* **Tácticas (Columnas):** Representan el **objetivo táctico** o la razón detrás de una acción del adversario. Son las categorías de alto nivel del ciclo de vida de un ataque. Las tácticas clave en la Matriz Enterprise incluyen:
    * `Reconnaissance` (Reconocimiento): Recopilación de información sobre el objetivo.
    * `Resource Development` (Desarrollo de Recursos): Preparación de infraestructura o herramientas.
    * `Initial Access` (Acceso Inicial): Cómo entra el atacante en la red (ej. Phishing).
    * `Execution` (Ejecución): Ejecución de código malicioso.
    * `Persistence` (Persistencia): Mantener el acceso a lo largo del tiempo (reinicios, cambios de credenciales).
    * `Privilege Escalation` (Escalada de Privilegios): Obtener permisos más elevados.
    * `Defense Evasion` (Evasión de Defensas): Evitar ser detectado.
    * `Credential Access` (Acceso a Credenciales): Robar nombres de usuario y contraseñas.
    * `Discovery` (Descubrimiento): Entender el entorno interno de la red/sistema.
    * `Lateral Movement` (Movimiento Lateral): Moverse de un sistema a otro dentro de la red.
    * `Collection` (Recolección): Reunir datos de interés para el objetivo final.
    * `Command and Control` (Comando y Control - C2): Comunicación con la infraestructura del atacante.
    * `Exfiltration` (Exfiltración): Robo de datos fuera de la red.
    * `Impact` (Impacto): Destrucción, interrupción o manipulación de sistemas/datos.

* **Técnicas (Celdas):** Representan **cómo** un adversario logra un objetivo táctico. Cada técnica tiene un ID único (ej. `T1566 Phishing`). Describen una acción específica.

* **Sub-técnicas (Detalles de Técnicas):** Representan una forma más específica de implementar una técnica. Tienen IDs con punto decimal (ej. `T1566.001 Spearphishing Attachment`, `T1566.002 Spearphishing Link`). No todas las técnicas tienen sub-técnicas.

* **Procedimientos (Ejemplos):** Son las implementaciones específicas de técnicas y sub-técnicas observadas en el mundo real por grupos de actores de amenaza (APTs, grupos de crimen) o malware específico. ATT&CK documenta estos procedimientos como ejemplos.

## ✅ ¿Por Qué es Útil ATT&CK para Blue Team / BTL1?

ATT&CK proporciona un valor inmenso para los defensores:

1.  **Lenguaje Común:** Ofrece una taxonomía estándar para describir las acciones de los atacantes, facilitando la comunicación y el análisis.
2.  **Contextualización de Incidentes:** Permite mapear la evidencia encontrada durante una investigación (logs, artefactos) a TTPs conocidas, ayudando a entender qué está haciendo el atacante y qué podría hacer después.
3.  **Guía para la Investigación:** Al identificar una TTP, puedes consultar en ATT&CK qué artefactos buscar o qué otras técnicas suelen usar atacantes que emplean esa TTP.
4.  **Mejora de Detecciones:** Ayuda a diseñar reglas de detección (ej. en SIEM) basadas en comportamientos (TTPs) en lugar de solo en IoCs frágiles.
5.  **Evaluación de Cobertura:** Permite analizar qué TTPs cubren (o no) tus defensas actuales.
6.  **Enriquecimiento de CTI:** Vincula IoCs con comportamientos (TTPs) para una inteligencia más robusta.

## 🚀 ¿Cómo Usar ATT&CK en la Práctica?

* **Sitio Web Oficial:** El recurso principal es [https://attack.mitre.org/](https://attack.mitre.org/). Puedes navegar por las matrices, buscar técnicas, leer descripciones detalladas, ver ejemplos de procedimientos y referencias.
* **ATT&CK Navigator:** Una herramienta web ([https://mitre-attack.github.io/attack-navigator/](https://mitre-attack.github.io/attack-navigator/)) para visualizar las matrices, crear "capas" (layers) para anotar, colorear técnicas (ej. marcar las observadas en un incidente, las que tienes detectadas), comparar capas, etc. Muy útil para análisis y presentaciones.
* **Mapeo de Evidencia a TTPs (Proceso Mental):**
    1.  **Observa:** Encuentras un artefacto o actividad sospechosa (ej. un nuevo servicio de Windows creado que ejecuta un script sospechoso).
    2.  **Busca/Consulta ATT&CK:** Busca en el sitio de ATT&CK palabras clave relevantes ("Windows service", "persistence", "service creation").
    3.  **Identifica la Técnica:** Encuentras `T1543.003 Create or Modify System Process: Windows Service`.
    4.  **Entiende el Contexto:** La página de la técnica te dice que pertenece a las tácticas de `Persistence` y `Privilege Escalation`. Te da detalles sobre cómo se usa, ejemplos y posibles mitigaciones/detecciones.
    5.  **Documenta:** Anota la TTP identificada en tus notas/informe para dar contexto a tu hallazgo.

## Relevance para BTL1

En el examen BTL1, aunque no siempre se pida explícitamente mapear cada hallazgo a una TTP de ATT&CK, hacerlo (cuando sea relevante y tengas confianza en la identificación) demuestra una comprensión más profunda del comportamiento del atacante. Puede añadir un gran valor a tu informe final al explicar *por qué* un artefacto específico es importante en el contexto de un ataque más amplio.

---
