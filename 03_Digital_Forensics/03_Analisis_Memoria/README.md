# Análisis Forense de Memoria (RAM)

El **Análisis Forense de Memoria**, también conocido como análisis de memoria volátil o análisis de RAM, es el proceso de examinar el contenido de la memoria de acceso aleatorio (RAM) de un sistema informático, generalmente a partir de un archivo de **volcado de memoria** (`memory dump`) adquirido previamente.

A diferencia del análisis de disco que examina datos "en reposo", el análisis de memoria captura una **instantánea del estado del sistema en ejecución** en el momento de la adquisición. Esta información es extremadamente volátil y se pierde al apagar el sistema.

## <0xE2><0x9A><0xA1> ¿Por Qué es Importante el Análisis de Memoria?

El análisis de memoria es vital por varias razones:

1.  **Captura Datos Volátiles:** Obtiene información que no siempre se escribe en disco, como:
    * Procesos en ejecución (incluidos los ocultos o maliciosos).
    * Conexiones de red activas y recientes.
    * Comandos ejecutados en terminales.
    * Controladores (drivers) cargados.
    * Claves de registro cargadas en memoria.
    * Contenido del portapapeles.
2.  **Detecta Malware Avanzado:** Es especialmente útil contra:
    * **Malware sin archivo (`fileless`):** Que se ejecuta directamente en memoria sin dejar un archivo ejecutable en disco.
    * **Malware ofuscado o empaquetado (`packed`):** A menudo, el malware se desempaqueta en memoria, revelando su código real.
    * **Rootkits:** Que pueden ocultar procesos o archivos en el sistema operativo pero ser visibles en un volcado de memoria.
3.  **Revela Actividad Reciente:** Puede mostrar qué estaba haciendo un usuario o un proceso justo antes de la adquisición.
4.  **Recupera Artefactos Sensibles:** A veces es posible encontrar credenciales (contraseñas, hashes), claves de cifrado o fragmentos de archivos/comunicaciones que residían temporalmente en memoria.
5.  **Complementa el Análisis de Disco:** Proporciona contexto de ejecución para los artefactos encontrados en disco. ¿Estaba este ejecutable malicioso realmente corriendo? ¿Con qué parámetros? ¿A qué se conectó?

## 챌린지 Desafíos del Análisis de Memoria

* **Volatilidad Extrema:** Los datos cambian constantemente. La adquisición debe ser rápida y en el momento adecuado.
* **Adquisición Delicada:** Debe realizarse en el sistema vivo, lo que puede alterar ligeramente el estado y requiere herramientas específicas (ver [`01_Adquisicion.md`](../01_Adquisicion.md)). Existe el riesgo de volcados corruptos o incompletos.
* **Complejidad del Análisis:** Requiere herramientas especializadas (como Volatility) y un buen entendimiento de cómo el sistema operativo gestiona la memoria y sus estructuras internas.

## 🔄 Proceso General de Análisis

1.  **Adquisición:** Obtener el volcado de memoria (`.raw`, `.dmp`, `.vmem`, etc.) del sistema objetivo.
2.  **Identificación del Perfil (`Profile`):** Determinar el sistema operativo exacto, la arquitectura (x86/x64) y a veces el Service Pack o build del volcado. Esto es **crucial** para que la herramienta de análisis interprete correctamente las estructuras de datos en memoria.
3.  **Ejecución de Plugins:** Utilizar una herramienta como Volatility con sus diversos plugins para extraer información específica (lista de procesos, conexiones de red, hives del registro, etc.).
4.  **Análisis de Resultados:** Interpretar la salida de los plugins para encontrar evidencia de actividad maliciosa o relevante para la investigación.
5.  **Correlación:** Comparar los hallazgos de la memoria con la evidencia obtenida del análisis de disco y de red.

## 📂 Estructura de esta Subsección

Dentro de esta carpeta (`03_Analisis_Memoria`), exploraremos:

* **[Conceptos Clave](./Conceptos_Clave.md):** Qué tipo de información específica podemos encontrar en la memoria.
* **[Herramienta Volatility](./Herramienta_Volatility.md):** Cómo usar el framework Volatility (el estándar de facto) para realizar el análisis.

## 🎯 Enfoque BTL1

El análisis de memoria es una parte frecuente e importante de los escenarios BTL1. Se espera que los candidatos sean capaces de usar Volatility para extraer información clave de un volcado de memoria proporcionado y responder a preguntas sobre procesos, redes y otros artefactos en memoria.

---
*El análisis de memoria abre una ventana única al estado de ejecución de un sistema, revelando actividad que de otro modo sería invisible.*
