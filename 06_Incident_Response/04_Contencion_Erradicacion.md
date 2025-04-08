# Contención y Erradicación: Conceptos Clave

Una vez que un incidente ha sido identificado y analizado (al menos inicialmente), las siguientes fases críticas en el ciclo de respuesta a incidentes (PICERL) son la Contención y la Erradicación.

##  containment_zone Contención (`Containment`)

* **Objetivo Principal:** Detener la propagación del incidente y limitar el daño lo más rápido posible. Se trata de "parar la hemorragia".
* **Importancia:** Evita que más sistemas se vean comprometidos, que se exfiltren más datos o que el atacante cause más impacto. Es una fase donde el tiempo suele ser crítico.
* **Estrategias Comunes (Ejemplos):**
    * **Aislamiento de Sistemas Afectados:**
        * Desconectar el cable de red.
        * Deshabilitar el adaptador de red virtual/físico.
        * Mover el sistema a una VLAN de cuarentena con reglas de firewall restrictivas.
        * Aplicar reglas de firewall en el host o en la red para bloquear comunicaciones específicas.
    * **Bloqueo de Indicadores:**
        * Bloquear direcciones IP o dominios maliciosos conocidos (C2, phishing) en el firewall perimetral, proxy o servidor DNS interno (sinkholing).
    * **Deshabilitación de Cuentas:**
        * Desactivar inmediatamente las cuentas de usuario o servicio que se sabe o sospecha que están comprometidas.
    * **Detención de Procesos:**
        * Terminar procesos maliciosos identificados en los sistemas afectados (aunque esto es volátil y el malware puede tener mecanismos para reiniciarse).
* **Consideraciones:** Requiere una toma de decisiones rápida y sopesar el impacto de la contención en las operaciones del negocio. A veces se aplica una contención a corto plazo (rápida y sucia) seguida de una estrategia a largo plazo más planificada.

## broom Erradicación (`Eradication`)

* **Objetivo Principal:** Eliminar por completo la causa raíz del incidente y todos los artefactos maliciosos del entorno afectado para asegurar que el atacante no pueda volver a entrar fácilmente.
* **Importancia:** Asegura una limpieza completa antes de restaurar los servicios. Si no se erradica correctamente, el incidente puede resurgir.
* **Estrategias Comunes (Ejemplos):**
    * **Eliminación de Malware:** Borrar archivos ejecutables, scripts, DLLs maliciosas identificadas durante el análisis.
    * **Eliminación de Persistencia:** Quitar claves de registro, tareas programadas, servicios o cuentas de usuario creadas por el atacante.
    * **Parcheo de Vulnerabilidades:** Aplicar los parches de seguridad necesarios para cerrar la vulnerabilidad que fue explotada originalmente.
    * **Reconfiguración Segura:** Corregir configuraciones inseguras que permitieron el ataque (ej. permisos débiles, servicios innecesarios habilitados).
    * **Reinstalación / Reimagen del Sistema (`Reimaging`):** **A menudo la opción más segura y recomendada para sistemas comprometidos.** Consiste en formatear el disco y reinstalar el sistema operativo desde una imagen "dorada" (limpia y segura) conocida. Luego se restauran los datos desde backups limpios. Esto asegura la eliminación de cualquier rootkit o backdoor oculto.
    * **Reseteo de Credenciales:** Forzar el cambio de contraseñas para todas las cuentas potencialmente expuestas o comprometidas.
* **Validación:** Antes de pasar a la recuperación, es crucial validar que la erradicación ha sido exitosa (ej. re-escaneando el sistema, monitorizando actividad).

## ✅ Relación con el Análisis (Fase de Identificación)

La eficacia de la Contención y la Erradicación depende directamente de la calidad del análisis realizado en la fase de Identificación. Un buen análisis debe determinar:
* Qué sistemas están comprometidos.
* Qué cuentas están comprometidas.
* Qué malware/herramientas usó el atacante y dónde residen.
* Qué vulnerabilidades se explotaron (si aplica).
* Qué mecanismos de persistencia se establecieron.

Esta información es la que guía las acciones específicas a tomar durante la contención y erradicación.

## 🎯 Nota sobre BTL1

Recuerda que el examen BTL1 se centra principalmente en la **Identificación y Análisis**. Aunque debes entender conceptualmente qué son la Contención y la Erradicación, **no se espera que realices estas acciones** directamente en el entorno simulado del examen. Tu rol es proporcionar el análisis detallado que permitiría a un equipo de IR tomar estas decisiones en un escenario real.

---
*La Contención busca detener el daño inmediato, mientras que la Erradicación busca eliminar la amenaza de raíz para una recuperación segura y duradera.*
