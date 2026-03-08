# 🐧 Tutorial Completo de Linux

## Índice

1. [¿Qué es Linux?](#1-qué-es-linux)
2. [Instalación de Linux (Ubuntu)](#2-instalación-de-linux-ubuntu)
3. [La terminal: tu herramienta principal](#3-la-terminal-tu-herramienta-principal)
4. [Sistema de archivos de Linux](#4-sistema-de-archivos-de-linux)
5. [Comandos básicos de navegación](#5-comandos-básicos-de-navegación)
6. [Gestión de archivos y directorios](#6-gestión-de-archivos-y-directorios)
7. [Permisos y propietarios](#7-permisos-y-propietarios)
8. [Visualización y edición de texto](#8-visualización-y-edición-de-texto)
9. [Búsqueda de archivos y contenido](#9-búsqueda-de-archivos-y-contenido)
10. [Gestión de procesos](#10-gestión-de-procesos)
11. [Gestión de paquetes (apt)](#11-gestión-de-paquetes-apt)
12. [Redes y conectividad](#12-redes-y-conectividad)
13. [Usuarios y grupos](#13-usuarios-y-grupos)
14. [Variables de entorno y configuración del shell](#14-variables-de-entorno-y-configuración-del-shell)
15. [Redirecciones, tuberías y operadores](#15-redirecciones-tuberías-y-operadores)
16. [Scripting en Bash](#16-scripting-en-bash)
17. [Compresión y archivado](#17-compresión-y-archivado)
18. [SSH y administración remota](#18-ssh-y-administración-remota)
19. [Cron: tareas programadas](#19-cron-tareas-programadas)
20. [Trucos y atajos de productividad](#20-trucos-y-atajos-de-productividad)
21. [Proyecto práctico: Script de administración](#21-proyecto-práctico-script-de-administración)
22. [Referencia rápida de comandos](#22-referencia-rápida-de-comandos)

---

## 1. ¿Qué es Linux?

**Linux** es un sistema operativo de **código abierto** basado en Unix, creado por **Linus Torvalds** en 1991. A diferencia de Windows o macOS, Linux es libre, gratuito y puede ser modificado por cualquiera.

```
Árbol de sistemas operativos:
┌─────────────────────────────────────┐
│           Sistema Operativo         │
├──────────────┬──────────────────────┤
│   Windows    │    Unix/Linux        │
│   (cerrado)  ├──────────┬───────────┤
│              │  macOS   │   Linux   │
│              │ (cerrado)│  (abierto)│
└──────────────┴──────────┴───────────┘
```

### ¿Por qué aprender Linux?

| Razón | Detalle |
|-------|---------|
| 🌐 **Servidores** | El 96% de los servidores web usan Linux |
| ☁️ **Cloud** | AWS, Azure y Google Cloud son Linux |
| 🔒 **Seguridad** | Muy usado en ciberseguridad |
| 🤖 **DevOps** | Docker, Kubernetes corren sobre Linux |
| 💻 **Programación** | Entorno nativo para desarrollo |
| 🆓 **Gratis** | Sin licencias, sin costos |

### Distribuciones de Linux

Una **distribución (distro)** es Linux + software adicional empaquetado:

```
Distribuciones más populares:

🟠 Ubuntu      → Principiantes, escritorio y servidor
🔴 Fedora      → Tecnología reciente, desarrolladores
⚫ Debian      → Estabilidad máxima, servidores
🟢 Linux Mint  → Fácil para usuarios de Windows
🏴 Arch Linux  → Control total, usuarios avanzados
🔵 CentOS/RHEL → Empresas, servidores críticos
```

> 💡 **Recomendación para principiantes:** Empieza con **Ubuntu 22.04 LTS** o **Linux Mint**. Son las más amigables y tienen la mayor comunidad de soporte.

---

## 2. Instalación de Linux (Ubuntu)

### Opción 1: Máquina virtual (recomendada para comenzar)

Instala **VirtualBox** (gratis) y crea una VM:

```bash
# Lo que necesitas:
# - VirtualBox: https://www.virtualbox.org/
# - ISO de Ubuntu: https://ubuntu.com/download/desktop

# Configuración mínima para la VM:
# - RAM: 2 GB (recomendado: 4 GB)
# - Disco: 20 GB
# - CPU: 2 núcleos
```

### Opción 2: WSL2 en Windows (Windows 10/11)

```powershell
# En PowerShell como Administrador:
wsl --install

# Instalar una distro específica:
wsl --install -d Ubuntu-22.04

# Listar distros disponibles:
wsl --list --online
```

### Opción 3: Instalación nativa (dual boot)

```
Pasos:
1. Descarga la ISO de Ubuntu desde ubuntu.com
2. Crea un USB booteable con Rufus (Windows) o balenaEtcher
3. Reinicia el PC y arranca desde el USB
4. Sigue el asistente de instalación
5. Elige "Instalar junto a Windows" para dual boot
```

### Verificar la instalación

```bash
# Ver versión del sistema operativo
cat /etc/os-release

# Ver versión del kernel de Linux
uname -r

# Ver arquitectura del sistema
uname -m

# Ver información del hardware
lscpu
```

---

## 3. La terminal: tu herramienta principal

La **terminal** (también llamada consola o shell) es la interfaz de texto donde escribes comandos. Es el corazón de Linux.

```
┌─────────────────────────────────────────┐
│  usuario@hostname:~$  █                 │
│                                         │
│  ↑           ↑       ↑                  │
│  nombre    máquina  directorio          │
│  usuario           actual               │
└─────────────────────────────────────────┘
```

### Abrir la terminal

- **Ubuntu:** `Ctrl + Alt + T`
- **Desde el menú:** Busca "Terminal" o "Konsole"
- **Clic derecho en escritorio:** "Abrir terminal aquí"

### Anatomía de un comando

```bash
comando  [opciones]  [argumentos]
   ↑          ↑           ↑
¿qué hacer? ¿cómo?   ¿sobre qué?

# Ejemplos:
ls          # solo comando
ls -l       # comando + opción
ls -l /home # comando + opción + argumento
ls -la /home /tmp  # múltiples argumentos
```

### Tipos de shell

```bash
# Ver qué shell estás usando
echo $SHELL
# /bin/bash  ← el más común

# Otros shells populares:
# bash  → Bourne Again Shell (estándar)
# zsh   → Z Shell (más features, usado con Oh My Zsh)
# sh    → Shell POSIX básico
# fish  → Friendly Interactive Shell
```

### Atajos esenciales de la terminal

| Atajo | Función |
|-------|---------|
| `Tab` | Autocompletar comandos y rutas |
| `Tab Tab` | Mostrar todas las opciones |
| `↑` / `↓` | Navegar por el historial |
| `Ctrl + C` | Cancelar comando en ejecución |
| `Ctrl + Z` | Suspender proceso |
| `Ctrl + D` | Cerrar terminal / fin de entrada |
| `Ctrl + L` | Limpiar pantalla (igual que `clear`) |
| `Ctrl + A` | Ir al inicio de la línea |
| `Ctrl + E` | Ir al final de la línea |
| `Ctrl + R` | Buscar en el historial |

---

## 4. Sistema de archivos de Linux

En Linux **todo es un archivo**: documentos, carpetas, dispositivos, procesos. El sistema de archivos tiene una estructura en árbol que empieza en `/` (raíz).

```
/  (raíz - root)
├── bin/      → Comandos esenciales (ls, cp, mv...)
├── boot/     → Archivos de arranque del sistema
├── dev/      → Dispositivos (discos, USB, teclado...)
├── etc/      → Archivos de configuración del sistema
├── home/     → Directorios personales de usuarios
│   ├── alice/
│   └── bob/
├── lib/      → Librerías del sistema
├── media/    → Puntos de montaje (USB, CD-ROM...)
├── mnt/      → Montajes temporales
├── opt/      → Software adicional/opcional
├── proc/     → Información de procesos (virtual)
├── root/     → Directorio home del usuario root
├── sbin/     → Comandos de administración del sistema
├── srv/      → Datos de servicios (web, ftp...)
├── sys/      → Información del kernel (virtual)
├── tmp/      → Archivos temporales (se borran al reiniciar)
├── usr/      → Programas y datos de usuarios
│   ├── bin/  → La mayoría de los programas
│   ├── lib/  → Librerías
│   └── local/→ Software instalado localmente
└── var/      → Datos variables (logs, bases de datos...)
    ├── log/
    └── www/
```

### Rutas absolutas vs relativas

```bash
# Ruta ABSOLUTA: siempre empieza desde /
/home/alice/documentos/informe.txt

# Ruta RELATIVA: depende de dónde estás ahora
documentos/informe.txt    # si estás en /home/alice
../alice/documentos/      # subir un nivel con ..

# Atajos especiales:
~          # tu directorio home (/home/tuusuario)
.          # directorio actual
..         # directorio padre (un nivel arriba)
-          # directorio anterior

# Ejemplos:
cd ~       # ir al home
cd ..      # subir un nivel
cd -       # volver al directorio anterior
```

---

## 5. Comandos básicos de navegación

### `pwd` - ¿Dónde estoy?

```bash
pwd
# /home/alice
```

### `ls` - Listar archivos

```bash
ls                    # listar directorio actual
ls /etc               # listar directorio específico
ls -l                 # formato detallado (long)
ls -a                 # mostrar archivos ocultos (empiezan con .)
ls -la                # detallado + ocultos
ls -lh                # tamaños legibles (KB, MB, GB)
ls -lt                # ordenar por fecha de modificación
ls -lS                # ordenar por tamaño
ls -R                 # listar recursivamente (subcarpetas)
ls --color=auto       # colorear la salida
```

```
Salida de ls -l:
-rw-r--r-- 1 alice users  4096 Mar 15 10:30 archivo.txt
↑           ↑  ↑     ↑     ↑      ↑           ↑
tipo+permisos nº  dueño grupo tamaño  fecha      nombre
```

### `cd` - Cambiar directorio

```bash
cd /var/log           # ir a una ruta absoluta
cd Documentos         # ir a una ruta relativa
cd ~                  # ir al home
cd ..                 # subir un nivel
cd ../..              # subir dos niveles
cd -                  # volver al directorio anterior
```

### `tree` - Ver árbol de directorios

```bash
# Instalar si no está disponible:
sudo apt install tree

tree                  # árbol del directorio actual
tree /etc             # árbol de /etc
tree -L 2             # solo 2 niveles de profundidad
tree -a               # incluir archivos ocultos
tree -d               # solo directorios
```

---

## 6. Gestión de archivos y directorios

### Crear archivos y directorios

```bash
# Crear directorio
mkdir proyectos
mkdir -p proyectos/web/css    # crear subdirectorios en cadena (-p)
mkdir dir1 dir2 dir3          # crear múltiples directorios

# Crear archivo vacío
touch archivo.txt
touch archivo1.txt archivo2.txt   # varios archivos a la vez

# Crear archivo con contenido
echo "Hola mundo" > saludo.txt         # crear/sobrescribir
echo "Segunda línea" >> saludo.txt     # añadir al final
cat > notas.txt << EOF                 # entrada multilínea
Línea 1
Línea 2
EOF
```

### Copiar archivos

```bash
cp origen.txt destino.txt         # copiar archivo
cp origen.txt /ruta/destino/      # copiar a otro directorio
cp -r carpeta1/ carpeta2/         # copiar directorio (-r recursivo)
cp -i origen.txt destino.txt      # preguntar antes de sobrescribir
cp -v origen.txt destino.txt      # mostrar lo que hace (-v verbose)
cp -u origen.txt destino.txt      # solo copiar si es más nuevo
```

### Mover y renombrar

```bash
mv archivo.txt nuevo_nombre.txt   # renombrar
mv archivo.txt /ruta/destino/     # mover a otro directorio
mv carpeta1/ /ruta/               # mover directorio
mv -i *.txt /backup/              # mover con confirmación
mv -v archivo.txt /tmp/           # modo verbose
```

### Eliminar archivos

```bash
rm archivo.txt                    # eliminar archivo
rm -i archivo.txt                 # pedir confirmación
rm -f archivo.txt                 # forzar sin confirmación
rm *.log                          # eliminar por patrón
rm -r carpeta/                    # eliminar directorio y contenido
rm -rf carpeta/                   # forzar eliminación recursiva ⚠️

# ⚠️ CUIDADO: rm -rf no tiene papelera de reciclaje
# Nunca ejecutes: rm -rf /  o  rm -rf /*
```

### Enlace simbólico (acceso directo)

```bash
ln -s /ruta/original /ruta/enlace   # crear enlace simbólico
ln -s ~/proyectos ~/Desktop/proyectos  # acceso directo en escritorio
ls -la | grep ^l                      # ver enlaces simbólicos
```

### Información de archivos

```bash
file documento.pdf              # tipo de archivo
stat archivo.txt                # información detallada
du -sh carpeta/                 # tamaño de carpeta
du -sh *                        # tamaño de cada elemento
df -h                           # espacio en disco
wc -l archivo.txt               # contar líneas
wc -w archivo.txt               # contar palabras
wc -c archivo.txt               # contar bytes
```

---

## 7. Permisos y propietarios

Los permisos en Linux son uno de los conceptos más importantes para la seguridad del sistema.

### Entender los permisos

```
-rwxr-xr--  1  alice  developers  4096  Mar 15  script.sh
↑↑↑↑↑↑↑↑↑↑  ↑    ↑        ↑
│└┤└┤└┤└┤   │   dueño    grupo
│ │  │  └─ Otros (other):  r-- = solo lectura
│ │  └──── Grupo (group):  r-x = lectura y ejecución
│ └─────── Dueño (user):   rwx = todos los permisos
└───────── Tipo: - archivo, d directorio, l enlace

Permisos:
r = read    (lectura)   = 4
w = write   (escritura) = 2
x = execute (ejecución) = 1
- = sin permiso         = 0
```

### Tabla de permisos en octal

```
Número  Letras  Significado
  0      ---    Sin permisos
  1      --x    Solo ejecución
  2      -w-    Solo escritura
  3      -wx    Escritura y ejecución
  4      r--    Solo lectura
  5      r-x    Lectura y ejecución
  6      rw-    Lectura y escritura
  7      rwx    Todos los permisos
```

### `chmod` - Cambiar permisos

```bash
# Usando notación octal:
chmod 755 script.sh    # rwxr-xr-x (típico para scripts)
chmod 644 archivo.txt  # rw-r--r-- (típico para archivos)
chmod 600 privado.txt  # rw------- (solo el dueño puede leer/escribir)
chmod 777 publico.sh   # rwxrwxrwx (⚠️ inseguro, evitar; usa 755 para scripts o 644 para archivos de datos)

# Usando notación simbólica:
chmod +x script.sh     # añadir ejecución a todos
chmod -x script.sh     # quitar ejecución a todos
chmod u+x script.sh    # añadir ejecución solo al dueño (u=user)
chmod g-w archivo.txt  # quitar escritura al grupo (g=group)
chmod o+r archivo.txt  # añadir lectura a otros (o=other)
chmod a+x script.sh    # añadir ejecución a todos (a=all)

# Recursivo:
chmod -R 755 carpeta/  # aplicar a toda la carpeta
```

### `chown` - Cambiar propietario

```bash
chown alice archivo.txt          # cambiar dueño
chown alice:developers archivo.txt  # cambiar dueño y grupo
chown :developers archivo.txt    # cambiar solo el grupo
chown -R alice:alice carpeta/    # recursivo
sudo chown root:root /etc/hosts  # necesita sudo para archivos del sistema
```

### Casos prácticos de permisos

```bash
# Script ejecutable por el dueño, legible por todos:
chmod 755 mi-script.sh

# Archivo de configuración privado:
chmod 600 ~/.ssh/config

# Clave SSH privada (obligatorio este permiso):
chmod 600 ~/.ssh/id_rsa

# Directorio web compartido:
chmod 755 /var/www/html/

# Ver permisos de forma visual:
ls -la archivo.txt
```

---

## 8. Visualización y edición de texto

### Ver contenido de archivos

```bash
# cat - mostrar todo el archivo
cat archivo.txt
cat -n archivo.txt    # con números de línea
cat archivo1.txt archivo2.txt  # concatenar dos archivos

# less - ver archivo página a página (mejor para archivos grandes)
less /var/log/syslog
# Dentro de less:
# j/k o ↑/↓ → moverse línea a línea
# Espacio   → página siguiente
# b         → página anterior
# /texto    → buscar texto
# n         → siguiente resultado de búsqueda
# q         → salir

# head - ver las primeras líneas
head archivo.txt          # primeras 10 líneas (por defecto)
head -n 5 archivo.txt     # primeras 5 líneas
head -n 20 /var/log/syslog

# tail - ver las últimas líneas
tail archivo.txt          # últimas 10 líneas
tail -n 5 archivo.txt     # últimas 5 líneas
tail -f /var/log/syslog   # seguir el archivo en tiempo real (muy útil para logs)

# more - paginador básico
more archivo_grande.txt
```

### `grep` - Buscar texto en archivos

```bash
grep "error" archivo.log          # buscar "error" en archivo
grep -i "error" archivo.log       # ignorar mayúsculas/minúsculas
grep -n "error" archivo.log       # mostrar número de línea
grep -r "TODO" ~/proyectos/       # buscar recursivamente en directorio
grep -v "info" archivo.log        # mostrar líneas que NO contienen el patrón
grep -c "error" archivo.log       # contar coincidencias
grep -l "config" /etc/*.conf      # solo mostrar nombres de archivos
grep -A 2 "error" archivo.log     # 2 líneas después del match
grep -B 2 "error" archivo.log     # 2 líneas antes del match
grep -E "error|warning" archivo.log  # expresiones regulares (regex)
grep "^Error" archivo.log         # líneas que empiezan con "Error"
grep "\.txt$" lista.txt           # líneas que terminan con ".txt"

# Ejemplos útiles:
grep "FAILED" /var/log/auth.log   # intentos de login fallidos
grep -r "password" /etc/          # buscar contraseñas en config (⚠️ solo en auditorías autorizadas; tratar la salida con cuidado)
ps aux | grep nginx               # buscar proceso nginx
```

### Editores de texto en terminal

#### nano (el más fácil)

```bash
nano archivo.txt          # abrir/crear archivo

# Atajos dentro de nano:
# Ctrl+O   → guardar (Write Out)
# Ctrl+X   → salir
# Ctrl+W   → buscar
# Ctrl+K   → cortar línea
# Ctrl+U   → pegar
# Ctrl+G   → ayuda
```

#### vim (el más potente)

```bash
vim archivo.txt           # abrir archivo

# vim tiene dos modos principales:
# NORMAL (modo por defecto) → para navegar y comandos
# INSERT                   → para escribir texto

# Para escribir: presiona 'i' (INSERT mode)
# Para salir de INSERT mode: presiona Escape

# Comandos en modo NORMAL:
# :w       → guardar
# :q       → salir
# :wq      → guardar y salir
# :q!      → salir sin guardar
# dd       → eliminar línea
# yy       → copiar línea
# p        → pegar
# /texto   → buscar
# u        → deshacer
# Ctrl+R   → rehacer
# gg       → ir al inicio del archivo
# G        → ir al final del archivo
```

> 💡 **Tip:** Si abres vim por accidente y no sabes cómo salir, presiona `Esc` varias veces y luego escribe `:q!` y Enter.

### `sed` - Editor de flujo

```bash
# Reemplazar texto:
sed 's/viejo/nuevo/' archivo.txt        # reemplazar primera ocurrencia por línea
sed 's/viejo/nuevo/g' archivo.txt       # reemplazar todas las ocurrencias
sed -i 's/viejo/nuevo/g' archivo.txt    # modificar el archivo directamente
sed -i.bak 's/viejo/nuevo/g' arch.txt   # con backup

# Eliminar líneas:
sed '3d' archivo.txt                    # eliminar línea 3
sed '/patrón/d' archivo.txt             # eliminar líneas con patrón
sed '1,5d' archivo.txt                  # eliminar líneas 1 a 5

# Mostrar líneas específicas:
sed -n '5,10p' archivo.txt              # mostrar líneas 5 a 10
```

---

## 9. Búsqueda de archivos y contenido

### `find` - Buscar archivos

```bash
# Buscar por nombre:
find /home -name "*.txt"            # todos los .txt en /home
find . -name "config.json"          # buscar en directorio actual
find / -name "passwd" 2>/dev/null   # buscar en todo el sistema

# Buscar por tipo:
find /tmp -type f                   # solo archivos
find /home -type d                  # solo directorios
find /etc -type l                   # solo enlaces simbólicos

# Buscar por tamaño:
find / -size +100M                  # archivos mayores de 100 MB
find / -size -1k                    # archivos menores de 1 KB
find /home -size +10M -size -100M   # entre 10 y 100 MB

# Buscar por fecha:
find /home -mtime -7                # modificados en los últimos 7 días
find /tmp -atime +30                # accedidos hace más de 30 días
find / -newer archivo.txt           # más nuevos que archivo.txt

# Buscar por permisos:
find / -perm 777                    # permisos exactos 777
find / -perm /u+s                   # archivos con setuid bit

# Ejecutar comandos sobre los resultados:
find /tmp -name "*.log" -delete             # eliminar archivos encontrados
find /home -name "*.jpg" -exec ls -lh {} \; # ejecutar comando por cada uno
find . -name "*.py" -exec chmod +x {} +     # hacer ejecutables todos los .py
```

### `locate` - Búsqueda rápida en base de datos

```bash
sudo apt install mlocate    # instalar si no está disponible
sudo updatedb               # actualizar la base de datos

locate archivo.txt          # búsqueda rápida
locate -i imagen.png        # ignorar mayúsculas
locate "*.conf" | head -20  # los primeros 20 .conf del sistema
```

### `which` y `whereis`

```bash
which python3               # ruta del ejecutable
which ls                    # /usr/bin/ls
whereis nginx               # binario, fuentes y páginas del manual
type ls                     # tipo de comando (alias, función, binario)
```

---

## 10. Gestión de procesos

Un **proceso** es un programa en ejecución. Linux permite gestionar todos los procesos del sistema.

### Ver procesos

```bash
# ps - foto instantánea de procesos
ps                          # procesos de tu sesión actual
ps aux                      # TODOS los procesos del sistema
ps aux | grep nginx         # buscar proceso específico
ps -ef                      # formato completo

# top - monitor en tiempo real (interactivo)
top
# Dentro de top:
# q     → salir
# k     → matar proceso (pide PID)
# r     → cambiar prioridad (renice)
# M     → ordenar por memoria
# P     → ordenar por CPU
# h     → ayuda

# htop - versión mejorada de top (más visual)
sudo apt install htop
htop
# Dentro de htop:
# F2    → configuración
# F3    → buscar
# F5    → árbol de procesos
# F9    → matar proceso
# F10   → salir
```

```
Salida de ps aux:
USER    PID  %CPU %MEM    VSZ   RSS STAT START   TIME COMMAND
alice   1234  0.0  0.1  12345  4567 S    10:00   0:00 bash
alice   5678  2.5  1.2  89012  4567 R    10:05   0:30 python3 script.py
  ↑      ↑    ↑    ↑                ↑
usuario  ID   CPU  RAM             estado
         del       usada           (R=corriendo, S=durmiendo, Z=zombie)
         proceso
```

### Controlar procesos

```bash
# Ejecutar en segundo plano:
comando &                   # ejecutar en background
sleep 100 &                 # ejemplo
jobs                        # ver procesos en background
fg                          # traer al frente (foreground)
fg %2                       # traer el trabajo número 2 al frente
bg                          # reanudar en background

# Señales para procesos:
kill PID                    # señal TERM (terminación suave)
kill -9 PID                 # señal KILL (fuerza bruta, irrecuperable)
kill -15 PID                # señal TERM (igual que kill)
kill -HUP PID               # señal HUP (recargar configuración)
killall nginx               # matar por nombre
pkill -f "python script"    # matar por patrón de comando

# Prioridad de procesos (nice):
nice -n 10 ./proceso        # iniciar con baja prioridad (10)
nice -n -5 ./proceso        # alta prioridad (negativo = más prioritario)
renice 5 -p PID             # cambiar prioridad de proceso en ejecución

# Ejemplo: compilar en background con baja prioridad
nice -n 19 make -j4 &
```

### `nohup` - Procesos persistentes

```bash
# nohup: el proceso continúa aunque cierres la terminal
nohup python3 servidor.py &
nohup ./script.sh > salida.log 2>&1 &

# Ver el output:
tail -f nohup.out
```

---

## 11. Gestión de paquetes (apt)

**APT** (Advanced Package Tool) es el gestor de paquetes de Debian/Ubuntu. Permite instalar, actualizar y eliminar software fácilmente.

### Comandos esenciales de apt

```bash
# Actualizar la lista de paquetes disponibles:
sudo apt update

# Actualizar todos los paquetes instalados:
sudo apt upgrade

# Actualizar el sistema completo (incluyendo cambios de dependencias):
sudo apt full-upgrade

# Instalar un paquete:
sudo apt install nombre-paquete
sudo apt install curl wget git vim htop

# Instalar múltiples paquetes:
sudo apt install python3 python3-pip nodejs npm

# Eliminar un paquete:
sudo apt remove nombre-paquete         # elimina el paquete (guarda config)
sudo apt purge nombre-paquete          # elimina paquete y configuración
sudo apt autoremove                    # eliminar dependencias huérfanas

# Buscar paquetes:
apt search "editor de texto"
apt search nginx
apt show nginx                         # información detallada del paquete

# Ver paquetes instalados:
apt list --installed
dpkg -l | grep python                  # filtrar los instalados

# Limpiar caché:
sudo apt clean                         # limpiar paquetes descargados
sudo apt autoclean                     # limpiar solo los obsoletos
```

### Flujo de trabajo típico

```bash
# 1. Siempre actualizar antes de instalar:
sudo apt update && sudo apt upgrade -y

# 2. Instalar lo que necesitas:
sudo apt install htop curl wget tree unzip

# 3. Limpiar después:
sudo apt autoremove && sudo apt clean
```

### Gestor snap (paquetes universales)

```bash
# snap es otro gestor de paquetes en Ubuntu:
snap install code          # instalar VS Code
snap install spotify
snap list                  # ver snaps instalados
snap refresh               # actualizar todos los snaps
snap remove code           # desinstalar
```

---

## 12. Redes y conectividad

### Información de red

```bash
# Ver interfaces de red y direcciones IP:
ip addr show               # forma moderna (recomendada)
ip addr show eth0          # solo interfaz eth0
ifconfig                   # forma antigua (puede no estar instalada)

# Ver tabla de enrutamiento:
ip route show
route -n                   # forma antigua

# Ver conexiones activas:
ss -tuln                   # puertos en escucha (TCP/UDP)
ss -tulnp                  # con el proceso que los usa (requiere sudo)
netstat -tuln              # alternativa (puede no estar instalada)

# Información del nombre de host:
hostname                   # nombre del equipo
hostname -I                # dirección IP local
```

### Conectividad y diagnóstico

```bash
# ping - comprobar conectividad
ping google.com            # ping continuo
ping -c 4 google.com       # solo 4 pings
ping -i 2 8.8.8.8          # ping cada 2 segundos

# traceroute - trazar ruta de paquetes
traceroute google.com
mtr google.com             # combinación de ping + traceroute (más visual)

# DNS
nslookup google.com        # consulta DNS
dig google.com             # consulta DNS detallada
dig google.com MX          # registros de correo
host google.com            # consulta simple

# curl - hacer peticiones HTTP
curl https://api.github.com            # GET básico
curl -I https://google.com             # solo cabeceras HTTP
curl -o archivo.zip https://url.com    # descargar archivo
curl -X POST -d "datos" https://api.com  # petición POST
curl -H "Authorization: Bearer token" https://api.com

# wget - descargar archivos
wget https://url.com/archivo.zip       # descargar
wget -O nuevo-nombre.zip https://url.com  # con nombre personalizado
wget -c https://url.com/grande.iso     # continuar descarga interrumpida
wget --limit-rate=1m https://url.com   # limitar velocidad de descarga
```

### Configuración del firewall (UFW)

```bash
# UFW (Uncomplicated Firewall) - fácil de usar en Ubuntu:
sudo ufw status                    # ver estado del firewall
sudo ufw enable                    # activar firewall
sudo ufw disable                   # desactivar

# Reglas:
sudo ufw allow 22                  # permitir SSH
sudo ufw allow 80                  # permitir HTTP
sudo ufw allow 443                 # permitir HTTPS
sudo ufw allow 8080/tcp            # puerto específico
sudo ufw deny 23                   # bloquear Telnet
sudo ufw delete allow 8080         # eliminar regla

# Reglas por servicio:
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
```

---

## 13. Usuarios y grupos

Linux es un sistema **multiusuario**. Cada usuario tiene su propio espacio y permisos.

### Gestión de usuarios

```bash
# Ver usuario actual:
whoami                      # nombre del usuario actual
id                          # UID, GID y grupos

# Ver todos los usuarios:
cat /etc/passwd             # lista de usuarios del sistema
getent passwd               # forma más limpia
cut -d: -f1 /etc/passwd     # solo los nombres

# Crear usuario:
sudo adduser alice          # interactivo, crea home y todo
sudo useradd -m alice       # básico con home directory
sudo useradd -m -s /bin/bash -G sudo alice  # con shell y grupo sudo

# Cambiar contraseña:
passwd                      # cambiar tu propia contraseña
sudo passwd alice           # cambiar contraseña de otro usuario

# Modificar usuario:
sudo usermod -aG sudo alice         # añadir a grupo sudo
sudo usermod -aG docker alice       # añadir al grupo docker
sudo usermod -s /bin/zsh alice      # cambiar shell
sudo usermod -l nuevo_nombre alice  # cambiar nombre de usuario

# Eliminar usuario:
sudo userdel alice                  # eliminar usuario (conserva home)
sudo userdel -r alice               # eliminar usuario y su home

# Cambiar de usuario:
su alice                    # cambiar a usuario alice
su -                        # cambiar a root
sudo su                     # cambiar a root (con permisos de sudo)
exit                        # volver al usuario anterior
```

### Gestión de grupos

```bash
# Ver grupos del usuario actual:
groups
id

# Ver todos los grupos:
cat /etc/group
getent group

# Crear grupo:
sudo groupadd desarrolladores

# Añadir usuario a grupo:
sudo usermod -aG desarrolladores alice
sudo gpasswd -a alice desarrolladores

# Eliminar usuario de grupo:
sudo gpasswd -d alice desarrolladores

# Eliminar grupo:
sudo groupdel desarrolladores
```

### sudo - Ejecutar como administrador

```bash
# sudo permite a usuarios ejecutar comandos como root:
sudo apt update             # ejecutar con privilegios de root
sudo -i                     # abrir shell de root
sudo -l                     # ver qué puede hacer con sudo

# El archivo de configuración de sudo:
sudo visudo                 # editar /etc/sudoers de forma segura
# Línea para dar todos los permisos a alice:
# alice ALL=(ALL:ALL) ALL
```

---

## 14. Variables de entorno y configuración del shell

### Variables de entorno

```bash
# Ver todas las variables de entorno:
env
printenv

# Ver una variable específica:
echo $HOME          # /home/alice
echo $USER          # alice
echo $PATH          # rutas donde se buscan ejecutables
echo $SHELL         # /bin/bash
echo $PWD           # directorio actual
echo $LANG          # idioma del sistema

# Variables más importantes:
# PATH    → directorios donde se buscan los comandos
# HOME    → directorio home del usuario
# USER    → nombre del usuario
# SHELL   → shell actual
# TERM    → tipo de terminal
# EDITOR  → editor de texto por defecto
# LANG    → configuración de idioma

# Crear/modificar variable en sesión actual:
export MI_VARIABLE="valor"
export PATH="$PATH:/nuevo/directorio"   # añadir al PATH

# La variable desaparece al cerrar la terminal
# Para hacerla permanente, añádela a ~/.bashrc
```

### Configuración permanente del shell

```bash
# Los archivos de configuración de bash:
~/.bashrc          # ejecutado en cada terminal interactiva (no login)
~/.bash_profile    # ejecutado al hacer login
~/.profile         # ejecutado al hacer login (alternativa)
~/.bash_aliases    # para tus alias (si lo incluyes en .bashrc)

# Editar la configuración:
nano ~/.bashrc

# Añadir al final de ~/.bashrc:
export EDITOR=vim
export PATH="$PATH:$HOME/.local/bin"
alias ll='ls -la'
alias ..='cd ..'
alias grep='grep --color=auto'

# Aplicar los cambios sin reiniciar terminal:
source ~/.bashrc
# o
. ~/.bashrc
```

### Alias - atajos de comandos

```bash
# Crear alias temporales:
alias ll='ls -la'
alias c='clear'
alias update='sudo apt update && sudo apt upgrade -y'

# Ver todos los alias:
alias

# Eliminar alias:
unalias ll

# Alias permanentes (añadir a ~/.bashrc):
alias ..='cd ..'
alias ...='cd ../..'
alias la='ls -la'
alias df='df -h'
alias du='du -sh'
alias ports='ss -tuln'
alias myip='curl ifconfig.me'
alias reload='source ~/.bashrc'
```

---

## 15. Redirecciones, tuberías y operadores

Estas son algunas de las características más poderosas de Linux.

### Redirecciones

```bash
# Redirección de salida:
ls > lista.txt          # guardar salida en archivo (sobrescribir)
ls >> lista.txt         # añadir al final del archivo
ls 2> errores.txt       # redirigir solo los errores (stderr)
ls > salida.txt 2>&1    # redirigir tanto stdout como stderr
ls > /dev/null 2>&1     # descartar toda la salida
ls 2>/dev/null          # descartar solo los errores

# Redirección de entrada:
sort < lista.txt        # leer entrada desde archivo
mysql -u root < backup.sql  # ejecutar script SQL

# Here document (heredoc):
cat << EOF > archivo.txt
Línea 1
Línea 2
Línea 3
EOF
```

```
stdin  (0): entrada estándar  → teclado por defecto
stdout (1): salida estándar   → pantalla por defecto
stderr (2): salida de errores → pantalla por defecto

Redirigir stdout:  comando > archivo
Redirigir stderr:  comando 2> archivo
Redirigir ambos:   comando > archivo 2>&1
/dev/null:         "agujero negro", descarta todo
```

### Tuberías (pipes) `|`

```bash
# El pipe `|` conecta la salida de un comando con la entrada del siguiente

# Ejemplos básicos:
ls -la | less              # paginar resultado de ls
ps aux | grep nginx        # filtrar procesos
cat /etc/passwd | grep alice  # buscar en archivo
df -h | grep /dev/sda1     # filtrar espacio en disco

# Cadenas de pipes:
cat access.log | grep "404" | cut -d' ' -f1 | sort | uniq -c | sort -rn
#     leer       filtrar      extraer col     ordenar contar únicos  más repetidos
# → Estadísticas de IPs con errores 404

# Pipes útiles:
history | grep apt         # buscar comandos anteriores con apt
du -sh * | sort -h         # ordenar directorios por tamaño
cat archivo.txt | wc -l    # contar líneas
ls | wc -l                 # contar archivos en directorio
```

### Operadores de control

```bash
# ; → ejecutar secuencialmente (sin importar si hay errores)
comando1 ; comando2 ; comando3

# && → ejecutar el siguiente SOLO si el anterior tuvo éxito (exit 0)
sudo apt update && sudo apt upgrade   # si update falla, no ejecuta upgrade
mkdir nuevo_dir && cd nuevo_dir       # solo entra si se creó el directorio

# || → ejecutar el siguiente SOLO si el anterior FALLÓ
ping -c 1 google.com || echo "Sin conexión a internet"
ls archivo.txt || echo "El archivo no existe"

# & → ejecutar en background
sleep 60 &
./script_largo.sh &

# Combinaciones útiles:
cd /ruta && mkdir -p subdir && cd subdir    # navegar y crear
comando_que_puede_fallar || exit 1          # salir si falla
```

### Sustitución de comandos

```bash
# Usar la salida de un comando dentro de otro:
echo "Hoy es $(date)"
echo "Hay $(ls | wc -l) archivos aquí"
echo "Usuario: $(whoami)"
mkdir "backup-$(date +%Y%m%d)"   # crear carpeta con fecha actual

# Forma antigua (con backticks):
echo "Hoy es `date`"
```

---

## 16. Scripting en Bash

Un **script de Bash** es un archivo de texto con una serie de comandos que se ejecutan secuencialmente.

### Tu primer script

```bash
#!/bin/bash
# Mi primer script
# Autor: Alice
# Fecha: 2024

echo "¡Hola, mundo!"
echo "El usuario actual es: $(whoami)"
echo "El directorio actual es: $(pwd)"
echo "La fecha y hora es: $(date)"
```

```bash
# Guardar como: hola.sh
# Hacer ejecutable:
chmod +x hola.sh

# Ejecutar:
./hola.sh
# o
bash hola.sh
```

### Variables

```bash
#!/bin/bash

# Declarar variables (sin espacios alrededor del =)
nombre="Alice"
edad=25
pi=3.14159

# Usar variables:
echo "Hola, $nombre"
echo "Tienes $edad años"

# Variables de entorno:
echo "Tu home es: $HOME"
echo "Tu usuario es: $USER"

# Variables especiales:
echo "Nombre del script: $0"
echo "Primer argumento: $1"
echo "Segundo argumento: $2"
echo "Todos los argumentos: $@"
echo "Número de argumentos: $#"
echo "PID del script: $$"
echo "Código de salida del último comando: $?"
```

### Entrada del usuario

```bash
#!/bin/bash

echo "¿Cuál es tu nombre?"
read nombre
echo "¡Hola, $nombre!"

# Con prompt en la misma línea:
read -p "¿Cuántos años tienes? " edad
echo "Tienes $edad años"

# Sin mostrar lo que se escribe (para contraseñas):
read -sp "Introduce la contraseña: " password
echo ""
echo "Contraseña guardada"
```

### Condicionales (if/else)

```bash
#!/bin/bash

edad=18

# if básico:
if [ $edad -ge 18 ]; then
    echo "Eres mayor de edad"
fi

# if/else:
if [ $edad -ge 18 ]; then
    echo "Eres mayor de edad"
else
    echo "Eres menor de edad"
fi

# if/elif/else:
if [ $edad -lt 13 ]; then
    echo "Eres un niño"
elif [ $edad -lt 18 ]; then
    echo "Eres un adolescente"
elif [ $edad -lt 65 ]; then
    echo "Eres adulto"
else
    echo "Eres mayor"
fi
```

```bash
# Operadores de comparación:
# Numéricos:     -eq (=), -ne (≠), -lt (<), -gt (>), -le (≤), -ge (≥)
# Strings:       = (igual), != (distinto), -z (vacío), -n (no vacío)
# Archivos:      -f (es archivo), -d (es directorio), -e (existe), -r (readable), -x (ejecutable)

# Ejemplos:
if [ "$nombre" = "Alice" ]; then echo "Hola Alice"; fi
if [ -f "config.txt" ]; then echo "El archivo existe"; fi
if [ -d "/var/log" ]; then echo "El directorio existe"; fi
if [ -z "$variable" ]; then echo "La variable está vacía"; fi
if [ -x "script.sh" ]; then echo "El script es ejecutable"; fi

# Operadores lógicos:
if [ $edad -ge 18 ] && [ "$pais" = "España" ]; then
    echo "Adulto español"
fi

if [ "$color" = "rojo" ] || [ "$color" = "azul" ]; then
    echo "Color primario"
fi
```

### Bucles

```bash
#!/bin/bash

# for - iterar sobre lista:
for fruta in manzana naranja plátano; do
    echo "Me gusta la $fruta"
done

# for - rango de números:
for i in {1..10}; do
    echo "Número: $i"
done

# for - con paso:
for i in {0..20..5}; do    # de 0 a 20 de 5 en 5
    echo $i
done

# for - estilo C:
for ((i=0; i<5; i++)); do
    echo "Iteración $i"
done

# for - iterar sobre archivos:
for archivo in *.txt; do
    echo "Procesando: $archivo"
    # wc -l "$archivo"
done

# while:
contador=0
while [ $contador -lt 5 ]; do
    echo "Contador: $contador"
    ((contador++))
done

# until (hasta que se cumpla la condición):
until [ $contador -eq 10 ]; do
    echo "Valor: $contador"
    ((contador++))
done

# break y continue:
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        continue    # saltar el 5
    fi
    if [ $i -eq 8 ]; then
        break       # parar en el 8
    fi
    echo $i
done
```

### Funciones

```bash
#!/bin/bash

# Definir función:
saludar() {
    echo "¡Hola, $1!"
}

# Función con valor de retorno:
suma() {
    local resultado=$(( $1 + $2 ))
    echo $resultado
}

# Función más compleja:
crear_backup() {
    local origen="$1"
    local destino="$2"
    local fecha=$(date +%Y%m%d_%H%M%S)

    if [ ! -d "$origen" ]; then
        echo "ERROR: El directorio origen no existe: $origen"
        return 1
    fi

    mkdir -p "$destino"
    cp -r "$origen" "$destino/backup_$fecha"
    echo "✅ Backup creado en: $destino/backup_$fecha"
    return 0
}

# Llamar funciones:
saludar "Mundo"
resultado=$(suma 5 3)
echo "5 + 3 = $resultado"
crear_backup "/home/alice/proyectos" "/backup"
```

### Manejo de errores

```bash
#!/bin/bash

# Salir si algún comando falla:
set -e

# Tratar variables no definidas como error:
set -u

# Mostrar cada comando antes de ejecutarlo (debug):
set -x

# Combinación recomendada para scripts robustos:
set -euo pipefail

# Función de limpieza al salir:
cleanup() {
    echo "Limpiando archivos temporales..."
    rm -f /tmp/mi_temp_$$
}
trap cleanup EXIT

# Capturar errores específicos:
if ! mkdir /tmp/directorio_protegido 2>/dev/null; then
    echo "No se pudo crear el directorio"
    exit 1
fi

echo "Éxito"
exit 0
```

---

## 17. Compresión y archivado

### tar - Archivado

```bash
# Crear un archivo .tar (archivado sin compresión):
tar -cf archivo.tar directorio/

# Crear con compresión gzip (.tar.gz o .tgz):
tar -czf archivo.tar.gz directorio/

# Crear con compresión bzip2 (.tar.bz2):
tar -cjf archivo.tar.bz2 directorio/

# Crear con compresión xz (.tar.xz):
tar -cJf archivo.tar.xz directorio/

# Ver contenido sin descomprimir:
tar -tf archivo.tar.gz

# Extraer:
tar -xf archivo.tar           # extraer .tar
tar -xzf archivo.tar.gz       # extraer .tar.gz
tar -xjf archivo.tar.bz2      # extraer .tar.bz2
tar -xJf archivo.tar.xz       # extraer .tar.xz

# Extraer en directorio específico:
tar -xzf archivo.tar.gz -C /destino/

# Extraer solo un archivo:
tar -xzf archivo.tar.gz archivo-especifico.txt
```

```
Opciones de tar:
c → create (crear)
x → extract (extraer)
t → list (listar)
f → file (especificar nombre de archivo)
z → gzip
j → bzip2
J → xz
v → verbose (mostrar progreso)
```

### zip y unzip

```bash
# Comprimir:
zip archivo.zip archivo1.txt archivo2.txt
zip -r archivo.zip directorio/          # recursivo
zip -9 archivo.zip archivo.txt          # máxima compresión

# Descomprimir:
unzip archivo.zip                       # en directorio actual
unzip archivo.zip -d /destino/          # en directorio específico
unzip -l archivo.zip                    # ver contenido sin extraer
unzip -o archivo.zip                    # sobrescribir sin preguntar
```

### gzip y bzip2

```bash
# gzip - comprimir/descomprimir archivos individuales:
gzip archivo.txt           # comprime y elimina el original → archivo.txt.gz
gzip -k archivo.txt        # mantener el original
gzip -d archivo.txt.gz     # descomprimir
gunzip archivo.txt.gz      # igual que gzip -d

# bzip2 - mejor compresión, más lento:
bzip2 archivo.txt          # → archivo.txt.bz2
bunzip2 archivo.txt.bz2    # descomprimir
```

---

## 18. SSH y administración remota

**SSH** (Secure Shell) permite conectarse de forma segura a servidores remotos.

### Conexión básica

```bash
# Conectarse a un servidor:
ssh usuario@servidor.com
ssh alice@192.168.1.100
ssh -p 2222 alice@servidor.com   # puerto no estándar

# Ejecutar un comando remoto sin abrir shell:
ssh alice@servidor.com "ls -la /var/www"
ssh alice@servidor.com "sudo systemctl status nginx"

# Copiar archivos con SCP:
scp archivo.txt alice@servidor.com:/home/alice/       # local → remoto
scp alice@servidor.com:/home/alice/archivo.txt .      # remoto → local
scp -r directorio/ alice@servidor.com:/home/alice/    # directorio entero

# rsync - sincronización eficiente (solo copia cambios):
rsync -avz directorio/ alice@servidor.com:/backup/
rsync -avz --delete directorio/ alice@servidor.com:/backup/  # eliminar archivos borrados
rsync -avz -e "ssh -p 2222" directorio/ alice@servidor.com:/backup/
```

### Autenticación con clave SSH

```bash
# 1. Generar par de claves:
ssh-keygen -t ed25519 -C "tu@email.com"   # moderna y segura (recomendada)
ssh-keygen -t rsa -b 4096 -C "tu@email.com"  # RSA clásico

# Se crean dos archivos:
# ~/.ssh/id_ed25519      → clave PRIVADA (¡nunca compartir!)
# ~/.ssh/id_ed25519.pub  → clave PÚBLICA (se copia al servidor)

# 2. Copiar clave pública al servidor:
ssh-copy-id alice@servidor.com
ssh-copy-id -i ~/.ssh/id_ed25519.pub alice@servidor.com

# 3. Ahora puedes conectarte sin contraseña:
ssh alice@servidor.com

# Ver tus claves:
ls -la ~/.ssh/
cat ~/.ssh/id_ed25519.pub
```

### Configuración SSH

```bash
# Crear/editar ~/.ssh/config para simplificar conexiones:
nano ~/.ssh/config
```

```
# Configuración de ejemplo:
Host miservidor
    HostName 192.168.1.100
    User alice
    Port 22
    IdentityFile ~/.ssh/id_ed25519

Host servidor-trabajo
    HostName trabajo.empresa.com
    User admin
    Port 2222
    IdentityFile ~/.ssh/id_trabajo

# Ahora puedes conectarte simplemente con:
# ssh miservidor
# ssh servidor-trabajo
```

```bash
# Permisos correctos para SSH (¡obligatorio!):
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/config
```

### Túneles SSH

```bash
# Túnel local (acceder a servicio remoto en local):
ssh -L 8080:localhost:80 alice@servidor.com
# Ahora puedes abrir http://localhost:8080 para acceder al web del servidor

# Túnel remoto (exponer servicio local en servidor):
ssh -R 9090:localhost:3000 alice@servidor.com
# Alguien en el servidor puede acceder a tu app local en el puerto 9090

# SOCKS proxy:
ssh -D 1080 alice@servidor.com
# Configura tu navegador para usar SOCKS5 en localhost:1080
```

---

## 19. Cron: tareas programadas

**Cron** permite ejecutar comandos o scripts de forma automática en momentos específicos.

### Sintaxis de cron

```
 ┌─────────────── minuto (0-59)
 │  ┌──────────── hora (0-23)
 │  │  ┌───────── día del mes (1-31)
 │  │  │  ┌────── mes (1-12)
 │  │  │  │  ┌─── día de la semana (0-7, donde 0 y 7 son domingo)
 │  │  │  │  │
 *  *  *  *  *   comando a ejecutar
```

### Ejemplos de expresiones cron

```
Expresión         Significado
0 * * * *       → cada hora (en punto)
*/15 * * * *    → cada 15 minutos
0 9 * * 1-5     → lunes a viernes a las 9:00
0 0 * * 0       → todos los domingos a medianoche
0 2 * * *       → todos los días a las 2:00 AM
0 0 1 * *       → primer día de cada mes a medianoche
0 0 1 1 *       → el 1 de enero a medianoche
*/5 9-17 * * 1-5 → cada 5 min, de 9 a 17h, lunes a viernes
```

### Gestionar el crontab

```bash
# Editar el crontab del usuario actual:
crontab -e          # abre el editor (primera vez pregunta qué editor)

# Ver el crontab actual:
crontab -l

# Eliminar todo el crontab:
crontab -r

# Ver crontab de otro usuario (requiere root):
sudo crontab -u alice -l
```

### Ejemplos prácticos

```bash
# Añadir estas líneas con: crontab -e

# Backup diario a las 2 AM:
0 2 * * * tar -czf /backup/home-$(date +\%Y\%m\%d).tar.gz /home/alice/

# Actualizar el sistema cada domingo a las 3 AM:
0 3 * * 0 sudo apt update && sudo apt upgrade -y >> /var/log/actualizacion.log 2>&1

# Limpiar /tmp cada día a medianoche:
0 0 * * * find /tmp -type f -mtime +7 -delete

# Verificar espacio en disco cada hora:
0 * * * * df -h > /tmp/espacio_disco.txt

# Ejecutar script propio cada 5 minutos:
*/5 * * * * /home/alice/scripts/monitoreo.sh >> /home/alice/logs/monitoreo.log 2>&1
```

> 💡 **Tip:** Usa `https://crontab.guru/` para verificar y entender expresiones cron visualmente.

---

## 20. Trucos y atajos de productividad

### Historial de comandos

```bash
history                     # ver historial completo
history | tail -20          # últimos 20 comandos
history | grep git          # buscar comandos con "git"
!42                         # ejecutar el comando número 42
!!                          # repetir el último comando
!git                        # repetir el último comando que empieza con "git"
sudo !!                     # repetir el último comando con sudo
Ctrl+R                      # buscar en el historial (búsqueda inversa)
# Escribe algo, Ctrl+R para buscar más atrás, Enter para ejecutar
```

### Expansión de llaves

```bash
# Crear múltiples archivos/directorios de una vez:
mkdir -p proyecto/{src,tests,docs,config}
touch proyecto/src/{main,utils,config}.py
echo {1..5}           # 1 2 3 4 5
echo {a..e}           # a b c d e
cp archivo.conf{,.bak}  # copiar con extensión .bak
mv imagen.png{,.old}    # renombrar añadiendo .old
```

### Comandos con xargs

```bash
# xargs pasa la salida como argumentos al siguiente comando
find . -name "*.log" | xargs rm          # eliminar todos los .log
find . -name "*.py" | xargs grep "TODO"  # buscar TODO en todos los .py
ls *.txt | xargs -I{} cp {} /backup/     # copiar con nombre personalizado
echo "uno dos tres" | xargs -n1 echo    # uno por línea
find . -name "*.jpg" | xargs -P4 convert {} {}.png  # paralelo con 4 procesos
```

### Comandos útiles varios

```bash
# tee - mostrar Y guardar en archivo al mismo tiempo:
ls -la | tee listado.txt
./script.sh | tee -a log.txt   # -a para añadir (append)

# watch - repetir comando periódicamente:
watch -n 2 df -h               # actualizar cada 2 segundos
watch -n 1 "ps aux | grep nginx"

# screen / tmux - multiplexores de terminal:
# Instalar tmux:
sudo apt install tmux

tmux                           # iniciar sesión
tmux new -s trabajo            # nueva sesión con nombre
tmux ls                        # listar sesiones
tmux attach -t trabajo         # reconectar a sesión
# Dentro de tmux:
# Ctrl+B %    → dividir pantalla vertical
# Ctrl+B "    → dividir pantalla horizontal
# Ctrl+B ←→   → moverse entre paneles
# Ctrl+B D    → desconectarse (la sesión sigue corriendo)

# column - formatear en columnas:
cat /etc/passwd | column -t -s:

# sort y uniq:
sort archivo.txt               # ordenar alfabéticamente
sort -n numeros.txt            # ordenar numéricamente
sort -r archivo.txt            # orden inverso
sort -u archivo.txt            # eliminar duplicados (sort + uniq)
uniq archivo.txt               # eliminar líneas duplicadas consecutivas
uniq -c archivo.txt            # contar ocurrencias

# cut - extraer columnas:
cut -d: -f1 /etc/passwd        # primer campo separado por :
cut -d, -f2,4 datos.csv        # campos 2 y 4 de un CSV
cut -c1-10 archivo.txt         # caracteres 1 al 10 de cada línea

# tr - traducir/eliminar caracteres:
echo "hola mundo" | tr a-z A-Z     # mayúsculas
echo "hola   mundo" | tr -s ' '    # comprimir espacios
cat archivo.txt | tr -d '\r'        # eliminar retornos de carro (Windows)
```

---

## 21. Proyecto práctico: Script de administración

Vamos a crear un **script de administración del sistema** que demuestra todo lo aprendido.

### Objetivo

Crear un script interactivo que:
- Muestre información del sistema
- Haga backups automatizados
- Monitoree el espacio en disco
- Gestione logs antiguos

```bash
#!/bin/bash
# =============================================================
# sysadmin.sh - Script de Administración del Sistema
# Autor: Tu nombre
# Descripción: Herramienta para administración básica del sistema
# =============================================================

set -euo pipefail

# ─── COLORES ──────────────────────────────────────────────────
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
CYAN='\033[0;36m'
NC='\033[0m'   # No Color

# ─── CONFIGURACIÓN ────────────────────────────────────────────
BACKUP_DIR="${HOME}/backups"
LOG_DIR="${HOME}/logs"
LOG_FILE="${LOG_DIR}/sysadmin_$(date +%Y%m%d).log"
DISK_THRESHOLD=80   # porcentaje de uso que activa la alerta

# ─── FUNCIONES DE UTILIDAD ────────────────────────────────────
log() {
    local nivel="$1"
    local mensaje="$2"
    local timestamp
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$timestamp] [$nivel] $mensaje" >> "$LOG_FILE"
}

imprimir_titulo() {
    echo ""
    echo -e "${CYAN}╔══════════════════════════════════════╗${NC}"
    echo -e "${CYAN}║${NC}  ${BLUE}$1${NC}"
    echo -e "${CYAN}╚══════════════════════════════════════╝${NC}"
}

imprimir_ok() {
    echo -e "  ${GREEN}✓${NC} $1"
}

imprimir_error() {
    echo -e "  ${RED}✗ ERROR:${NC} $1"
    log "ERROR" "$1"
}

imprimir_advertencia() {
    echo -e "  ${YELLOW}⚠ ADVERTENCIA:${NC} $1"
    log "WARN" "$1"
}

# ─── FUNCIÓN: INFORMACIÓN DEL SISTEMA ─────────────────────────
mostrar_info_sistema() {
    imprimir_titulo "INFORMACIÓN DEL SISTEMA"

    echo ""
    echo -e "  ${BLUE}Sistema Operativo:${NC}"
    echo "    $(grep PRETTY_NAME /etc/os-release | cut -d'"' -f2)"

    echo -e "  ${BLUE}Kernel:${NC}"
    echo "    $(uname -r)"

    echo -e "  ${BLUE}Hostname:${NC}"
    echo "    $(hostname)"

    echo -e "  ${BLUE}Uptime:${NC}"
    echo "    $(uptime -p)"

    echo -e "  ${BLUE}CPU:${NC}"
    echo "    $(lscpu | grep 'Model name' | cut -d: -f2 | xargs)"

    echo -e "  ${BLUE}Memoria RAM:${NC}"
    free -h | awk '/^Mem:/ {printf "    Total: %s  Usada: %s  Libre: %s\n", $2, $3, $4}'

    echo -e "  ${BLUE}IP Local:${NC}"
    echo "    $(hostname -I | awk '{print $1}')"

    log "INFO" "Información del sistema mostrada"
}

# ─── FUNCIÓN: ESPACIO EN DISCO ────────────────────────────────
verificar_disco() {
    imprimir_titulo "ESPACIO EN DISCO"
    echo ""

    while IFS= read -r linea; do
        # Extraer el porcentaje de uso
        porcentaje=$(echo "$linea" | awk '{print $5}' | tr -d '%')
        particion=$(echo "$linea" | awk '{print $1}')

        if [[ "$porcentaje" =~ ^[0-9]+$ ]]; then
            if [ "$porcentaje" -ge "$DISK_THRESHOLD" ]; then
                echo -e "  ${RED}⚠  $linea${NC}"
                imprimir_advertencia "Disco $particion al ${porcentaje}% de capacidad"
            else
                echo -e "  ${GREEN}✓${NC}  $linea"
            fi
        fi
    done < <(df -h | grep -v tmpfs | grep -v udev | tail -n +2)

    log "INFO" "Verificación de disco completada"
}

# ─── FUNCIÓN: BACKUP ──────────────────────────────────────────
hacer_backup() {
    local origen="${1:-$HOME/Documentos}"
    local fecha
    fecha=$(date +%Y%m%d_%H%M%S)
    local nombre_backup="backup_${fecha}.tar.gz"
    local ruta_completa="${BACKUP_DIR}/${nombre_backup}"

    imprimir_titulo "REALIZANDO BACKUP"
    echo ""

    # Verificar que existe el origen
    if [ ! -d "$origen" ]; then
        imprimir_error "El directorio origen no existe: $origen"
        return 1
    fi

    # Crear directorio de backups si no existe
    mkdir -p "$BACKUP_DIR"

    echo "  Origen: $origen"
    echo "  Destino: $ruta_completa"
    echo ""

    # Calcular tamaño antes
    local tamanio
    tamanio=$(du -sh "$origen" 2>/dev/null | cut -f1)
    echo "  Tamaño a comprimir: $tamanio"

    # Realizar el backup
    echo ""
    echo -n "  Creando backup..."
    if tar -czf "$ruta_completa" -C "$(dirname "$origen")" "$(basename "$origen")" 2>/dev/null; then
        local tamanio_final
        tamanio_final=$(du -sh "$ruta_completa" | cut -f1)
        imprimir_ok "Backup creado: $nombre_backup ($tamanio_final)"
        log "INFO" "Backup creado: $ruta_completa"
    else
        imprimir_error "Falló la creación del backup"
        return 1
    fi

    # Mostrar backups existentes:
    echo ""
    echo "  Backups disponibles:"
    ls -lh "$BACKUP_DIR"/*.tar.gz 2>/dev/null | awk '{print "    " $NF " (" $5 ")"}'
}

# ─── FUNCIÓN: LIMPIAR LOGS ────────────────────────────────────
limpiar_logs() {
    local dias="${1:-30}"

    imprimir_titulo "LIMPIEZA DE LOGS ANTIGUOS"
    echo ""
    echo "  Buscando logs de más de $dias días..."
    echo ""

    local count=0
    while IFS= read -r archivo; do
        echo "  Eliminando: $archivo"
        rm -f "$archivo"
        ((count++)) || true
    done < <(find /tmp -name "*.log" -type f -mtime +"$dias" 2>/dev/null)

    if [ $count -eq 0 ]; then
        imprimir_ok "No hay logs antiguos que limpiar"
    else
        imprimir_ok "Se eliminaron $count archivos de log"
    fi

    log "INFO" "Limpieza de logs completada: $count archivos eliminados"
}

# ─── FUNCIÓN: PROCESOS ────────────────────────────────────────
mostrar_procesos() {
    imprimir_titulo "TOP 10 PROCESOS (por CPU)"
    echo ""
    ps aux --sort=-%cpu | head -11 | awk 'NR==1{print "  " $0} NR>1{print "  " $0}'
}

# ─── MENÚ PRINCIPAL ───────────────────────────────────────────
mostrar_menu() {
    echo ""
    echo -e "${CYAN}╔══════════════════════════════════════╗${NC}"
    echo -e "${CYAN}║${NC}  ${BLUE}🐧 ADMINISTRADOR DEL SISTEMA${NC}        ${CYAN}║${NC}"
    echo -e "${CYAN}╠══════════════════════════════════════╣${NC}"
    echo -e "${CYAN}║${NC}  1. Información del sistema           ${CYAN}║${NC}"
    echo -e "${CYAN}║${NC}  2. Verificar espacio en disco        ${CYAN}║${NC}"
    echo -e "${CYAN}║${NC}  3. Realizar backup                   ${CYAN}║${NC}"
    echo -e "${CYAN}║${NC}  4. Limpiar logs antiguos             ${CYAN}║${NC}"
    echo -e "${CYAN}║${NC}  5. Mostrar procesos activos          ${CYAN}║${NC}"
    echo -e "${CYAN}║${NC}  6. Ejecutar todo                     ${CYAN}║${NC}"
    echo -e "${CYAN}║${NC}  0. Salir                             ${CYAN}║${NC}"
    echo -e "${CYAN}╚══════════════════════════════════════╝${NC}"
    echo ""
}

main() {
    # Inicializar directorio de logs
    mkdir -p "$LOG_DIR"

    log "INFO" "Script iniciado por $(whoami)"

    while true; do
        mostrar_menu
        read -rp "  Elige una opción [0-6]: " opcion

        case $opcion in
            1) mostrar_info_sistema ;;
            2) verificar_disco ;;
            3)
                read -rp "  Directorio a respaldar [$HOME/Documentos]: " dir
                hacer_backup "${dir:-$HOME/Documentos}"
                ;;
            4)
                read -rp "  ¿Limpiar logs de más de cuántos días? [30]: " dias
                limpiar_logs "${dias:-30}"
                ;;
            5) mostrar_procesos ;;
            6)
                mostrar_info_sistema
                verificar_disco
                mostrar_procesos
                ;;
            0)
                echo ""
                echo -e "  ${GREEN}¡Hasta luego!${NC}"
                log "INFO" "Script terminado"
                exit 0
                ;;
            *)
                imprimir_advertencia "Opción no válida: $opcion"
                ;;
        esac

        echo ""
        read -rp "  Presiona Enter para continuar..."
    done
}

main "$@"
```

### Instrucciones para el proyecto

```bash
# 1. Guardar el script:
mkdir -p ~/scripts
nano ~/scripts/sysadmin.sh
# (pegar el contenido del script)

# 2. Hacerlo ejecutable:
chmod +x ~/scripts/sysadmin.sh

# 3. Ejecutar:
~/scripts/sysadmin.sh

# 4. Opcional - añadir al PATH:
echo 'export PATH="$PATH:$HOME/scripts"' >> ~/.bashrc
source ~/.bashrc
sysadmin.sh    # ejecutar desde cualquier lugar
```

---

## 22. Referencia rápida de comandos

### Navegación y archivos

| Comando | Descripción |
|---------|-------------|
| `pwd` | Mostrar directorio actual |
| `ls -la` | Listar archivos con detalles |
| `cd /ruta` | Cambiar de directorio |
| `mkdir -p dir` | Crear directorio (con padres) |
| `touch archivo` | Crear archivo vacío |
| `cp -r src dst` | Copiar (recursivo) |
| `mv src dst` | Mover/renombrar |
| `rm -rf dir` | Eliminar recursivamente |
| `find . -name "*.txt"` | Buscar archivos |
| `du -sh *` | Ver tamaño de elementos |
| `df -h` | Ver espacio en disco |

### Permisos y usuarios

| Comando | Descripción |
|---------|-------------|
| `chmod 755 archivo` | Cambiar permisos |
| `chown user:group archivo` | Cambiar propietario |
| `sudo comando` | Ejecutar como root |
| `su usuario` | Cambiar de usuario |
| `whoami` | Ver usuario actual |
| `id` | Ver ID y grupos |
| `adduser nombre` | Crear usuario |
| `passwd nombre` | Cambiar contraseña |

### Procesos y sistema

| Comando | Descripción |
|---------|-------------|
| `ps aux` | Ver todos los procesos |
| `top` / `htop` | Monitor en tiempo real |
| `kill PID` | Terminar proceso |
| `kill -9 PID` | Forzar terminación |
| `nohup cmd &` | Ejecutar en background |
| `jobs` | Ver procesos en background |
| `uname -r` | Ver versión del kernel |
| `uptime` | Tiempo de funcionamiento |
| `free -h` | Ver uso de memoria |

### Red y conectividad

| Comando | Descripción |
|---------|-------------|
| `ip addr show` | Ver interfaces y IPs |
| `ping host` | Probar conectividad |
| `ssh user@host` | Conectar por SSH |
| `scp src dst` | Copiar por SSH |
| `curl URL` | Petición HTTP |
| `wget URL` | Descargar archivo |
| `ss -tuln` | Ver puertos abiertos |

### Texto y búsqueda

| Comando | Descripción |
|---------|-------------|
| `cat archivo` | Ver contenido |
| `less archivo` | Ver con paginación |
| `head -n 10` | Primeras 10 líneas |
| `tail -f log` | Seguir log en tiempo real |
| `grep "patrón" archivo` | Buscar texto |
| `grep -r "texto" dir/` | Buscar recursivamente |
| `sed 's/viejo/nuevo/g'` | Reemplazar texto |
| `wc -l archivo` | Contar líneas |
| `sort archivo` | Ordenar líneas |
| `uniq archivo` | Eliminar duplicados |

### Paquetes (Debian/Ubuntu)

| Comando | Descripción |
|---------|-------------|
| `sudo apt update` | Actualizar lista de paquetes |
| `sudo apt upgrade` | Actualizar paquetes instalados |
| `sudo apt install pkg` | Instalar paquete |
| `sudo apt remove pkg` | Eliminar paquete |
| `apt search nombre` | Buscar paquete |
| `apt show nombre` | Info de paquete |
| `apt list --installed` | Paquetes instalados |

---

## 🎓 Próximos pasos

¡Enhorabuena por llegar hasta aquí! Ahora conoces los fundamentos de Linux. Para seguir aprendiendo:

### Nivel intermedio
- **awk** - Procesamiento avanzado de texto
- **systemd** - Gestión de servicios (`systemctl start/stop/enable nginx`)
- **LVM** - Gestión lógica de volúmenes
- **Bash avanzado** - Arrays, expresiones regulares, funciones complejas
- **Python en Linux** - Automatización y scripts más potentes

### Nivel avanzado
- **Kubernetes/Docker** - Contenedores y orquestación
- **Ansible** - Automatización de configuración
- **Nginx/Apache** - Administración de servidores web
- **iptables/nftables** - Firewall avanzado
- **SELinux/AppArmor** - Seguridad obligatoria

### Recursos recomendados
- 📖 [The Linux Command Line (libro gratuito)](https://linuxcommand.org/tlcl.php)
- 🎮 [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) - Aprende Linux jugando
- 📺 [Linux Journey](https://linuxjourney.com/) - Tutorial interactivo
- 🖥️ [explainshell.com](https://explainshell.com/) - Explica cada parte de un comando
- 📚 `man nombre_comando` - El manual oficial de cada comando
- 💬 [r/linux4noobs](https://www.reddit.com/r/linux4noobs/) - Comunidad de principiantes

---

> 🐧 **¡Linux es un viaje, no un destino!** Cuanto más lo uses, más descubrirás. La mejor forma de aprender es practicando: instala una VM, rompe cosas, arréglales y aprende del proceso.
