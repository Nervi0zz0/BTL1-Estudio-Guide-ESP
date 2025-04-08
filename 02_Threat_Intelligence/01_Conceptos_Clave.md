# Inteligencia de Amenazas (CTI): Conceptos Clave

La **Inteligencia de Amenazas Cibernéticas (Cyber Threat Intelligence - CTI)** es el conocimiento basado en evidencia (contexto, mecanismos, indicadores, implicaciones y consejos accionables) sobre una amenaza o peligro existente o emergente para los activos. El objetivo principal de la CTI es **informar la toma de decisiones** relacionadas con la ciberseguridad, permitiendo una defensa más proactiva y eficaz.

En el contexto de BTL1, aplicar conceptos de CTI te ayuda a entender *por qué* y *cómo* ocurren los ataques, y no solo a identificar que *algo* malo ha pasado.

## ¿Qué NO es (solo) CTI?

Es importante entender que CTI va más allá de una simple lista de Indicadores de Compromiso (IoCs). Una lista de IPs o hashes maliciosos sin contexto es solo *información* o *datos*. La *inteligencia* requiere **análisis, contexto y relevancia** para ser accionable.

## Tipos de CTI (Niveles)

Aunque existen varios niveles, para BTL1 nos interesan principalmente:

* **Táctica:** Enfocada en IoCs específicos y observables (IPs, dominios, hashes) para la detección y bloqueo inmediato. Es la más común en análisis de alertas diarias.
* **Operacional:** Centrada en las Tácticas, Técnicas y Procedimientos (TTPs) de los atacantes. Busca entender *cómo* operan los adversarios (sus herramientas, métodos de ataque, infraestructura).

(Existe también la CTI Estratégica, más enfocada en riesgos a largo plazo y panorama de amenazas, generalmente para la dirección).

## Indicadores de Compromiso (IoCs)

Son las "huellas digitales" o piezas de evidencia forense que indican que una actividad maliciosa ha ocurrido en un sistema o red. Son el nivel más básico de la inteligencia táctica.

* **Ejemplos Comunes:**
    * Direcciones IP (de C&C, servidores de phishing, escaneo)
    * Nombres de Dominio (maliciosos, DGA)
    * Hashes de Archivos (MD5, SHA1, SHA256 de malware)
    * URLs específicas (phishing, descarga de malware)
    * Direcciones de correo electrónico (del atacante)
    * Nombres de archivo o rutas específicas
    * Claves de registro o Mutex creados por malware

* **Valor:** Útiles para detección rápida y bloqueo, pero relativamente fáciles de cambiar por los atacantes.

## Tácticas, Técnicas y Procedimientos (TTPs)

Representan el *comportamiento* del adversario. Son un nivel más abstracto y valioso de inteligencia operacional:

* **Táctica:** El objetivo o propósito de una acción del atacante (ej. Acceso Inicial, Ejecución, Persistencia, Exfiltración).
* **Técnica:** El método específico utilizado para lograr una táctica (ej. Phishing Spearphishing [T1566.001] para Acceso Inicial, Ejecución de Scripts [T1059] para Ejecución).
* **Procedimiento:** La implementación particular de una técnica por un actor o grupo específico (ej. usar un script PowerShell específico ofuscado para descargar y ejecutar un malware).

* **Valor:** Entender las TTPs permite predecir movimientos futuros, diseñar detecciones más robustas (basadas en comportamiento, no solo en firmas) y comprender mejor al adversario. Son más difíciles de cambiar para los atacantes que los IoCs simples. El framework **MITRE ATT&CK®** es el estándar de facto para catalogar TTPs.

## Actores de Amenaza (Threat Actors)

Son los individuos, grupos u organizaciones responsables de los ciberataques. Comprender (aunque sea a nivel básico) quién podría estar detrás de un ataque ayuda a entender:

* **Motivaciones:** ¿Financiera? ¿Espionaje? ¿Ideológica?
* **Capacidades:** ¿Son sofisticados (APT) o usan herramientas comunes?
* **TTPs Típicas:** Ciertos grupos tienden a reutilizar herramientas o métodos.

En BTL1, no se espera una atribución profunda, pero reconocer patrones asociados a tipos de actores puede ser útil.

## Ciclo de Vida de la Inteligencia (Simplificado)

Incluso en una investigación BTL1, sigues informalmente un ciclo:

1.  **Dirección:** ¿Qué necesito saber? (Definido por el escenario del examen).
2.  **Recolección:** Obtener datos (logs, headers, resultados de herramientas).
3.  **Procesamiento:** Organizar y formatear los datos (ej. extraer IoCs).
4.  **Análisis:** Interpretar los datos, buscar patrones, correlacionar, usar herramientas CTI (VT, OTX...). ¿Qué significa esta IP? ¿Este hash es conocido? ¿Qué TTPs observo?
5.  **Diseminación:** Presentar los hallazgos (en tu informe final).
6.  **Feedback:** (En un entorno real, esto mejora el ciclo futuro).

## La Pirámide del Dolor (Pyramid of Pain)

Creada por David J Bianco, esta pirámide ilustra qué tipos de indicadores son más "dolorosos" (difíciles/costosos) para un atacante si los defensores los bloquean o detectan. Bloquear TTPs causa más "dolor" que bloquear simples hashes o IPs.

* **(Base - Fácil de cambiar):** Hashes -> IPs -> Nombres de Dominio
* **(Medio):** Artefactos de Red (ej. User-Agents) -> Artefactos de Host (ej. nombres de archivo)
* **(Cima - Difícil de cambiar):** Herramientas -> **TTPs**

Comprender esto ayuda a priorizar qué tipo de inteligencia es más valiosa a largo plazo.

---
