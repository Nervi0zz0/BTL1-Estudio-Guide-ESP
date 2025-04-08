# Splunk Cheatsheet para Análisis de Seguridad

**Splunk** es una potente plataforma utilizada ampliamente como SIEM y para análisis de logs en general. Permite buscar, analizar y visualizar grandes volúmenes de datos de máquina. Su lenguaje de búsqueda se llama **SPL (Search Processing Language)**.

## 💡 Conceptos Básicos de Búsqueda (SPL)

La base de Splunk es su barra de búsqueda, donde introduces consultas SPL. Elementos clave:

* **Time Range Picker:** **Fundamental.** Selecciona el rango de tiempo sobre el que quieres buscar (últimos 15 min, últimas 24h, fechas específicas...). ¡Asegúrate de que cubre el período de tu incidente!
* **Índice (`index`):** Los datos se organizan en índices. Debes especificar en cuál buscar.
    * `index="nombre_indice"` (Ej: `index="os"`, `index="security"`, `index="firewall"`)
    * `index=*` (Busca en todos los índices, menos eficiente).
* **Metadatos Comunes:** Campos que Splunk añade automáticamente o extrae al indexar. Útiles para filtrar rápido:
    * `sourcetype="tipo_fuente"` (Ej: `sourcetype="WinEventLog:Security"`, `sourcetype="linux_secure"`, `sourcetype="cisco:asa"`)
    * `source="ruta_o_nombre_archivo"` (Ej: `source="/var/log/auth.log"`)
    * `host="nombre_host"` (Ej: `host="webserver01"`)
* **Palabras Clave / Términos:** Busca eventos que contengan ciertas palabras. Usa comillas `"` para frases exactas.
    * `sshd`
    * `"failed login"`
    * `error OR warning` (Operadores booleanos: `AND`, `OR`, `NOT`)
* **Campos (`field=value`):** Filtra por valores específicos en campos extraídos por Splunk.
    * `src_ip="192.168.1.10"`
    * `user="admin"`
    * `status_code=404`
    * `EventCode=4625` (Se pueden usar comparadores: `>`, `<`, `>=`, `<=`, `!=`)
* **Pipe (`|`):** **Esencial.** Envía los resultados de una búsqueda inicial a comandos posteriores para procesarlos (filtrar, transformar, agregar, formatear). `comando1 | comando2 | comando3 ...`

##  कमांड SPL Comandos Esenciales (Cheatsheet)

Aquí tienes una tabla con comandos SPL comunes y útiles para análisis de seguridad, incluyendo los de tus notas:

| Comando SPL (Ejemplo)                                | Descripción                                                                                                                               |
| :--------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------- |
| `search <terminos>` (implícito)                    | Comando base. Busca eventos que coincidan. Ej: `index="security" host="dc01" EventCode=4625`                                                    |
| **Filtrado/Selección:** |                                                                                                                                           |
| `head [N]`                                           | Muestra los **primeros N** eventos (por defecto 10) según el orden actual (usualmente cronológico inverso). Ej: `... | head 20`               |
| `tail [N]`                                           | Muestra los **últimos N** eventos según el orden actual.                                                                                   |
| `where <condicion>`                                | Filtra resultados **después** de la búsqueda inicial, usando comparaciones (`>`, `<`, `!=`, etc.), funciones (`like()`, `cidrmatch()`). Ej: `... | where process_name like "%.exe"` |
| **Agrupación/Estadísticas:** |                                                                                                                                           |
| `stats <func(campo)> BY <campo_agrupar>`             | **Muy potente.** Realiza cálculos estadísticos. Funciones: `count`, `dc` (distinct_count), `avg`, `sum`, `min`, `max`, `values`, `list`. Agrupa con `BY`. |
|  *Ej: `... | stats count BY src_ip`* | *Cuenta eventos por IP origen.* |
|  *Ej: `... | stats dc(user) BY host`* | *Cuenta usuarios únicos por host.* |
|  *Ej: `... | stats values(action) AS Acciones BY user`*| *Lista acciones únicas por usuario (renombrando el campo a 'Acciones').* |
| `top <campo> [limit=N]`                             | Muestra los N valores más frecuentes de un campo, con su cuenta y porcentaje. Ej: `index=fw "action=blocked" | top src_ip limit=10`           |
| `rare <campo> [limit=N]`                            | Muestra los N valores menos frecuentes de un campo. Útil para encontrar anomalías. Ej: `... | rare user_agent limit=10`                    |
| **Formato/Visualización:** |                                                                                                                                           |
| `table <campo1> <campo2> ...`                         | Muestra los resultados en tabla, **solo** con los campos especificados. Ej: `... | table _time, user, src_ip, dest_ip, status`           |
| `fields [+]/- <campo1> ...`                         | **Añade (`+`) o quita (`-`)** campos de la vista de resultados sin ocultar los demás. Ej: `... | fields - _raw, host`                      |
| `rename <campo_orig> AS <campo_nuevo>`              | Renombra un campo para mayor claridad. Ej: `... | rename user AS Usuario`                                                                  |
| `sort [+]/- <campo>`                                | Ordena los resultados por un campo (`+` asc, `-` desc). Ej: `... | sort -count` (ordena por campo 'count' de mayor a menor)               |
| **Visualización Temporal:** |                                                                                                                                           |
| `timechart [span=<duracion>] <func> BY <campo>`       | Crea gráficos de series temporales. Similar a `stats` pero con el tiempo como eje X implícito. `span` define el intervalo (ej. `1h`, `5m`). |
|  *Ej: `... | timechart span=1h count BY EventCode`* | *Gráfico de eventos por hora, separado por código de evento.* |

**Ejemplos Combinados:**

* `index="main" src_ip="192.168.1.1"`: Busca eventos del índice `main` donde el campo `src_ip` sea `192.168.1.1`.
* `index="security" sourcetype="syslog" error`: Busca eventos `syslog` en índice `security` que contengan la palabra `error`.
* `index="security" | stats count BY sourcetype`: Cuenta cuántos eventos hay por cada tipo de fuente en el índice `security`.
* `index="security" | head 10`: Muestra los 10 eventos más recientes (o primeros según orden) del índice `security`.
* `index="security" | table _time, host, src_ip, dest_ip, _raw`: Muestra una tabla con esos campos específicos para eventos del índice `security`.
* `index="security" | timechart span=1h count BY sourcetype`: Crea un gráfico de eventos por hora, agrupados por tipo de fuente, para el índice `security`.
* `index="main" "failed login" | top src_ip`: Busca eventos con `"failed login"` en índice `main` y muestra las IPs origen (`src_ip`) que aparecen con más frecuencia.

## ⭐ Consejos Rápidos para Búsquedas

* **Filtra Temprano, Filtra Frecuentemente:** Usa `index`, `sourcetype`, `host` y términos clave al inicio para reducir el volumen de datos antes de usar comandos `|` costosos.
* **Itera:** Empieza con búsquedas amplias y añade filtros o comandos `|` progresivamente para refinar los resultados.
* **`_time`:** Usa el selector de rango de tiempo de la interfaz, es más eficiente que filtrar por `_time` en la búsqueda.
* **Modo `Fast` vs `Smart` vs `Verbose`:** Afecta a cómo Splunk procesa y muestra los datos. `Fast` es más rápido pero extrae menos campos; `Verbose` lo extrae todo. `Smart` (por defecto) intenta equilibrar.
* **¡Lee la Documentación!** Splunk SPL tiene muchísimos comandos y funciones: [Splunk Search Reference](https://docs.splunk.com/Documentation/Splunk/latest/SearchReference/WhatsInThisManual).

---
*Dominar SPL es una habilidad clave para cualquier analista que trabaje con Splunk. Empieza con estos comandos básicos y practica construyendo consultas para responder preguntas específicas.*
