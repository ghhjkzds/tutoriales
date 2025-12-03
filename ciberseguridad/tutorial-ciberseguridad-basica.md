# 🔐 Tutorial de Ciberseguridad Básica

Este tutorial cubre los conceptos fundamentales de ciberseguridad que todo usuario y estudiante de informática debe conocer para proteger sus datos, dispositivos y privacidad en el mundo digital.

---

## 📋 Tabla de Contenidos

1. [Introducción a la Ciberseguridad](#introducción-a-la-ciberseguridad)
2. [Amenazas Comunes](#amenazas-comunes)
3. [Contraseñas Seguras](#contraseñas-seguras)
4. [Navegación Segura en Internet](#navegación-segura-en-internet)
5. [Protección de Dispositivos](#protección-de-dispositivos)
6. [Seguridad en Redes WiFi](#seguridad-en-redes-wifi)
7. [Redes Sociales y Privacidad](#redes-sociales-y-privacidad)
8. [Correo Electrónico Seguro](#correo-electrónico-seguro)
9. [Copias de Seguridad](#copias-de-seguridad)
10. [Respuesta ante Incidentes](#respuesta-ante-incidentes)
11. [Ejercicios Prácticos](#ejercicios-prácticos)

---

## Introducción a la Ciberseguridad

### ¿Qué es la Ciberseguridad?

La ciberseguridad es el conjunto de prácticas, tecnologías y procesos diseñados para proteger sistemas, redes, programas y datos de ataques digitales, daños o accesos no autorizados.

```
┌─────────────────────────────────────────────────────────────┐
│                     CIBERSEGURIDAD                           │
│                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │   PERSONAS  │  │  PROCESOS   │  │ TECNOLOGÍA  │          │
│  │             │  │             │  │             │          │
│  │ • Formación │  │ • Políticas │  │ • Antivirus │          │
│  │ • Conciencia│  │ • Protocolos│  │ • Firewalls │          │
│  │ • Buenas    │  │ • Auditorías│  │ • Cifrado   │          │
│  │   prácticas │  │             │  │ • VPN       │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└─────────────────────────────────────────────────────────────┘
```

### La Triada CIA

Los tres pilares fundamentales de la seguridad de la información:

| Principio | Descripción | Ejemplo Práctico |
|-----------|-------------|------------------|
| **Confidencialidad** | Solo personas autorizadas acceden a la información | Usar contraseñas fuertes y cifrado |
| **Integridad** | Los datos no se modifican sin autorización | Verificar descargas con checksums |
| **Disponibilidad** | La información está accesible cuando se necesita | Hacer copias de seguridad |

### ¿Por qué es importante?

```
Estadísticas de ciberseguridad (datos aproximados):

📊 El 95% de las brechas de seguridad son causadas por error humano
📧 El 91% de los ciberataques comienzan con un email de phishing
💰 El costo promedio de una brecha de datos supera los $4 millones
⏰ Un ataque de ransomware ocurre cada 11 segundos
🔓 El 80% de las brechas involucran contraseñas débiles o robadas
```

---

## Amenazas Comunes

### Tipos de Malware

El malware (software malicioso) incluye diversos tipos de programas dañinos:

```
┌─────────────────────────────────────────────────────────────┐
│                    TIPOS DE MALWARE                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  🦠 VIRUS          → Se adjunta a archivos legítimos        │
│                      Se propaga cuando ejecutas el archivo  │
│                                                              │
│  🐛 GUSANO         → Se replica automáticamente             │
│                      Se propaga por la red sin intervención │
│                                                              │
│  🐴 TROYANO        → Se disfraza de software legítimo       │
│                      Abre puertas traseras en tu sistema    │
│                                                              │
│  🔒 RANSOMWARE     → Cifra tus archivos                     │
│                      Exige pago para recuperarlos           │
│                                                              │
│  👁️ SPYWARE        → Espía tu actividad                     │
│                      Roba información personal              │
│                                                              │
│  🔑 KEYLOGGER      → Registra las teclas que pulsas         │
│                      Captura contraseñas y datos            │
│                                                              │
│  📢 ADWARE         → Muestra publicidad no deseada          │
│                      Puede instalar otros programas         │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Ingeniería Social

La ingeniería social manipula a las personas para que revelen información confidencial o realicen acciones peligrosas.

#### Phishing

El phishing es un intento de obtener información sensible haciéndose pasar por una entidad de confianza.

**Señales de un email de phishing:**

```
❌ Remitente sospechoso: soporte@banco-segur0.xyz
❌ Urgencia extrema: "¡Tu cuenta será bloqueada en 24 horas!"
❌ Enlaces extraños: haz clic aquí → banco.com.atacante.ru
❌ Errores ortográficos y gramaticales
❌ Solicitud de información personal o contraseñas
❌ Archivos adjuntos inesperados (.exe, .zip, .doc con macros)
```

**Ejemplo de email legítimo vs phishing:**

```
✅ LEGÍTIMO                          ❌ PHISHING
───────────────────────────────────────────────────────────────
De: soporte@tubanco.com              De: soporte@tubanco-online.xyz
                                     
Estimado cliente,                    URGENTE!!!
                                     
Le informamos que su                 Tu cuenta sera bloqueada
extracto mensual está                sino verificas tus datos
disponible en nuestra                inmediatamente!!!
banca online.                        
                                     Haz clic aqui para verificar:
Acceda a www.tubanco.com             www.tubanco.verificar-ahora.ru
                                     
Atentamente,                         Introduce tu usuario, contraseña
Servicio al Cliente                  y PIN de tarjeta.
```

#### Otros tipos de ingeniería social

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Vishing** | Phishing por teléfono | "Llamo del banco, necesito verificar sus datos" |
| **Smishing** | Phishing por SMS | "Has ganado un premio, haz clic aquí" |
| **Pretexting** | Crear una historia falsa | "Soy de soporte técnico, necesito acceso remoto" |
| **Baiting** | Dejar USB infectados | Pendrive abandonado en un estacionamiento |
| **Tailgating** | Seguir a alguien autorizado | Entrar a un edificio detrás de un empleado |

### Ataques de Fuerza Bruta

Un ataque que prueba todas las combinaciones posibles de contraseñas.

```
Tiempo para descifrar contraseñas (aproximado):

┌──────────────────────────────────────────────────────────────┐
│ Longitud  │ Solo números │ + Minúsculas │ + Mayús + Símbolos │
├───────────┼──────────────┼──────────────┼────────────────────┤
│ 4 caract. │ Instantáneo  │ Instantáneo  │ Instantáneo        │
│ 6 caract. │ Instantáneo  │ Segundos     │ Minutos            │
│ 8 caract. │ Segundos     │ Horas        │ Semanas            │
│ 10 caract.│ Horas        │ Meses        │ Décadas            │
│ 12 caract.│ Meses        │ Siglos       │ Milenios           │
└──────────────────────────────────────────────────────────────┘

💡 Conclusión: Usa contraseñas de al menos 12 caracteres
               con números, mayúsculas, minúsculas y símbolos
```

---

## Contraseñas Seguras

### Características de una Contraseña Fuerte

```
✅ BUENA CONTRASEÑA                  ❌ MALA CONTRASEÑA
───────────────────────────────────────────────────────────────
M1P3rr0#Lad@2024                     123456
                                     password
• 16 caracteres                      contraseña
• Mayúsculas y minúsculas            qwerty
• Números                            fecha de nacimiento
• Símbolos especiales                nombre de mascota
• No es palabra del diccionario      admin123
• No contiene datos personales       iloveyou
```

### Cómo Crear Contraseñas Memorables

**Método de la frase:**

```
1. Elige una frase memorable:
   "Mi perro Toby cumplió 5 años en Marzo del 2023"

2. Toma las primeras letras:
   MpTc5aeMd2023

3. Añade símbolos:
   MpTc5@eM#2023!

4. Resultado: Contraseña fuerte de 14 caracteres
```

**Método de las palabras aleatorias:**

```
1. Elige 4-5 palabras aleatorias:
   caballo - batería - grapa - correcto

2. Únalas con números y símbolos:
   Caballo4Bateria#Grapa!Correcto

3. Resultado: Frase de paso segura y memorable
```

### Gestores de Contraseñas

Los gestores de contraseñas almacenan de forma segura todas tus contraseñas.

```
┌─────────────────────────────────────────────────────────────┐
│               GESTORES DE CONTRASEÑAS                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  🔐 GRATUITOS           🔐 DE PAGO                          │
│                                                              │
│  • Bitwarden            • 1Password                         │
│  • KeePassXC            • Dashlane                          │
│  • KeePass              • LastPass Premium                  │
│                                                              │
│  ✅ Ventajas:                                               │
│  • Solo recuerdas 1 contraseña maestra                      │
│  • Genera contraseñas aleatorias fuertes                    │
│  • Sincroniza entre dispositivos                            │
│  • Detecta contraseñas comprometidas                        │
│  • Rellena formularios automáticamente                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Autenticación de Dos Factores (2FA)

La autenticación de dos factores añade una capa extra de seguridad.

```
┌─────────────────────────────────────────────────────────────┐
│            FACTORES DE AUTENTICACIÓN                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1️⃣ ALGO QUE SABES       → Contraseña, PIN                  │
│                                                              │
│  2️⃣ ALGO QUE TIENES      → Teléfono, token físico           │
│                                                              │
│  3️⃣ ALGO QUE ERES        → Huella dactilar, rostro          │
│                                                              │
└─────────────────────────────────────────────────────────────┘

Tipos de 2FA (de más a menos seguro):

�� Llaves de seguridad físicas (YubiKey, Google Titan)
🥈 Apps de autenticación (Google Authenticator, Authy, Microsoft Authenticator)
🥉 SMS (mejor que nada, pero vulnerable a SIM swapping)
```

**Configurar 2FA en servicios populares:**

| Servicio | Cómo activar 2FA |
|----------|------------------|
| **Google** | Cuenta → Seguridad → Verificación en dos pasos |
| **Microsoft** | Cuenta → Seguridad → Opciones de seguridad adicionales |
| **Facebook** | Configuración → Seguridad e inicio de sesión → Autenticación en dos pasos |
| **Twitter/X** | Configuración → Seguridad → Autenticación en dos fases |
| **Instagram** | Configuración → Seguridad → Autenticación en dos pasos |
| **GitHub** | Settings → Password and authentication → Two-factor authentication |

---

## Navegación Segura en Internet

### Identificar Sitios Web Seguros

```
✅ SITIO SEGURO                      ❌ SITIO SOSPECHOSO
───────────────────────────────────────────────────────────────
🔒 https://www.banco.com              ⚠️ http://banco.ofertas.xyz

• Candado verde/cerrado              • Sin candado o con advertencia
• HTTPS (S = Secure)                 • HTTP sin S
• Dominio oficial conocido           • Dominio extraño o con errores
• Certificado válido                 • Certificado inválido o expirado
```

### Verificar la URL

```
Anatomía de una URL:

https://www.ejemplo.com/pagina?parametro=valor
  │       │      │       │            │
  │       │      │       │            └─ Parámetros
  │       │      │       └─ Ruta de la página
  │       │      └─ Dominio (lo más importante)
  │       └─ Subdominio
  └─ Protocolo (debe ser HTTPS)

⚠️ Cuidado con URLs engañosas:
───────────────────────────────────────────────────────────────
❌ www.banco-seguro.com.atacante.xyz  → El dominio real es atacante.xyz
❌ www.bаnco.com (con 'а' cirílico)   → Parece banco.com pero no lo es
❌ www.banc0.com (con cero)           → Dominio similar fraudulento
❌ www.banco.com.verificar.ru         → El dominio real es verificar.ru
```

### Navegadores y Extensiones de Seguridad

**Configuración básica de seguridad en navegadores:**

```
┌─────────────────────────────────────────────────────────────┐
│         CONFIGURACIÓN RECOMENDADA DEL NAVEGADOR             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ✅ Habilitar protección contra phishing y malware          │
│  ✅ Bloquear pop-ups y redirecciones                        │
│  ✅ Usar DNS seguro (DNS over HTTPS)                        │
│  ✅ Borrar cookies de terceros                              │
│  ✅ Habilitar "No rastrear"                                 │
│  ✅ Actualizar automáticamente                              │
│                                                              │
│  ❌ Evitar guardar contraseñas en el navegador              │
│     (usar un gestor de contraseñas en su lugar)             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Extensiones recomendadas:**

| Extensión | Función |
|-----------|---------|
| **uBlock Origin** | Bloquea publicidad y rastreadores |
| **HTTPS Everywhere** | Fuerza conexiones HTTPS |
| **Privacy Badger** | Bloquea rastreadores de terceros |
| **Bitwarden** | Gestor de contraseñas |

### Uso de VPN

Una VPN (Red Privada Virtual) cifra tu conexión a internet.

```
Sin VPN:
Tu dispositivo → ISP puede ver todo → Internet
                      ↓
              Sitios ven tu IP real

Con VPN:
Tu dispositivo → Conexión cifrada → Servidor VPN → Internet
                      ↓                    ↓
              ISP solo ve              Sitios ven
              tráfico cifrado          IP del VPN
```

**Cuándo usar VPN:**

- ✅ En redes WiFi públicas (cafeterías, aeropuertos, hoteles)
- ✅ Para proteger tu privacidad del ISP
- ✅ Para acceder a contenido geobloqueado (cuando es legal)
- ✅ Al manejar información sensible fuera de casa/oficina

**VPNs recomendadas:**

| VPN | Características |
|-----|-----------------|
| **ProtonVPN** | Versión gratuita disponible, enfocado en privacidad |
| **Mullvad** | No requiere email, acepta efectivo |
| **NordVPN** | Fácil de usar, muchos servidores |
| **ExpressVPN** | Rápido, buen soporte |

---

## Protección de Dispositivos

### Seguridad en Windows

```bash
# Verificar estado del antivirus (PowerShell como administrador)
Get-MpComputerStatus | Select-Object AntivirusEnabled, RealTimeProtectionEnabled

# Actualizar Windows Defender
Update-MpSignature

# Escaneo rápido
Start-MpScan -ScanType QuickScan

# Escaneo completo
Start-MpScan -ScanType FullScan
```

**Configuración esencial de Windows:**

```
┌─────────────────────────────────────────────────────────────┐
│           CONFIGURACIÓN DE SEGURIDAD EN WINDOWS             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Windows Update                                           │
│     Configuración → Actualización y seguridad → Automático  │
│                                                              │
│  2. Windows Defender                                         │
│     Seguridad de Windows → Protección antivirus → Activar   │
│                                                              │
│  3. Firewall                                                 │
│     Seguridad de Windows → Firewall → Activar en todas      │
│     las redes                                                │
│                                                              │
│  4. Control de cuentas de usuario (UAC)                      │
│     Panel de control → Cuentas → Cambiar configuración UAC  │
│     → Nivel alto                                             │
│                                                              │
│  5. BitLocker (si disponible)                                │
│     Panel de control → Sistema y seguridad → BitLocker      │
│     → Activar cifrado de disco                              │
│                                                              │
│  6. Deshabilitar ejecución automática                        │
│     gpedit.msc → Configuración de equipo →                  │
│     Plantillas administrativas → Componentes de Windows →   │
│     Directivas de reproducción automática → Desactivar     │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Seguridad en Linux

```bash
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Configurar firewall básico (UFW)
sudo apt install ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo ufw status

# Verificar puertos abiertos
sudo ss -tuln

# Instalar y ejecutar ClamAV (antivirus)
sudo apt install clamav clamav-daemon
sudo freshclam
sudo clamscan -r /home

# Verificar usuarios del sistema
cat /etc/passwd | grep -v nologin | grep -v false

# Ver intentos de login fallidos
sudo grep "Failed password" /var/log/auth.log | tail -20
```

### Seguridad en macOS

```bash
# Verificar estado del firewall
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate

# Activar firewall
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on

# Activar modo sigiloso (no responde a pings)
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setstealthmode on

# Verificar FileVault (cifrado de disco)
fdesetup status

# Activar FileVault
sudo fdesetup enable

# Verificar si hay aplicaciones con acceso total al disco
sqlite3 /Library/Application\ Support/com.apple.TCC/TCC.db \
  "SELECT client FROM access WHERE service='kTCCServiceSystemPolicyAllFiles'"
```

### Seguridad en Dispositivos Móviles

```
┌─────────────────────────────────────────────────────────────┐
│        SEGURIDAD EN SMARTPHONES (iOS y Android)             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ✅ HACER:                                                   │
│  • Usar bloqueo con PIN/huella/Face ID                      │
│  • Mantener el sistema operativo actualizado                │
│  • Instalar apps solo de tiendas oficiales                  │
│  • Revisar permisos de las aplicaciones                     │
│  • Activar "Buscar mi dispositivo"                          │
│  • Activar cifrado del dispositivo                          │
│  • Hacer copias de seguridad regulares                      │
│                                                              │
│  ❌ EVITAR:                                                  │
│  • Jailbreak o root (elimina protecciones)                  │
│  • Instalar APKs de fuentes desconocidas                    │
│  • Conectar a WiFi públicas sin VPN                         │
│  • Prestar el teléfono desbloqueado                         │
│  • Ignorar actualizaciones de seguridad                     │
│                                                              │
└─────────────────────────────────────────────────────────────┘

Permisos que debes revisar cuidadosamente:
• Cámara y micrófono
• Ubicación
• Contactos
• Almacenamiento
• SMS y llamadas
```

---

## Seguridad en Redes WiFi

### Tipos de Seguridad WiFi

```
┌─────────────────────────────────────────────────────────────┐
│              PROTOCOLOS DE SEGURIDAD WiFi                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ❌ WEP (Wired Equivalent Privacy)                          │
│     → NUNCA usar, se puede romper en minutos                │
│                                                              │
│  ⚠️ WPA (WiFi Protected Access)                             │
│     → Obsoleto, vulnerable a ataques                        │
│                                                              │
│  ⚠️ WPA2-TKIP                                               │
│     → Mejor evitar, usar AES en su lugar                    │
│                                                              │
│  ✅ WPA2-AES (también llamado WPA2-CCMP)                    │
│     → Bueno, el más común actualmente                       │
│                                                              │
│  ✅ WPA3                                                     │
│     → El mejor, usar si está disponible                     │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Configurar Router de Forma Segura

```
Lista de verificación para tu router:

□ Cambiar contraseña de administrador por defecto
  (admin/admin o admin/password son las más comunes)

□ Cambiar nombre de red (SSID)
  (No revelar modelo de router ni datos personales)

□ Usar WPA2-AES o WPA3

□ Crear contraseña WiFi fuerte (12+ caracteres)

□ Actualizar firmware del router

□ Deshabilitar WPS (WiFi Protected Setup)
  (Vulnerable a ataques de fuerza bruta)

□ Deshabilitar administración remota

□ Cambiar puerto de administración por defecto

□ Crear red de invitados separada

□ Considerar ocultar SSID (seguridad menor)
```

### Peligros del WiFi Público

```
⚠️ RIESGOS EN REDES WiFi PÚBLICAS ⚠️

1. ATAQUES MAN-IN-THE-MIDDLE
   El atacante intercepta comunicación entre tú y el servidor
   ┌────────┐         ┌──────────┐         ┌──────────┐
   │   Tú   │ ← ── →  │ Atacante │ ← ── →  │ Servidor │
   └────────┘         └──────────┘         └──────────┘

2. EVIL TWIN (Gemelo Malvado)
   Red falsa con nombre similar a la legítima
   ✅ "Starbucks_WiFi" (real)
   ❌ "Starbucks_WiFi_Gratis" (falsa)

3. PACKET SNIFFING
   Captura de datos no cifrados que viajan por la red

4. SESSION HIJACKING
   Robo de sesiones activas (cookies)
```

**Protección en WiFi públicas:**

```
✅ SIEMPRE en WiFi público:
───────────────────────────────────────────────────────────────
• Usar VPN
• Verificar que los sitios usen HTTPS
• No acceder a banca online o información sensible
• Desactivar conexión automática a redes
• Desactivar compartir archivos
• Usar datos móviles para operaciones sensibles

Configuración recomendada:
• Olvidar la red cuando termines de usarla
• Usar "Red pública" en Windows (más restrictivo)
```

---

## Redes Sociales y Privacidad

### Configuración de Privacidad

```
┌─────────────────────────────────────────────────────────────┐
│        CONFIGURACIÓN DE PRIVACIDAD RECOMENDADA              │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  FACEBOOK                                                    │
│  Configuración → Privacidad                                  │
│  • Quién puede ver publicaciones → Solo amigos              │
│  • Quién puede enviarte solicitudes → Amigos de amigos     │
│  • Indexación en motores de búsqueda → Desactivar          │
│  • Reconocimiento facial → Desactivar                       │
│                                                              │
│  INSTAGRAM                                                   │
│  Configuración → Privacidad                                  │
│  • Cuenta privada → Activar                                  │
│  • Estado de actividad → Desactivar                         │
│  • Compartir historia → Solo amigos cercanos                │
│                                                              │
│  TWITTER/X                                                   │
│  Configuración → Privacidad y seguridad                     │
│  • Proteger tus tweets → Activar (si deseas)                │
│  • Ubicación en tweets → Desactivar                         │
│  • Descubrimiento → Limitar                                 │
│                                                              │
│  LINKEDIN                                                    │
│  Configuración → Visibilidad                                 │
│  • Modo privado de navegación → Activar                     │
│  • Visibilidad de email → Solo conexiones                   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Información que NO Debes Compartir

```
❌ INFORMACIÓN PELIGROSA EN REDES SOCIALES:

📍 Ubicación en tiempo real
   "Cenando en restaurante X" → Ladrones saben que no estás en casa

📸 Fotos de documentos
   DNI, pasaporte, tarjetas → Robo de identidad

🏠 Dirección de casa
   En fotos, check-ins → Acoso, robos

📅 Planes de viaje
   "Me voy 2 semanas a..." → Casa vacía

👨‍👩‍👧 Información de menores
   Fotos, nombres, escuelas → Depredadores

💳 Información financiera
   Tarjetas, cheques, extractos → Fraude

🔑 Preguntas de seguridad
   Nombre de mascota, calle donde creciste → Hackeo de cuentas
```

### Huella Digital

```
Tu huella digital está compuesta por:

┌─────────────────────────────────────────────────────────────┐
│                    TU HUELLA DIGITAL                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ACTIVA (lo que compartes intencionalmente)                 │
│  • Publicaciones en redes sociales                          │
│  • Comentarios y reseñas                                    │
│  • Fotos y videos que subes                                 │
│  • Perfiles públicos                                        │
│                                                              │
│  PASIVA (lo que otros recopilan sobre ti)                   │
│  • Historial de navegación                                  │
│  • Compras online                                           │
│  • Ubicaciones visitadas                                    │
│  • Cookies y rastreadores                                   │
│  • Metadatos de archivos                                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘

🔍 Para ver qué saben de ti:
• Google: myactivity.google.com
• Facebook: Configuración → Tu información de Facebook
• Twitter: Configuración → Datos de tu cuenta
```

---

## Correo Electrónico Seguro

### Identificar Emails Maliciosos

```
ANÁLISIS DE UN EMAIL SOSPECHOSO:

┌─────────────────────────────────────────────────────────────┐
│ De: servicio@bancco-seguro.xyz  ← ❌ Dominio falso          │
│ Para: usuario@email.com                                      │
│ Asunto: ⚠️ URGENTE: Su cuenta será suspendida ← ❌ Urgencia │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│ Estimado cliente,                                            │
│                                                              │
│ Hemos detectado actividad sospechosa en su cuenta.          │
│ Para evitar la suspención de su cuenta, haga clic en el     │
│                      ↑                                       │
│                ❌ Error ortográfico                          │
│                                                              │
│ siguiente enlace dentro de las proximas 24 horas:           │
│                                                              │
│ [Verificar mi cuenta] ← ❌ Al pasar el ratón:               │
│                         http://verificar.bancco.xyz/login   │
│                                                              │
│ Si no verifica su cuenta, será bloqueada permanentemente.   │
│                      ↑                                       │
│                ❌ Amenazas                                   │
│                                                              │
│ Atentamente,                                                 │
│ Equipo de Seguridad                                          │
│ ← ❌ Sin firma profesional ni datos de contacto             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Verificar Remitentes

```bash
# Ver cabeceras completas del email
# En Gmail: Más → Mostrar original
# En Outlook: Archivo → Propiedades → Encabezados de Internet

Campos importantes a verificar:

From: nombre-visible@dominio.com     ← Puede ser falsificado
Return-Path: real@otrodominio.xyz    ← Revela origen real
Received: from servidor.xyz          ← Ruta del email
SPF: pass/fail                        ← Verificación del dominio
DKIM: pass/fail                       ← Firma digital válida
DMARC: pass/fail                      ← Política de autenticación

Si SPF, DKIM o DMARC fallan → Email probablemente fraudulento
```

### Proteger tu Email

```
┌─────────────────────────────────────────────────────────────┐
│              BUENAS PRÁCTICAS DE EMAIL                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ✅ HACER:                                                   │
│  • Usar 2FA en tu cuenta de correo                          │
│  • Crear alias para registros online                        │
│  • Usar email temporal para sitios no importantes           │
│  • Verificar adjuntos antes de abrirlos                     │
│  • Reportar phishing (no solo eliminarlo)                   │
│                                                              │
│  ❌ EVITAR:                                                  │
│  • Hacer clic en enlaces de emails no solicitados           │
│  • Descargar adjuntos de remitentes desconocidos            │
│  • Responder a emails sospechosos                           │
│  • Usar email del trabajo para cuentas personales           │
│  • Enviar información sensible por email sin cifrar         │
│                                                              │
│  🔒 HERRAMIENTAS ADICIONALES:                               │
│  • ProtonMail: Email cifrado de extremo a extremo           │
│  • SimpleLogin: Alias de email                               │
│  • Have I Been Pwned: Verificar si tu email fue filtrado    │
│    (haveibeenpwned.com)                                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Copias de Seguridad

### La Regla 3-2-1

```
┌─────────────────────────────────────────────────────────────┐
│                    REGLA 3-2-1 DE BACKUPS                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│     3            2                    1                      │
│   Copias     Tipos de             Copia                     │
│  de datos    medios              offsite                    │
│                                                              │
│   ┌─────┐    ┌─────────────┐    ┌──────────────────┐       │
│   │  1  │    │ Disco duro  │    │    En la nube    │       │
│   │  2  │    │  interno    │    │        o         │       │
│   │  3  │    └─────────────┘    │  otra ubicación  │       │
│   └─────┘    ┌─────────────┐    │     física       │       │
│              │ Disco duro  │    └──────────────────┘       │
│              │  externo    │                                │
│              └─────────────┘                                │
│                                                              │
│  Ejemplo práctico:                                          │
│  1. Datos en tu ordenador (original)                        │
│  2. Copia en disco externo (en casa)                        │
│  3. Copia en la nube o disco en casa de familiar           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Qué Datos Respaldar

```
PRIORIDAD ALTA (respaldar frecuentemente):
├── Documentos importantes
│   ├── Documentos de trabajo/estudios
│   ├── Documentos legales (escaneados)
│   └── Documentos financieros
├── Fotos y videos familiares
├── Configuraciones y contraseñas
│   └── Exportación del gestor de contraseñas
└── Proyectos de código/trabajo

PRIORIDAD MEDIA (respaldar periódicamente):
├── Música y películas (si no están en streaming)
├── Juegos guardados
└── Configuraciones de aplicaciones

NO NECESITA BACKUP:
├── Sistema operativo (se puede reinstalar)
├── Programas (se pueden descargar)
└── Archivos temporales
```

### Herramientas de Backup

```bash
# Windows - Historial de archivos
# Configuración → Actualización y seguridad → Copia de seguridad

# macOS - Time Machine
# Preferencias del Sistema → Time Machine → Seleccionar disco

# Linux - rsync (ejemplo básico)
rsync -avz --delete /home/usuario/Documentos/ /media/backup/

# Ejemplo con exclusiones
rsync -avz --delete \
  --exclude='.cache' \
  --exclude='node_modules' \
  --exclude='*.tmp' \
  /home/usuario/ /media/backup/

# Backup automatizado con cron
# Editar: crontab -e
# Ejecutar cada día a las 2 AM:
0 2 * * * rsync -avz /home/usuario/Documentos/ /media/backup/
```

### Servicios de Backup en la Nube

| Servicio | Almacenamiento Gratuito | Características |
|----------|------------------------|-----------------|
| **Google Drive** | 15 GB | Integrado con Google |
| **OneDrive** | 5 GB | Integrado con Windows |
| **iCloud** | 5 GB | Integrado con Apple |
| **Dropbox** | 2 GB | Fácil sincronización |
| **Backblaze** | - | $7/mes, ilimitado |
| **pCloud** | 10 GB | Cifrado opcional |

### Verificar Backups

```
⚠️ IMPORTANTE: Un backup que no funciona es peor que no tener backup

Lista de verificación trimestral:
□ Verificar que el backup está actualizado
□ Intentar restaurar algunos archivos de prueba
□ Comprobar integridad de los datos
□ Verificar espacio disponible
□ Revisar que no hay errores en los logs
```

---

## Respuesta ante Incidentes

### Qué Hacer si te Hackean

```
┌─────────────────────────────────────────────────────────────┐
│           PASOS INMEDIATOS SI TE HACKEAN                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1️⃣ DESCONECTAR                                             │
│     • Desconecta el dispositivo de Internet                 │
│     • Apaga WiFi y datos móviles                            │
│                                                              │
│  2️⃣ CAMBIAR CONTRASEÑAS (desde otro dispositivo seguro)    │
│     • Email principal primero                               │
│     • Banco y servicios financieros                         │
│     • Redes sociales                                        │
│     • Cualquier servicio con la misma contraseña           │
│                                                              │
│  3️⃣ ACTIVAR 2FA                                             │
│     • En todos los servicios que lo permitan                │
│                                                              │
│  4️⃣ REVISAR ACTIVIDAD                                       │
│     • Revisar últimos accesos en cada servicio             │
│     • Buscar emails de "nuevo inicio de sesión"            │
│     • Verificar transacciones bancarias                     │
│                                                              │
│  5️⃣ ESCANEAR DISPOSITIVOS                                   │
│     • Ejecutar antivirus completo                           │
│     • Considerar formateo si es grave                       │
│                                                              │
│  6️⃣ NOTIFICAR                                               │
│     • Al banco si hay movimientos sospechosos               │
│     • A la policía si hay robo de dinero/identidad         │
│     • A contactos si pueden recibir mensajes falsos        │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Señales de que Fuiste Hackeado

```
⚠️ SEÑALES DE ALERTA:

CUENTA:
• Emails de "inicio de sesión" que no reconoces
• Contraseña ya no funciona
• Publicaciones/mensajes que no hiciste
• Notificaciones de cambio de contraseña
• Amigos reportan mensajes extraños tuyos

DISPOSITIVO:
• Lentitud inusual
• Pop-ups y publicidad excesiva
• Programas que no instalaste
• El ratón se mueve solo
• Archivos desaparecen o se cifran
• Antivirus desactivado

FINANCIERO:
• Transacciones que no reconoces
• Nuevas tarjetas o préstamos a tu nombre
• Facturas de servicios que no contrataste
```

### Ransomware: Qué Hacer

```
🔒 SI TUS ARCHIVOS FUERON CIFRADOS POR RANSOMWARE:

1. NO PAGUES EL RESCATE
   • No garantiza recuperar los archivos
   • Financia más ataques
   • Pueden pedirte más dinero después

2. DESCONECTA INMEDIATAMENTE
   • Evita que se propague a otros dispositivos

3. IDENTIFICA EL RANSOMWARE
   • Visita: nomoreransom.org
   • Puede haber herramienta de descifrado gratuita

4. REPORTA EL INCIDENTE
   • A la policía
   • A la Agencia de Ciberseguridad de tu país

5. RESTAURA DESDE BACKUP
   • Si tienes backups limpios (no infectados)

6. LIMPIA Y REINSTALA
   • Formatea el disco
   • Reinstala sistema operativo limpio
```

### Contactos de Emergencia

```
┌─────────────────────────────────────────────────────────────┐
│         RECURSOS DE AYUDA EN CIBERSEGURIDAD                 │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  🌍 INTERNACIONALES:                                         │
│  • No More Ransom: nomoreransom.org                         │
│  • Have I Been Pwned: haveibeenpwned.com                    │
│  • VirusTotal: virustotal.com                               │
│                                                              │
│  🇪🇸 ESPAÑA:                                                 │
│  • INCIBE: incibe.es | 017 (teléfono gratuito)             │
│  • Policía Nacional: policia.es                             │
│  • Guardia Civil: gdt.guardiacivil.es                       │
│                                                              │
│  🇲🇽 MÉXICO:                                                 │
│  • Policía Cibernética: policia.cdmx.gob.mx                │
│  • CERT-MX: cert-mx.org.mx                                  │
│                                                              │
│  🇦🇷 ARGENTINA:                                              │
│  • CERT.ar: cert.ar                                         │
│  • División Delitos Tecnológicos                            │
│                                                              │
│  🇨🇴 COLOMBIA:                                               │
│  • CAI Virtual: caivirtual.policia.gov.co                   │
│  • ColCERT: colcert.gov.co                                  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Ejercicios Prácticos

### Ejercicio 1: Auditoría de Contraseñas

```
OBJETIVO: Evaluar y mejorar la seguridad de tus contraseñas

1. Verifica si tus emails han sido filtrados:
   → Visita: haveibeenpwned.com
   → Ingresa cada uno de tus emails

2. Si apareces en una filtración:
   → Cambia la contraseña de ese servicio
   → Cambia cualquier cuenta donde usaste la misma contraseña

3. Instala un gestor de contraseñas:
   → Bitwarden (gratuito y de código abierto)
   → Importa contraseñas del navegador
   → Genera nuevas contraseñas únicas para cada servicio

4. Activa 2FA en estas cuentas (por orden de prioridad):
   □ Email principal
   □ Banco
   □ Redes sociales
   □ Servicios de trabajo
```

### Ejercicio 2: Reconocer Phishing

```
OBJETIVO: Mejorar tu capacidad de detectar emails fraudulentos

1. Practica en simuladores gratuitos:
   → Google Phishing Quiz: phishingquiz.withgoogle.com
   → Phish.io: phish.io

2. Analiza estos ejemplos (¿legítimo o phishing?):

   a) Email de "Amazon" con enlace:
      http://amazon.com.verificar-pedido.xyz/login
      → _______________________________

   b) Email de tu banco con remitente:
      alertas@tubanco.com
      Enlace: https://www.tubanco.com/acceso
      → _______________________________

   c) Email urgente de "Microsoft":
      De: security@microsoft-support.xyz
      "Tu cuenta será eliminada en 24h"
      → _______________________________

   RESPUESTAS:
   a) Phishing - El dominio real es verificar-pedido.xyz
   b) Posiblemente legítimo - Verificar cabeceras completas
   c) Phishing - Dominio falso y urgencia artificial
```

### Ejercicio 3: Configurar tu Router

```
OBJETIVO: Asegurar tu red doméstica

□ Paso 1: Accede al router
  → Generalmente: 192.168.1.1 o 192.168.0.1
  → Busca la IP en: ipconfig (Windows) o ifconfig (Linux/Mac)

□ Paso 2: Cambia credenciales por defecto
  → Usuario admin y contraseña por defecto = peligro
  → Crea una contraseña fuerte

□ Paso 3: Configura WiFi seguro
  → Cambia nombre de red (no reveles modelo de router)
  → Selecciona WPA2-AES o WPA3
  → Crea contraseña WiFi de 12+ caracteres

□ Paso 4: Seguridad adicional
  → Desactiva WPS
  → Desactiva administración remota
  → Actualiza firmware

□ Paso 5: Crea red de invitados
  → Separada de tu red principal
  → Para visitas y dispositivos IoT
```

### Ejercicio 4: Crear un Plan de Backup

```
OBJETIVO: Implementar la regla 3-2-1

1. Identifica datos críticos:
   □ Documentos importantes
   □ Fotos familiares
   □ Proyectos de trabajo/estudio
   □ Configuraciones y contraseñas

2. Configura backup local:
   → Compra disco externo (tamaño = 2x tus datos)
   → Configura backup automático semanal

3. Configura backup en la nube:
   → Elige servicio (Google Drive, OneDrive, etc.)
   → Configura sincronización automática

4. Programa recordatorio mensual:
   → Verificar que backups funcionan
   → Probar restauración de algunos archivos

5. Documenta el proceso:
   → Crea un documento con:
     • Dónde están los backups
     • Cómo restaurar
     • Contraseñas de cifrado (si las hay)
```

### Ejercicio 5: Simulacro de Incidente

```
OBJETIVO: Practicar respuesta ante un hackeo

ESCENARIO: Recibes un email que dice:
"Detectamos acceso inusual a tu cuenta de Google desde Rusia"

Responde estas preguntas:

1. ¿Cómo verificas si el email es legítimo?
   _____________________________________________

2. ¿Qué acciones inmediatas tomarías?
   _____________________________________________

3. ¿Qué otras cuentas podrían estar en riesgo?
   _____________________________________________

4. ¿A quién notificarías?
   _____________________________________________

SOLUCIÓN SUGERIDA:
1. No hagas clic en enlaces del email. Accede directamente
   a myaccount.google.com y revisa "Actividad de seguridad"

2. - Cambiar contraseña de Google
   - Revisar dispositivos conectados
   - Activar 2FA si no está activo
   - Cerrar sesiones desconocidas

3. - Cualquier cuenta con la misma contraseña
   - Cuentas vinculadas a ese email
   - Servicios con "Iniciar sesión con Google"

4. - Nadie externo si puedes resolverlo
   - Google Support si perdiste acceso
   - Policía si hay robo de dinero/identidad
```

---

## 📚 Recursos Adicionales

### Cursos Gratuitos

| Plataforma | Curso | Enlace |
|------------|-------|--------|
| Google | Fundamentos de Seguridad | grow.google/intl/es |
| Coursera | Ciberseguridad de Google | coursera.org |
| INCIBE | Cursos para ciudadanos | incibe.es/ciudadania |
| Cisco | Introduction to Cybersecurity | netacad.com |

### Herramientas Recomendadas

```
┌─────────────────────────────────────────────────────────────┐
│             HERRAMIENTAS DE SEGURIDAD GRATUITAS             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ANTIVIRUS:                                                  │
│  • Windows Defender (incluido en Windows)                   │
│  • Malwarebytes Free (escaneo bajo demanda)                 │
│  • ClamAV (Linux/Mac)                                       │
│                                                              │
│  GESTORES DE CONTRASEÑAS:                                    │
│  • Bitwarden                                                 │
│  • KeePassXC                                                 │
│                                                              │
│  VPN:                                                        │
│  • ProtonVPN (versión gratuita)                             │
│  • Windscribe (10GB gratis)                                 │
│                                                              │
│  NAVEGACIÓN SEGURA:                                          │
│  • Firefox con extensiones de privacidad                    │
│  • Brave Browser                                             │
│  • Tor Browser (para máxima privacidad)                     │
│                                                              │
│  VERIFICACIÓN:                                               │
│  • VirusTotal (analizar archivos sospechosos)               │
│  • Have I Been Pwned (verificar emails filtrados)           │
│  • SSL Labs (verificar certificados de sitios)              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Podcasts y Canales

- **Darknet Diaries** (Podcast) - Historias de hackers y ciberseguridad
- **Security Now** (Podcast) - Noticias de seguridad semanales
- **El Lado del Mal** (Blog) - Seguridad en español
- **HackerSploit** (YouTube) - Tutoriales de hacking ético
- **NetworkChuck** (YouTube) - Redes y seguridad accesible

### Libros Recomendados

| Libro | Autor | Nivel |
|-------|-------|-------|
| "Hacking: Arte del Exploitation" | Jon Erickson | Intermedio |
| "El Arte de la Intrusión" | Kevin Mitnick | Principiante |
| "The Web Application Hacker's Handbook" | Dafydd Stuttard | Avanzado |
| "Ingeniería Social" | Christopher Hadnagy | Principiante |

---

## 🎯 Resumen y Próximos Pasos

### Lista de Verificación de Seguridad Personal

```
NIVEL BÁSICO (hazlo hoy):
□ Usa contraseñas únicas para cada servicio
□ Activa 2FA en email y banco
□ Actualiza tu sistema operativo
□ Configura un backup básico

NIVEL INTERMEDIO (esta semana):
□ Instala un gestor de contraseñas
□ Revisa permisos de apps móviles
□ Configura tu router de forma segura
□ Verifica tus emails en haveibeenpwned.com

NIVEL AVANZADO (este mes):
□ Implementa la regla 3-2-1 de backups
□ Usa VPN en redes públicas
□ Revisa configuración de privacidad en redes sociales
□ Practica identificar phishing
```

### Mantente Actualizado

La ciberseguridad evoluciona constantemente. Mantente informado sobre nuevas amenazas y mejores prácticas:

- Suscríbete a alertas de INCIBE o tu CERT nacional
- Sigue las actualizaciones de tus proveedores de software
- Revisa periódicamente la configuración de seguridad de tus cuentas

---

**Recuerda**: La seguridad perfecta no existe, pero cada capa de protección que añades hace más difícil que seas víctima de un ataque. ¡Tu seguridad digital está en tus manos!

---

📖 **Tutorial relacionado**: [Tutorial Completo de Seguridad Informática](../docs/seguridad-informatica.md) - Para conceptos más avanzados y técnicos.
