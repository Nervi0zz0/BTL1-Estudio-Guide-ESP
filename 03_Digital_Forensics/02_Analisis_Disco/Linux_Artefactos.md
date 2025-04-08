# Artefactos Forenses Clave en Linux

Linux, debido a su naturaleza abierta y su estructura de archivos estándar, ofrece muchos lugares donde buscar evidencia digital. Conocer los directorios y archivos de log principales es esencial.

## 📜 Logs del Sistema (`/var/log`)

Este directorio es el repositorio central para la mayoría de los logs del sistema y las aplicaciones en casi todas las distribuciones de Linux.

* **Valor Forense:** Registro de eventos del sistema, autenticación, actividad de servicios (web, mail, etc.), errores del kernel.
* **Archivos Clave (Pueden variar ligeramente entre distribuciones):**
    * **`syslog` o `messages`:** Log general del sistema, eventos variados.
    * **`auth.log` (Debian/Ubuntu) o `secure` (CentOS/RHEL):** **¡Muy importante!** Registra intentos de login (exitosos y fallidos), uso de `sudo`, actividad de SSH, creación de usuarios/grupos.
    * **`kern.log`:** Mensajes del kernel (hardware, drivers).
    * **`dmesg`:** Mensajes del buffer del kernel, útil para eventos de arranque.
    * **`boot.log`:** Mensajes específicos del proceso de arranque.
    * **`faillog`:** Registra intentos fallidos de login (formato binario).
    * **`lastlog`:** Registra la última vez que cada usuario inició sesión (formato binario).
    * **Logs de Aplicaciones:** Subdirectorios como `/var/log/apache2/`, `/var/log/httpd/`, `/var/log/nginx/`, `/var/log/mysql/`, etc., contienen logs específicos de esos servicios.
    * **`wtmp`:** Registra los logins/logouts históricos de los usuarios (formato binario, leído por `last`).
    * **`btmp`:** Registra los intentos fallidos de login (formato binario, leído por `lastb`).
    * **`apt/` (Debian/Ubuntu) o `yum.log` (CentOS/RHEL):** Historial de instalación/actualización de paquetes.
* **Herramientas de Análisis:** `cat`, `less`, `more`, `tail`, `head`, `grep`, `awk`, `sed` para visualización y filtrado de texto. `journalctl` para sistemas que usan `systemd`. `last`, `lastb`, `who`, `utmpdump` para logs binarios de login.

## ⌨️ Historial de Comandos (`Bash History`)

Registra los comandos ejecutados por los usuarios en la terminal `bash` (la más común).

* **Valor Forense:** Permite reconstruir las acciones realizadas por un usuario (o atacante) en la línea de comandos. ¡Extremadamente valioso!
* **Ubicación Típica:** Archivo oculto `.bash_history` en el directorio home de cada usuario (`/home/<usuario>/.bash_history` o `/root/.bash_history`).
* **Consideraciones:**
    * El usuario puede borrar o manipular este archivo.
    * Por defecto, puede no incluir timestamps (se configura con `HISTTIMEFORMAT`).
    * Otras shells (zsh, fish) usan archivos diferentes (`.zsh_history`, `.local/share/fish/fish_history`).
* **Herramientas:** `cat`, `less`, `grep`, `strings` (si el archivo está corrupto).

## 👤 Cuentas de Usuario y Grupos

Información sobre quién puede acceder al sistema y con qué permisos.

* **Valor Forense:** Identificar usuarios legítimos, usuarios creados por atacantes, membresías a grupos privilegiados (ej. `sudo`, `wheel`, `adm`, `docker`).
* **Archivos Clave:**
    * `/etc/passwd`: Información básica de usuarios (nombre, UID, GID, home, shell). Legible por todos.
    * `/etc/shadow`: Contiene los hashes de las contraseñas y políticas de expiración. **Solo legible por root.**
    * `/etc/group`: Define los grupos y qué usuarios pertenecen a ellos.
    * `/etc/sudoers` o archivos en `/etc/sudoers.d/`: Define quién puede usar `sudo` y con qué privilegios.
* **Herramientas:** `cat`, `less`, `grep`, `getent passwd <usuario>`, `getent group <grupo>`, `id <usuario>`.

## 🌐 Información de Red

Configuración y estado de las interfaces y conexiones de red.

* **Valor Forense:** Entender la configuración de red del sistema, identificar conexiones activas (potencial C2), puertos en escucha.
* **Comandos/Ubicaciones Clave:**
    * **Configuración Interfaces:** Comando `ip addr` (o `ifconfig` legacy). Archivos de configuración estática varían (ej. `/etc/network/interfaces`, `/etc/sysconfig/network-scripts/`).
    * **Conexiones Activas:** Comando `ss -tulnp` (o `netstat -tulnp` legacy). Muestra TCP/UDP, escuchando/conectado, IPs/puertos, y el proceso asociado (con `-p`).
    * **Configuración DNS:** `/etc/resolv.conf`.
    * **Caché ARP:** `ip neigh` (o `arp -a` legacy).

## ⏰ Tareas Programadas (`Cron`)

Permite ejecutar comandos o scripts automáticamente en momentos definidos. Usado legítimamente, pero también como técnica de persistencia común para malware.

* **Valor Forense:** Identificar scripts o comandos sospechosos que se ejecutan periódicamente.
* **Ubicaciones:**
    * **System-wide:** `/etc/crontab`, archivos dentro de `/etc/cron.d/`, `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/`.
    * **User-specific:** Se gestionan con `crontab -e`. Los archivos suelen estar en `/var/spool/cron/crontabs/<usuario>` (requiere root para ver los de otros usuarios).
* **Herramientas:** `cat`, `ls` para ver los directorios/archivos. `crontab -l -u <usuario>` para listar las tareas de un usuario específico.

## ⚙️ Información de Procesos (Principalmente Live/Memoria)

Aunque se obtiene mejor de un sistema vivo o volcado de memoria, algunos rastros pueden quedar en logs.

* **Valor Forense:** Identificar procesos maliciosos en ejecución.
* **Comandos (Live):** `ps aux`, `ps -elf`, `top`, `htop`.
* **Sistema de Archivos `/proc`:** Virtual. Contiene un directorio por cada PID de proceso en ejecución (`/proc/<PID>/`) con información detallada (`cmdline` - comando, `environ` - variables de entorno, `maps` - memoria mapeada, `fd` - archivos abiertos).

## 👤 Directorios Home de Usuario (`/home/<usuario>` o `/root`)

Contienen los archivos personales del usuario y muchas configuraciones de aplicaciones.

* **Valor Forense:** Archivos descargados por el usuario (potencial malware), configuraciones de aplicaciones (navegadores, clientes de correo), claves SSH (`.ssh/`), scripts personales, historial de comandos.
* **Herramientas:** `ls -la`, `find`, `grep`.

## 🗑️ Archivos Temporales (`/tmp`, `/var/tmp`)

Directorios diseñados para almacenar archivos temporales. A menudo tienen permisos laxos y son usados por malware.

* **Valor Forense:** Malware descargado, scripts intermedios, archivos extraídos.
* **Herramientas:** `ls -la`, `find`.

---
