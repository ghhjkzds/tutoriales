# 🖥️ Tutorial de Windows, CMD y PowerShell

Guía práctica y sencilla para aprender a moverte con soltura por Windows, dominar el símbolo del sistema (CMD) y dar tus primeros pasos con PowerShell.

---

## 📋 Tabla de Contenidos

1. [Introducción a Windows](#introducción-a-windows)
2. [El Escritorio y la Interfaz Gráfica](#el-escritorio-y-la-interfaz-gráfica)
3. [Gestión de Archivos y Carpetas](#gestión-de-archivos-y-carpetas)
4. [El Símbolo del Sistema (CMD)](#el-símbolo-del-sistema-cmd)
5. [Comandos Esenciales de CMD](#comandos-esenciales-de-cmd)
6. [Variables de Entorno y Configuración](#variables-de-entorno-y-configuración)
7. [Introducción a PowerShell](#introducción-a-powershell)
8. [Comandos Esenciales de PowerShell](#comandos-esenciales-de-powershell)
9. [Automatización con Scripts .bat y .ps1](#automatización-con-scripts-bat-y-ps1)
10. [Administración del Sistema](#administración-del-sistema)
11. [Ejercicios Prácticos](#ejercicios-prácticos)

---

## Introducción a Windows

### ¿Qué es Windows?

Windows es el sistema operativo de escritorio más utilizado en el mundo, desarrollado por Microsoft. Gestiona el hardware del equipo y proporciona una interfaz gráfica para que los usuarios interactúen con programas y archivos.

### Versiones Más Comunes

| Versión | Año | Características Clave |
|---------|-----|----------------------|
| Windows 7 | 2009 | Estabilidad, bajo consumo de recursos |
| Windows 10 | 2015 | Actualizaciones continuas, tienda de apps |
| Windows 11 | 2021 | Nuevo diseño, requisitos de hardware actualizados |

### Conceptos Clave

- **Sistema de archivos**: Windows utiliza principalmente **NTFS** para organizar archivos en disco.
- **Unidades**: Las unidades de almacenamiento se identifican con letras (`C:`, `D:`, `E:`...).
- **Rutas absolutas y relativas**:
  - Absoluta: `C:\Users\Alumno\Documentos\archivo.txt`
  - Relativa: `Documentos\archivo.txt` (desde la carpeta del usuario)

---

## El Escritorio y la Interfaz Gráfica

### Elementos Principales

```
┌──────────────────────────────────────────────────────┐
│  Barra de tareas                          🔋🔊📶 12:00│
│  ┌──────┐  ┌──────┐  ┌──────┐                        │
│  │ Icono│  │ Icono│  │ Icono│   ← Accesos directos   │
│  └──────┘  └──────┘  └──────┘                        │
│                                                      │
│                   ESCRITORIO                         │
│                                                      │
├──────────────────────────────────────────────────────┤
│ 🪟  🔍 Buscar  📁  🌐  ...         🔔 🔋  11:30 AM   │
└──────────────────────────────────────────────────────┘
```

### Atajos de Teclado Imprescindibles

| Atajo | Acción |
|-------|--------|
| `Win` | Abre el menú Inicio |
| `Win + E` | Abre el Explorador de archivos |
| `Win + D` | Muestra/oculta el escritorio |
| `Win + L` | Bloquea el equipo |
| `Win + R` | Abre el cuadro de diálogo Ejecutar |
| `Alt + F4` | Cierra la ventana activa |
| `Ctrl + C / V / X` | Copiar / Pegar / Cortar |
| `Ctrl + Z` | Deshacer |
| `Ctrl + Alt + Supr` | Menú de seguridad (Administrador de tareas, etc.) |
| `Win + Pausa` | Abre Propiedades del sistema |

### Ejecutar Programas Rápidamente (`Win + R`)

El cuadro **Ejecutar** permite abrir herramientas del sistema sin buscarlo:

| Comando | Abre |
|---------|------|
| `cmd` | Símbolo del sistema |
| `powershell` | Windows PowerShell |
| `notepad` | Bloc de notas |
| `calc` | Calculadora |
| `msconfig` | Configuración del sistema |
| `regedit` | Editor del Registro |
| `taskmgr` | Administrador de tareas |
| `control` | Panel de control |
| `mspaint` | Paint |

---

## Gestión de Archivos y Carpetas

### Explorador de Archivos

Abre el Explorador con `Win + E`. Su estructura principal:

```
📁 Este equipo
├── 📁 Escritorio
├── 📁 Documentos
├── 📁 Descargas
├── 📁 Imágenes
├── 💾 Disco local (C:)
│   ├── 📁 Users
│   │   └── 📁 TuUsuario
│   ├── 📁 Windows
│   └── 📁 Program Files
└── 💽 Disco D: (si existe)
```

### Operaciones Básicas con Archivos

| Acción | Cómo hacerlo |
|--------|-------------|
| Crear carpeta | Clic derecho → Nuevo → Carpeta |
| Renombrar | Seleccionar + tecla `F2` |
| Copiar | `Ctrl + C` y después `Ctrl + V` en destino |
| Mover | `Ctrl + X` y después `Ctrl + V` en destino |
| Eliminar | Seleccionar + tecla `Supr` |
| Eliminar definitivamente | Seleccionar + `Shift + Supr` |
| Ver propiedades | Clic derecho → Propiedades |

### Extensiones de Archivo Comunes

| Extensión | Tipo de archivo |
|-----------|----------------|
| `.txt` | Texto plano |
| `.docx` | Documento Word |
| `.xlsx` | Hoja de cálculo Excel |
| `.pdf` | Documento PDF |
| `.exe` | Programa ejecutable |
| `.zip / .rar` | Archivo comprimido |
| `.jpg / .png` | Imagen |
| `.mp4 / .avi` | Vídeo |
| `.bat` | Script de comandos de Windows |

---

## El Símbolo del Sistema (CMD)

### ¿Qué es CMD?

CMD (Command Prompt o símbolo del sistema) es la interfaz de línea de comandos clásica de Windows. Permite ejecutar instrucciones de texto para gestionar archivos, la red, procesos y mucho más, sin necesidad de ratón.

### Cómo Abrir CMD

1. **`Win + R`** → escribe `cmd` → `Enter`
2. **Barra de búsqueda** → escribe `cmd` → `Enter`
3. **Como Administrador** → clic derecho en CMD → *Ejecutar como administrador*

### Anatomía del Prompt

```
C:\Users\Alumno>_
│               │└─ Cursor: aquí escribes
│               └─ Símbolo >
└─ Ruta actual (directorio de trabajo)
```

> 💡 **Consejo**: Cuando el prompt muestre `C:\Windows\system32>` es porque abriste CMD como administrador.

---

## Comandos Esenciales de CMD

### Navegación por Directorios

```cmd
:: Ver en qué carpeta estás
cd

:: Cambiar de directorio
cd Documentos
cd C:\Users\Alumno\Proyectos

:: Subir un nivel (ir a la carpeta padre)
cd ..

:: Ir a la raíz de la unidad
cd \

:: Cambiar de unidad (de C: a D:)
D:
```

### Listar y Explorar

```cmd
:: Listar archivos y carpetas del directorio actual
dir

:: Listar con detalles (tamaño, fecha)
dir /o:n

:: Listar incluyendo archivos ocultos
dir /a

:: Listar en formato corto
dir /w
```

Ejemplo de salida de `dir`:
```
 El volumen de la unidad C es Windows
 El número de serie del volumen es: 1A2B-3C4D

 Directorio de C:\Users\Alumno

14/01/2025  10:32    <DIR>          .
14/01/2025  10:32    <DIR>          ..
12/01/2025  09:15    <DIR>          Documentos
13/01/2025  14:20        15.360     notas.txt
               1 archivos         15.360 bytes
               3 dirs  50.000.000.000 bytes libres
```

### Crear, Copiar, Mover y Eliminar

```cmd
:: Crear una carpeta nueva
mkdir MiCarpeta
md Proyectos\Web

:: Eliminar una carpeta vacía
rmdir MiCarpeta

:: Eliminar una carpeta y todo su contenido (¡cuidado!)
rmdir /s /q MiCarpeta

:: Crear un archivo de texto vacío
echo. > archivo.txt

:: Escribir texto en un archivo (sobrescribe)
echo Hola mundo > saludo.txt

:: Añadir texto a un archivo (sin sobrescribir)
echo Segunda línea >> saludo.txt

:: Mostrar el contenido de un archivo
type saludo.txt

:: Copiar un archivo
copy origen.txt destino.txt
copy origen.txt C:\Destino\

:: Mover (o renombrar) un archivo
move archivo.txt NuevoNombre.txt
move archivo.txt C:\OtraCarpeta\

:: Eliminar un archivo
del archivo.txt

:: Eliminar todos los .txt de la carpeta actual
del *.txt
```

### Comandos de Red

```cmd
:: Ver la configuración de red (IP, máscara, gateway)
ipconfig

:: Información detallada (incluye MAC y DNS)
ipconfig /all

:: Limpiar caché DNS
ipconfig /flushdns

:: Hacer ping a un host (comprobar conectividad)
ping google.com
ping 8.8.8.8

:: Trazar la ruta hasta un destino
tracert google.com

:: Ver conexiones de red activas
netstat -an

:: Ver la tabla de enrutamiento
route print
```

### Gestión de Procesos

```cmd
:: Listar todos los procesos en ejecución
tasklist

:: Buscar un proceso concreto
tasklist | findstr notepad

:: Terminar un proceso por nombre
taskkill /im notepad.exe /f

:: Terminar un proceso por PID
taskkill /pid 1234 /f
```

### Comandos del Sistema

```cmd
:: Limpiar la pantalla
cls

:: Ver la versión de Windows
ver
winver

:: Mostrar o cambiar la fecha
date

:: Mostrar o cambiar la hora
time

:: Información detallada del sistema
systeminfo

:: Apagar el equipo
shutdown /s /t 0

:: Reiniciar el equipo
shutdown /r /t 0

:: Cancelar un apagado programado
shutdown /a

:: Ejecutar un comando como administrador (desde CMD normal)
runas /user:Administrador cmd
```

### Búsqueda de Texto (findstr)

```cmd
:: Buscar una palabra en un archivo
findstr "error" registro.log

:: Busca sin distinguir mayúsculas/minúsculas
findstr /i "error" registro.log

:: Buscar en varios archivos
findstr "TODO" *.txt

:: Buscar recursivamente en subdirectorios
findstr /s "hola" *.txt
```

### Ayuda en CMD

```cmd
:: Ayuda general (lista todos los comandos)
help

:: Ayuda de un comando concreto
help dir
dir /?
copy /?
```

---

## Variables de Entorno y Configuración

### Variables de Entorno Importantes

Las variables de entorno almacenan información del sistema que los programas pueden consultar:

```cmd
:: Ver todas las variables de entorno
set

:: Ver el valor de una variable
echo %VARIABLE%

:: Ejemplos
echo %USERNAME%      → nombre de usuario actual
echo %USERPROFILE%   → ruta de la carpeta del usuario (C:\Users\Alumno)
echo %TEMP%          → carpeta de archivos temporales
echo %SystemRoot%    → ruta de Windows (normalmente C:\Windows)
echo %PATH%          → rutas donde Windows busca ejecutables
echo %COMPUTERNAME%  → nombre del equipo
echo %OS%            → sistema operativo
```

### Definir una Variable Temporal (solo en la sesión actual)

```cmd
set MI_VARIABLE=Hola
echo %MI_VARIABLE%
```

### El PATH: Cómo Windows Encuentra los Programas

Cuando escribes un comando como `python` o `git`, Windows lo busca en cada carpeta listada en la variable `%PATH%`. Puedes añadir una ruta para esta sesión:

```cmd
set PATH=%PATH%;C:\MiPrograma\bin
```

Para que sea permanente, ve a:
**Panel de control → Sistema → Configuración avanzada → Variables de entorno**

---

## Introducción a PowerShell

### ¿Qué es PowerShell?

PowerShell es la shell moderna de Windows (y multiplataforma desde PowerShell Core). Es mucho más potente que CMD porque:

- Trabaja con **objetos** en lugar de texto plano.
- Incluye miles de **cmdlets** (comandos especializados).
- Permite scripting avanzado con bucles, funciones y módulos.

### Cómo Abrir PowerShell

1. **`Win + R`** → escribe `powershell` → `Enter`
2. **Clic derecho** sobre la carpeta en el Explorador → *Abrir terminal de PowerShell aquí*
3. **Buscar** "PowerShell" en el menú Inicio

### Diferencias con CMD

| Aspecto | CMD | PowerShell |
|---------|-----|-----------|
| Tipo de salida | Texto | Objetos |
| Scripting | Básico (.bat) | Avanzado (.ps1) |
| Comandos | Comandos clásicos | Cmdlets + compatibilidad CMD |
| Uso típico | Tareas sencillas | Automatización y administración |

---

## Comandos Esenciales de PowerShell

### Navegación y Archivos

Los comandos de PowerShell tienen alias que imitan a CMD y a Linux, por lo que muchos comandos que ya conoces funcionan también:

```powershell
# Ver el directorio actual
pwd
Get-Location

# Cambiar de directorio
cd C:\Users\Alumno\Documentos
Set-Location C:\Users\Alumno\Documentos

# Listar archivos y carpetas
ls
dir
Get-ChildItem

# Listar solo archivos .txt
Get-ChildItem -Filter *.txt

# Listar recursivamente
Get-ChildItem -Recurse

# Crear una carpeta
mkdir NuevaCarpeta
New-Item -ItemType Directory -Name NuevaCarpeta

# Crear un archivo
New-Item -ItemType File -Name notas.txt

# Copiar archivos
Copy-Item origen.txt destino.txt
Copy-Item C:\origen\* C:\destino\ -Recurse

# Mover archivos
Move-Item archivo.txt C:\OtraCarpeta\

# Eliminar archivos
Remove-Item archivo.txt
Remove-Item MiCarpeta -Recurse -Force

# Ver el contenido de un archivo
Get-Content notas.txt
cat notas.txt
```

### Trabajar con Texto y Filtrado

```powershell
# Buscar texto en archivos (equivalente a findstr)
Select-String -Path *.txt -Pattern "error"

# Filtrar resultados de un comando
Get-ChildItem | Where-Object { $_.Extension -eq ".txt" }

# Ordenar resultados
Get-ChildItem | Sort-Object Length

# Mostrar solo la N primeras líneas
Get-Content log.txt | Select-Object -First 20

# Contar líneas de un archivo
(Get-Content log.txt).Count
```

### Procesos y Servicios

```powershell
# Listar procesos
Get-Process

# Buscar un proceso concreto
Get-Process -Name notepad

# Detener un proceso
Stop-Process -Name notepad
Stop-Process -Id 1234

# Listar servicios
Get-Service

# Iniciar/detener un servicio (requiere admin)
Start-Service -Name "wuauserv"
Stop-Service -Name "wuauserv"
```

### Red

```powershell
# Ver configuración de red
Get-NetIPAddress
ipconfig    # también funciona en PowerShell

# Hacer ping
Test-Connection google.com
Test-Connection google.com -Count 4

# Ver conexiones activas
Get-NetTCPConnection

# Descargar un archivo de Internet
Invoke-WebRequest -Uri "https://ejemplo.com/archivo.zip" -OutFile "archivo.zip"
```

### Variables y Operaciones Básicas

```powershell
# Asignar una variable (se usa $ delante del nombre)
$nombre = "Alumno"
$numero = 42

# Mostrar valores
Write-Host "Hola, $nombre"
Write-Output $numero

# Operaciones aritméticas
$resultado = 10 + 5 * 2
Write-Host $resultado   # → 20

# Longitud de un string
$nombre.Length

# Convertir a mayúsculas/minúsculas
$nombre.ToUpper()
$nombre.ToLower()
```

---

## Automatización con Scripts .bat y .ps1

### Scripts de CMD (.bat)

Un archivo `.bat` es una secuencia de comandos CMD que se ejecutan en orden.

**Ejemplo 1 — Saludo básico:**
```bat
@echo off
echo ¡Hola! Este es mi primer script
echo Fecha y hora actual:
date /t
time /t
pause
```

> 💡 `@echo off` evita que se muestre cada comando antes de ejecutarlo. `pause` espera a que el usuario pulse una tecla.

**Ejemplo 2 — Crear estructura de proyecto:**
```bat
@echo off
set PROYECTO=%1
if "%PROYECTO%"=="" set PROYECTO=MiProyecto

echo Creando estructura del proyecto: %PROYECTO%
mkdir %PROYECTO%
mkdir %PROYECTO%\src
mkdir %PROYECTO%\docs
mkdir %PROYECTO%\tests
echo. > %PROYECTO%\README.txt
echo Estructura creada correctamente.
pause
```

Guarda este fichero como `crear-proyecto.bat` y ejecútalo con:
```
crear-proyecto.bat NombreProyecto
```

**Ejemplo 3 — Bucle básico:**
```bat
@echo off
for %%i in (1 2 3 4 5) do (
    echo Número: %%i
)
pause
```

### Scripts de PowerShell (.ps1)

**Ejemplo 1 — Saludo con fecha:**
```powershell
$nombre = "Alumno"
$fecha = Get-Date -Format "dd/MM/yyyy HH:mm"
Write-Host "¡Hola, $nombre! Son las $fecha"
```

**Ejemplo 2 — Limpiar archivos temporales:**
```powershell
$carpetaTemp = $env:TEMP
$archivos = Get-ChildItem -Path $carpetaTemp -Recurse -ErrorAction SilentlyContinue

Write-Host "Archivos temporales encontrados: $($archivos.Count)"

Remove-Item -Path "$carpetaTemp\*" -Recurse -Force -ErrorAction SilentlyContinue

Write-Host "Carpeta temporal limpiada."
```

**Ejemplo 3 — Hacer backup de una carpeta:**
```powershell
$origen  = "C:\Users\$env:USERNAME\Documentos"
$destino = "D:\Backup\Documentos_$(Get-Date -Format 'yyyyMMdd')"

Copy-Item -Path $origen -Destination $destino -Recurse -Force
Write-Host "Backup completado en: $destino"
```

### Política de Ejecución de Scripts en PowerShell

Por defecto, Windows puede bloquear la ejecución de scripts `.ps1`. Para permitirlos (abre PowerShell como administrador):

```powershell
# Ver la política actual
Get-ExecutionPolicy

# Permitir scripts locales
Set-ExecutionPolicy RemoteSigned

# Restaurar la política restrictiva
Set-ExecutionPolicy Restricted
```

---

## Administración del Sistema

### Administrador de Tareas

Abre con `Ctrl + Alt + Supr` → *Administrador de tareas*, o bien `taskmgr` desde Ejecutar.

Pestañas principales:

| Pestaña | Para qué sirve |
|---------|---------------|
| **Procesos** | Ver y terminar aplicaciones y procesos del sistema |
| **Rendimiento** | Gráficas de CPU, RAM, disco y red en tiempo real |
| **Inicio** | Programas que arrancan con Windows |
| **Servicios** | Iniciar y detener servicios del sistema |

### Gestión de Discos

```cmd
:: Comprobación de errores en disco (requiere reinicio)
chkdsk C: /f /r

:: Desfragmentar disco (solo HDD, no SSD)
defrag C:

:: Ver espacio en disco
wmic logicaldisk get caption,freespace,size
```

### Información del Sistema desde CMD/PowerShell

```cmd
:: Información detallada del sistema
systeminfo

:: Ver la arquitectura del procesador
echo %PROCESSOR_ARCHITECTURE%

:: Ver la RAM disponible (PowerShell)
Get-WmiObject -Class Win32_OperatingSystem | Select-Object FreePhysicalMemory, TotalVisibleMemorySize
```

### Gestión de Usuarios desde CMD (requiere admin)

```cmd
:: Listar usuarios locales
net user

:: Crear un usuario
net user NuevoUsuario Contraseña123 /add

:: Eliminar un usuario
net user NuevoUsuario /delete

:: Añadir usuario al grupo Administradores
net localgroup Administradores NuevoUsuario /add
```

### Firewall de Windows

```cmd
:: Ver estado del firewall
netsh advfirewall show allprofiles

:: Activar el firewall
netsh advfirewall set allprofiles state on

:: Permitir un programa a través del firewall (ejemplo: Python)
netsh advfirewall firewall add rule name="Python" dir=in action=allow program="C:\Python312\python.exe" enable=yes
```

---

## Ejercicios Prácticos

### 🟢 Nivel Básico

**Ejercicio 1 — Exploración con CMD**

1. Abre CMD y navega hasta tu carpeta de Documentos.
2. Crea una carpeta llamada `TutorialWindows`.
3. Dentro de ella, crea un archivo `notas.txt` con el texto "Aprendiendo CMD".
4. Muestra el contenido del archivo con `type`.
5. Lista el contenido de la carpeta con `dir`.

```cmd
cd %USERPROFILE%\Documents
mkdir TutorialWindows
cd TutorialWindows
echo Aprendiendo CMD > notas.txt
type notas.txt
dir
```

---

**Ejercicio 2 — Información de red**

1. Muestra tu dirección IP actual.
2. Haz ping a `8.8.8.8` (DNS de Google) para comprobar que tienes internet.
3. Realiza un `tracert` hacia `google.com` y observa los saltos.

```cmd
ipconfig
ping 8.8.8.8
tracert google.com
```

---

### 🟡 Nivel Intermedio

**Ejercicio 3 — Script de organización de archivos**

Crea un archivo `organizar.bat` que:
1. Cree las carpetas `Imágenes`, `Documentos` y `Otros` en el escritorio.
2. Muestre un mensaje de confirmación al terminar.

```bat
@echo off
set ESCRITORIO=%USERPROFILE%\Desktop

mkdir "%ESCRITORIO%\Imágenes"
mkdir "%ESCRITORIO%\Documentos"
mkdir "%ESCRITORIO%\Otros"

echo Carpetas creadas en el escritorio.
pause
```

---

**Ejercicio 4 — PowerShell: inventario del sistema**

Crea un script `inventario.ps1` que genere un archivo `informe.txt` con:
- Nombre del equipo
- Usuario actual
- Versión de Windows
- Memoria RAM total y libre

```powershell
$informe = @"
=== INVENTARIO DEL SISTEMA ===
Equipo    : $env:COMPUTERNAME
Usuario   : $env:USERNAME
Sistema   : $((Get-WmiObject Win32_OperatingSystem).Caption)
RAM total : $([math]::Round((Get-WmiObject Win32_OperatingSystem).TotalVisibleMemorySize / 1MB, 2)) GB
RAM libre : $([math]::Round((Get-WmiObject Win32_OperatingSystem).FreePhysicalMemory / 1MB, 2)) GB
Fecha     : $(Get-Date -Format 'dd/MM/yyyy HH:mm')
"@

$informe | Out-File -FilePath "informe.txt" -Encoding UTF8
Write-Host "Informe generado: informe.txt"
Get-Content informe.txt
```

---

### 🔴 Nivel Avanzado

**Ejercicio 5 — Monitorización de procesos**

Crea un script PowerShell `monitor.ps1` que:
1. Liste los 5 procesos que más CPU consumen.
2. Muestre la memoria RAM libre.
3. Se repita cada 5 segundos durante 3 iteraciones.

```powershell
for ($i = 1; $i -le 3; $i++) {
    Clear-Host
    Write-Host "=== Iteración $i/3 — $(Get-Date -Format 'HH:mm:ss') ===" -ForegroundColor Cyan

    Write-Host "`nTop 5 procesos por CPU:" -ForegroundColor Yellow
    Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 |
        Format-Table Name, CPU, WorkingSet -AutoSize

    $ram = Get-WmiObject Win32_OperatingSystem
    $libreGB = [math]::Round($ram.FreePhysicalMemory / 1MB, 2)
    Write-Host "RAM libre: $libreGB GB" -ForegroundColor Green

    if ($i -lt 3) { Start-Sleep -Seconds 5 }
}
Write-Host "`nMonitorización finalizada."
```

---

## 📚 Resumen de Comandos Rápidos

### CMD

| Comando | Acción |
|---------|--------|
| `cd <ruta>` | Cambiar directorio |
| `dir` | Listar archivos |
| `mkdir <nombre>` | Crear carpeta |
| `rmdir /s /q <nombre>` | Eliminar carpeta |
| `copy <origen> <destino>` | Copiar archivo |
| `move <origen> <destino>` | Mover/renombrar |
| `del <archivo>` | Eliminar archivo |
| `type <archivo>` | Mostrar contenido |
| `cls` | Limpiar pantalla |
| `ipconfig` | Ver configuración de red |
| `ping <host>` | Comprobar conectividad |
| `tasklist` | Ver procesos |
| `taskkill /im <proceso> /f` | Terminar proceso |
| `systeminfo` | Info del sistema |
| `help` | Ayuda |

### PowerShell

| Cmdlet | Acción |
|--------|--------|
| `Get-ChildItem` (`ls`) | Listar archivos |
| `Set-Location` (`cd`) | Cambiar directorio |
| `New-Item` | Crear archivo/carpeta |
| `Copy-Item` | Copiar |
| `Move-Item` | Mover |
| `Remove-Item` | Eliminar |
| `Get-Content` (`cat`) | Ver contenido |
| `Select-String` | Buscar texto |
| `Get-Process` | Ver procesos |
| `Stop-Process` | Terminar proceso |
| `Get-Service` | Ver servicios |
| `Test-Connection` | Ping |
| `Invoke-WebRequest` | Descargar de Internet |
| `Get-Date` | Fecha y hora |

---

## 🔗 Recursos Adicionales

- [Documentación oficial de CMD (Microsoft)](https://docs.microsoft.com/es-es/windows-server/administration/windows-commands/windows-commands)
- [Documentación oficial de PowerShell](https://docs.microsoft.com/es-es/powershell/)
- [SS64 — Referencia rápida de comandos](https://ss64.com/nt/)
- [PowerShell Gallery — módulos y scripts](https://www.powershellgallery.com/)

---

> ✅ **¡Enhorabuena!** Has completado el tutorial de Windows, CMD y PowerShell. Practica los ejercicios y poco a poco dominarás la línea de comandos de Windows.
