# 🏠 Guía Completa de Hosting y Deploy Casero con tu Propio PC

## Índice

1. [¿Qué es el hosting casero?](#1-qué-es-el-hosting-casero)
2. [Requisitos previos](#2-requisitos-previos)
3. [Conceptos clave de redes](#3-conceptos-clave-de-redes)
4. [Hosting casero en Linux](#4-hosting-casero-en-linux)
   - [Preparar el sistema](#41-preparar-el-sistema)
   - [Instalar Nginx](#42-instalar-nginx)
   - [Configurar un sitio web estático](#43-configurar-un-sitio-web-estático)
   - [Desplegar una aplicación Node.js con PM2](#44-desplegar-una-aplicación-nodejs-con-pm2)
   - [Desplegar con Docker](#45-desplegar-con-docker-en-linux)
   - [Abrir el puerto en el firewall (ufw)](#46-abrir-el-puerto-en-el-firewall-ufw)
5. [Hosting casero en Windows](#5-hosting-casero-en-windows)
   - [Preparar el sistema](#51-preparar-el-sistema)
   - [Instalar IIS (servidor web de Windows)](#52-instalar-iis-servidor-web-de-windows)
   - [Instalar Nginx en Windows](#53-instalar-nginx-en-windows)
   - [Desplegar una aplicación Node.js con pm2](#54-desplegar-una-aplicación-nodejs-con-pm2)
   - [Desplegar con Docker Desktop](#55-desplegar-con-docker-desktop)
   - [Abrir el puerto en el Firewall de Windows](#56-abrir-el-puerto-en-el-firewall-de-windows)
6. [Exponer tu servidor al exterior](#6-exponer-tu-servidor-al-exterior)
   - [IP dinámica y DDNS](#61-ip-dinámica-y-ddns)
   - [Port Forwarding en el router](#62-port-forwarding-en-el-router)
   - [HTTPS con Let's Encrypt (Certbot)](#63-https-con-lets-encrypt-certbot)
   - [Túneles: Cloudflare Tunnel y Ngrok](#64-túneles-cloudflare-tunnel-y-ngrok)
7. [Proyecto práctico: Deploy completo de una API REST](#7-proyecto-práctico-deploy-completo-de-una-api-rest)
8. [Comparación: Linux vs Windows para hosting casero](#8-comparación-linux-vs-windows-para-hosting-casero)
9. [Seguridad básica del servidor](#9-seguridad-básica-del-servidor)
10. [Buenas prácticas](#10-buenas-prácticas)
11. [Solución de problemas comunes](#11-solución-de-problemas-comunes)

---

## 1. ¿Qué es el hosting casero?

El **hosting casero** (también llamado *self-hosting*) consiste en usar **tu propio ordenador o PC** como servidor para alojar y servir aplicaciones web, APIs, sitios estáticos o cualquier servicio accesible desde internet, sin depender de servicios de pago externos.

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                       │
│   🌍 INTERNET                                                         │
│        │                                                              │
│        ▼                                                              │
│   📡 ROUTER (Port Forwarding: 80 / 443)                              │
│        │                                                              │
│        ▼                                                              │
│   🖥️  TU PC (Nginx / IIS / Node.js / Docker)                         │
│        │                                                              │
│        ▼                                                              │
│   📦 Tu aplicación corriendo en localhost                             │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### ¿Cuándo tiene sentido el hosting casero?

| Caso de uso | ¿Recomendado? |
|-------------|--------------|
| Proyectos personales y de aprendizaje | ✅ Ideal |
| Portfolio o CV online | ✅ Buena opción |
| Servidor para uso interno (red local) | ✅ Perfecto |
| Servidor de juegos para amigos | ✅ Muy común |
| Aplicación de producción con millones de usuarios | ❌ No recomendado |
| Alta disponibilidad 24/7 con SLA | ❌ Mejor un VPS |

### Ventajas y desventajas

| Ventajas | Desventajas |
|----------|-------------|
| 💰 Sin coste mensual de hosting | 💡 Gasta electricidad |
| 🔒 Control total sobre los datos | 🌐 IP dinámica (puede cambiar) |
| 📚 Aprendizaje profundo de redes y servidores | ⚡ Sin garantía de uptime |
| ⚙️ Configuración personalizada | 🔧 Tú gestionas la seguridad |
| 🚀 Rendimiento según tu hardware | 📡 Depende de tu banda ancha |

---

## 2. Requisitos previos

### Hardware mínimo recomendado

| Componente | Mínimo | Recomendado |
|------------|--------|-------------|
| CPU | Dual-core 1.5 GHz | Quad-core 2 GHz+ |
| RAM | 2 GB | 4 GB+ |
| Almacenamiento | 20 GB HDD | 50 GB SSD |
| Conexión | 10 Mbps subida | 50 Mbps+ subida |

### Software necesario

- **Sistema operativo**: Ubuntu 22.04 LTS / Debian 12 (Linux) o Windows 10/11 (Windows)
- **Servidor web**: Nginx (recomendado) o Apache / IIS (Windows)
- **Runtime de aplicación**: Node.js, Python, Java, Go, etc.
- **Proceso manager**: PM2 (Node.js), systemd (Linux), NSSM (Windows)
- **Opcionalmente**: Docker

### Conocimientos previos útiles

- Comandos básicos de terminal (Linux o Windows CMD/PowerShell)
- Conceptos básicos de redes (IP, puertos, DNS)
- Algún lenguaje de backend (Node.js, Python, etc.)

---

## 3. Conceptos clave de redes

Antes de comenzar, es fundamental entender estos conceptos:

### IP local vs IP pública

```
Tu red de casa:
  Tu PC         →  IP local:  192.168.1.100
  Router        →  IP local:  192.168.1.1
                →  IP pública: 85.23.14.67  (la que ve internet)
```

- **IP local (privada)**: Solo accesible dentro de tu red doméstica.
- **IP pública**: La dirección con la que tu hogar se identifica en internet.

### ¿Qué es un puerto?

Un puerto es como una "puerta" dentro de tu PC. Las aplicaciones escuchan en puertos específicos:

| Puerto | Protocolo |
|--------|-----------|
| 80 | HTTP |
| 443 | HTTPS |
| 22 | SSH |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 3000 | Node.js (por convención) |
| 8080 | HTTP alternativo |

### ¿Qué es el Port Forwarding?

El *port forwarding* le dice al router: "cuando llegue tráfico al puerto X, reenvíalo al PC con IP local Y".

```
Internet → Router (puerto 80) → Tu PC (192.168.1.100:80)
```

### ¿Qué es un reverse proxy?

Nginx o Apache actúan como intermediarios entre internet y tu aplicación:

```
Internet → Nginx (puerto 80/443) → Tu app (localhost:3000)
```

Ventajas: HTTPS, múltiples apps en un solo PC, caché, compresión.

---

## 4. Hosting casero en Linux

### 4.1 Preparar el sistema

```bash
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Instalar utilidades básicas
sudo apt install -y curl wget git unzip net-tools

# Ver tu IP local
ip a | grep "inet " | grep -v "127.0.0.1"

# Ver tu IP pública
curl ifconfig.me
```

### Configurar una IP local estática (recomendado)

Para que el port forwarding del router siempre apunte al mismo PC, conviene asignar una IP estática local:

```bash
# Editar la configuración de Netplan (Ubuntu 22.04+)
sudo nano /etc/netplan/00-installer-config.yaml
```

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:                        # Cambia por tu interfaz (ip a para verla)
      addresses:
        - 192.168.1.100/24       # IP estática deseada
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
      routes:
        - to: default
          via: 192.168.1.1       # IP de tu router
```

```bash
# Aplicar los cambios
sudo netplan apply
```

---

### 4.2 Instalar Nginx

```bash
# Instalar Nginx
sudo apt install -y nginx

# Iniciar y habilitar al arrancar
sudo systemctl start nginx
sudo systemctl enable nginx

# Verificar estado
sudo systemctl status nginx

# Comprobar que funciona (desde otro terminal o navegador en http://localhost)
curl http://localhost
```

**Estructura de directorios de Nginx:**

```
/etc/nginx/
├── nginx.conf              ← Configuración principal
├── sites-available/        ← Sitios disponibles
│   └── default
└── sites-enabled/          ← Sitios activos (symlinks)
    └── default -> ../sites-available/default

/var/www/html/              ← Raíz web por defecto
/var/log/nginx/
├── access.log              ← Registro de accesos
└── error.log               ← Registro de errores
```

---

### 4.3 Configurar un sitio web estático

```bash
# Crear el directorio del sitio
sudo mkdir -p /var/www/mi-sitio

# Crear una página de prueba
sudo nano /var/www/mi-sitio/index.html
```

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Mi servidor casero</title>
</head>
<body>
  <h1>¡Hola desde mi PC! 🏠</h1>
  <p>Servidor casero funcionando correctamente.</p>
</body>
</html>
```

```bash
# Crear la configuración de Nginx para el sitio
sudo nano /etc/nginx/sites-available/mi-sitio
```

```nginx
server {
    listen 80;
    server_name _;          # Acepta cualquier nombre de dominio o IP

    root /var/www/mi-sitio;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    # Logs específicos del sitio
    access_log /var/log/nginx/mi-sitio-access.log;
    error_log  /var/log/nginx/mi-sitio-error.log;
}
```

```bash
# Activar el sitio (symlink)
sudo ln -s /etc/nginx/sites-available/mi-sitio /etc/nginx/sites-enabled/

# Desactivar el sitio por defecto si quieres
sudo rm /etc/nginx/sites-enabled/default

# Verificar la configuración
sudo nginx -t

# Recargar Nginx
sudo systemctl reload nginx
```

---

### 4.4 Desplegar una aplicación Node.js con PM2

#### Instalar Node.js

```bash
# Instalar Node.js (via NodeSource)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Verificar
node --version
npm --version
```

#### Instalar PM2

```bash
# PM2: gestor de procesos para Node.js
sudo npm install -g pm2
```

#### Crear una aplicación de ejemplo

```bash
mkdir ~/mi-api && cd ~/mi-api
npm init -y
npm install express
nano app.js
```

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json());

app.get('/', (req, res) => {
  res.json({
    mensaje: '¡API funcionando desde mi PC!',
    hora: new Date().toISOString(),
    hostname: require('os').hostname()
  });
});

app.get('/salud', (req, res) => {
  res.json({ estado: 'ok' });
});

app.listen(PORT, () => {
  console.log(`Servidor corriendo en http://localhost:${PORT}`);
});
```

#### Iniciar con PM2

```bash
# Iniciar la aplicación
pm2 start app.js --name mi-api

# Ver estado
pm2 status

# Ver logs en tiempo real
pm2 logs mi-api

# Guardar configuración para que arranque con el sistema
pm2 startup systemd
# (ejecuta el comando que te indique PM2)
pm2 save
```

#### Configurar Nginx como reverse proxy

```bash
sudo nano /etc/nginx/sites-available/mi-api
```

```nginx
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass         http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection 'upgrade';
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/mi-api /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

# Probar
curl http://localhost
```

---

### 4.5 Desplegar con Docker en Linux

```bash
# Instalar Docker
curl -fsSL https://get.docker.com | sudo bash

# Agregar tu usuario al grupo docker (evita usar sudo)
sudo usermod -aG docker $USER
newgrp docker

# Verificar
docker --version
docker run hello-world
```

#### Crear un Dockerfile para la API

```bash
cd ~/mi-api
nano Dockerfile
```

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

```bash
# Construir la imagen
docker build -t mi-api:latest .

# Ejecutar el contenedor
docker run -d \
  --name mi-api \
  --restart unless-stopped \
  -p 3000:3000 \
  mi-api:latest

# Ver contenedores en ejecución
docker ps

# Ver logs
docker logs -f mi-api
```

#### Con Docker Compose (recomendado para múltiples servicios)

```bash
nano docker-compose.yml
```

```yaml
version: '3.8'

services:
  api:
    build: .
    container_name: mi-api
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    volumes:
      - ./logs:/app/logs

  # Nginx como proxy inverso
  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - api
```

```bash
# Iniciar todos los servicios
docker compose up -d

# Ver estado
docker compose ps

# Ver logs
docker compose logs -f
```

---

### 4.6 Abrir el puerto en el firewall (ufw)

```bash
# Instalar ufw si no está
sudo apt install -y ufw

# Reglas básicas
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Permitir SSH (¡importante hacerlo antes de activar ufw!)
sudo ufw allow ssh

# Permitir HTTP y HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Activar el firewall
sudo ufw enable

# Ver estado
sudo ufw status verbose
```

---

## 5. Hosting casero en Windows

### 5.1 Preparar el sistema

Abre **PowerShell como administrador** para los siguientes comandos:

```powershell
# Ver tu IP local
ipconfig

# Ver tu IP pública
(Invoke-WebRequest -Uri "https://ifconfig.me" -UseBasicParsing).Content

# Verificar puertos en uso
netstat -ano | findstr ":80"
```

### Configurar una IP local estática (recomendado)

1. Abre **Panel de control** → **Redes e Internet** → **Centro de redes y recursos compartidos**
2. Haz clic en tu conexión de red activa
3. Haz clic en **Propiedades** → **Protocolo de Internet versión 4 (TCP/IPv4)**
4. Selecciona **Usar la siguiente dirección IP** y rellena:
   - Dirección IP: `192.168.1.100`
   - Máscara de subred: `255.255.255.0`
   - Puerta de enlace predeterminada: `192.168.1.1` (IP de tu router)
   - DNS preferido: `8.8.8.8`
   - DNS alternativo: `1.1.1.1`

O con PowerShell:

```powershell
# Ver interfaces de red disponibles
Get-NetAdapter

# Asignar IP estática (sustituye "Ethernet" por tu interfaz)
New-NetIPAddress -InterfaceAlias "Ethernet" `
  -IPAddress 192.168.1.100 `
  -PrefixLength 24 `
  -DefaultGateway 192.168.1.1

# Asignar DNS
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" `
  -ServerAddresses ("8.8.8.8","1.1.1.1")
```

---

### 5.2 Instalar IIS (servidor web de Windows)

IIS (*Internet Information Services*) es el servidor web nativo de Windows, ideal para alojar sitios web estáticos o aplicaciones ASP.NET.

```powershell
# Instalar IIS con PowerShell (como administrador)
Install-WindowsFeature -name Web-Server -IncludeManagementTools

# O activarlo desde la interfaz gráfica:
# Panel de control → Programas → Activar características de Windows
# → Marcar "Internet Information Services"
```

#### Configurar un sitio en IIS

1. Abre **Administrador de IIS** (busca `inetmgr` en el menú inicio)
2. En el panel izquierdo, expande el nombre de tu PC
3. Haz clic derecho en **Sitios** → **Agregar sitio web**
4. Rellena:
   - Nombre del sitio: `mi-sitio`
   - Ruta de acceso física: `C:\inetpub\wwwroot\mi-sitio`
   - Puerto: `80`
5. Haz clic en **Aceptar**

```powershell
# Crear el directorio y una página de prueba
New-Item -ItemType Directory -Force -Path "C:\inetpub\wwwroot\mi-sitio"

$html = @"
<!DOCTYPE html>
<html lang="es">
<head><meta charset="UTF-8"><title>Servidor Windows</title></head>
<body>
  <h1>¡Hola desde Windows! 🪟</h1>
  <p>Servidor casero en Windows funcionando.</p>
</body>
</html>
"@

Set-Content -Path "C:\inetpub\wwwroot\mi-sitio\index.html" -Value $html
```

---

### 5.3 Instalar Nginx en Windows

Nginx también está disponible para Windows y puede usarse como reverse proxy.

```powershell
# Instalar con winget (Windows Package Manager)
winget install nginx

# O descargar manualmente desde https://nginx.org/en/download.html
# y extraer en C:\nginx
```

```powershell
# Iniciar Nginx (desde la carpeta de instalación)
cd C:\nginx
.\nginx.exe

# O como servicio con NSSM (ver sección 5.4)

# Verificar que funciona
Invoke-WebRequest -Uri "http://localhost" -UseBasicParsing
```

**Archivos de configuración en Windows:**

```
C:\nginx\
├── conf\
│   └── nginx.conf          ← Configuración principal
├── html\
│   └── index.html          ← Raíz web por defecto
└── logs\
    ├── access.log
    └── error.log
```

Edita `C:\nginx\conf\nginx.conf` con tu editor favorito (VS Code, Notepad++, etc.).

---

### 5.4 Desplegar una aplicación Node.js con PM2

#### Instalar Node.js en Windows

Descarga el instalador desde [nodejs.org](https://nodejs.org) o usa winget:

```powershell
winget install OpenJS.NodeJS.LTS
```

#### Instalar PM2 y el wrapper para Windows

```powershell
npm install -g pm2
npm install -g pm2-windows-startup

# Configurar PM2 para iniciar con Windows
pm2-startup install
```

#### Iniciar la aplicación

```powershell
# Suponiendo que tu app está en C:\apps\mi-api\app.js
cd C:\apps\mi-api
pm2 start app.js --name mi-api
pm2 save

# Ver estado
pm2 status

# Ver logs
pm2 logs mi-api
```

#### Configurar Nginx como reverse proxy en Windows

Edita `C:\nginx\conf\nginx.conf` y reemplaza o añade en el bloque `http`:

```nginx
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass         http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection 'upgrade';
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```powershell
# Recargar la configuración de Nginx
cd C:\nginx
.\nginx.exe -s reload
```

#### Instalar Nginx como servicio de Windows con NSSM

```powershell
# Instalar NSSM (Non-Sucking Service Manager)
winget install NSSM.NSSM

# Instalar Nginx como servicio
nssm install nginx "C:\nginx\nginx.exe"
nssm set nginx AppDirectory "C:\nginx"
nssm start nginx

# Ver estado
nssm status nginx
```

---

### 5.5 Desplegar con Docker Desktop

1. Descarga **Docker Desktop** desde [docker.com](https://www.docker.com/products/docker-desktop/)
2. Instala y reinicia Windows
3. Abre Docker Desktop y espera a que arranque

```powershell
# Verificar instalación
docker --version
docker run hello-world

# Construir y ejecutar tu aplicación (mismos comandos que en Linux)
cd C:\apps\mi-api
docker build -t mi-api:latest .
docker run -d --name mi-api --restart unless-stopped -p 3000:3000 mi-api:latest

# Ver contenedores
docker ps

# Ver logs
docker logs -f mi-api
```

```powershell
# Con Docker Compose
docker compose up -d
docker compose ps
docker compose logs -f
```

> **Nota**: Docker Desktop en Windows usa WSL2 (Windows Subsystem for Linux) internamente. Asegúrate de tenerlo habilitado: `wsl --install`

---

### 5.6 Abrir el puerto en el Firewall de Windows

```powershell
# Abrir puerto 80 (HTTP)
New-NetFirewallRule -DisplayName "HTTP Puerto 80" `
  -Direction Inbound `
  -Protocol TCP `
  -LocalPort 80 `
  -Action Allow

# Abrir puerto 443 (HTTPS)
New-NetFirewallRule -DisplayName "HTTPS Puerto 443" `
  -Direction Inbound `
  -Protocol TCP `
  -LocalPort 443 `
  -Action Allow

# Ver reglas existentes
Get-NetFirewallRule | Where-Object { $_.DisplayName -like "*HTTP*" }
```

También puedes hacerlo desde la interfaz gráfica:

1. Busca **Firewall de Windows Defender con seguridad avanzada** en el menú inicio
2. Haz clic en **Reglas de entrada** → **Nueva regla**
3. Selecciona **Puerto** → **TCP** → Puerto específico: `80`
4. Elige **Permitir la conexión** → Asigna un nombre y guarda

---

## 6. Exponer tu servidor al exterior

### 6.1 IP dinámica y DDNS

La mayoría de conexiones domésticas tienen **IP pública dinámica** (cambia periódicamente). Para solucionarlo usa un servicio **DDNS (Dynamic DNS)**:

| Servicio | Plan gratuito | Notas |
|----------|--------------|-------|
| [DuckDNS](https://www.duckdns.org/) | ✅ Sí | Muy sencillo, ideal para principiantes |
| [No-IP](https://www.noip.com/) | ✅ Sí (limitado) | Popular, cliente disponible para Windows y Linux |
| [Cloudflare](https://www.cloudflare.com/) | ✅ Sí | Requiere dominio propio; el mejor para uso avanzado |
| [FreeDNS](https://freedns.afraid.org/) | ✅ Sí | Muchos subdominios disponibles |

#### Ejemplo con DuckDNS (Linux)

```bash
# Crear un script de actualización
mkdir -p ~/duckdns
nano ~/duckdns/duck.sh
```

```bash
#!/bin/bash
# Reemplaza TU_TOKEN y TU_SUBDOMINIO con los tuyos
echo url="https://www.duckdns.org/update?domains=TU_SUBDOMINIO&token=TU_TOKEN&ip=" | curl -k -o ~/duckdns/duck.log -K -
```

```bash
chmod +x ~/duckdns/duck.sh

# Programar con cron (cada 5 minutos)
crontab -e
# Añade esta línea:
*/5 * * * * ~/duckdns/duck.sh >/dev/null 2>&1
```

#### Ejemplo con DuckDNS (Windows - PowerShell)

```powershell
# Guardar como C:\duckdns\update.ps1
$token = "TU_TOKEN"
$domain = "TU_SUBDOMINIO"
$url = "https://www.duckdns.org/update?domains=$domain&token=$token&ip="
Invoke-WebRequest -Uri $url -UseBasicParsing | Out-File "C:\duckdns\duck.log"
```

```powershell
# Crear una tarea programada para ejecutarlo cada 5 minutos
$action = New-ScheduledTaskAction -Execute "powershell.exe" `
  -Argument "-File C:\duckdns\update.ps1"
$trigger = New-ScheduledTaskTrigger -RepetitionInterval (New-TimeSpan -Minutes 5) -Once -At (Get-Date)
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName "DuckDNS-Update" -RunLevel Highest
```

---

### 6.2 Port Forwarding en el router

Cada router tiene una interfaz diferente, pero los pasos son similares:

1. Accede a la interfaz web del router (normalmente `http://192.168.1.1` o `http://192.168.0.1`)
2. Busca la sección **Port Forwarding**, **NAT**, **Virtual Server** o **Reenvío de puertos**
3. Añade una regla:
   - Puerto externo: `80`
   - IP interna: `192.168.1.100` (la IP de tu PC)
   - Puerto interno: `80`
   - Protocolo: `TCP`
4. Repite para el puerto `443`
5. Guarda y aplica los cambios

```
┌──────────────────────────────────────────────┐
│  Router - Port Forwarding                    │
├────────┬──────────────┬────────┬─────────────┤
│ Puerto │ IP Interna   │ Puerto │ Protocolo   │
│ Externo│              │ Interno│             │
├────────┼──────────────┼────────┼─────────────┤
│ 80     │ 192.168.1.100│ 80     │ TCP         │
│ 443    │ 192.168.1.100│ 443    │ TCP         │
│ 22     │ 192.168.1.100│ 22     │ TCP (SSH)   │
└────────┴──────────────┴────────┴─────────────┘
```

> ⚠️ **Importante**: Algunos ISPs bloquean los puertos 80 y 443 en conexiones domésticas. Si es tu caso, prueba con puertos alternativos como 8080 o usa Cloudflare Tunnel (ver sección 6.4).

---

### 6.3 HTTPS con Let's Encrypt (Certbot)

Para obtener un certificado SSL gratuito necesitas un dominio (puede ser de DDNS) y los puertos 80 y 443 abiertos.

#### En Linux con Nginx

```bash
# Instalar Certbot
sudo apt install -y certbot python3-certbot-nginx

# Obtener y configurar el certificado (reemplaza con tu dominio)
sudo certbot --nginx -d tu-subdominio.duckdns.org

# Renovación automática (cron ya se configura automáticamente)
sudo certbot renew --dry-run
```

Certbot modifica automáticamente tu configuración de Nginx para redirigir HTTP a HTTPS.

#### En Windows con Nginx

```powershell
# Instalar Win-ACME (cliente ACME para Windows)
winget install win-acme

# Ejecutar el asistente
wacs.exe
# Selecciona: Nginx, tu dominio, y sigue las instrucciones
```

---

### 6.4 Túneles: Cloudflare Tunnel y Ngrok

Si no puedes hacer port forwarding (router de operadora, IP bloqueada, etc.), los **túneles** son la solución: tu PC se conecta hacia afuera y Cloudflare/Ngrok reenvían el tráfico.

#### Cloudflare Tunnel (gratuito, recomendado para producción)

Requiere una cuenta gratuita en Cloudflare y un dominio (puede ser gratuito con Freenom o Cloudflare Registrar).

```bash
# Linux: Instalar cloudflared
curl -L --output cloudflared.deb \
  https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared.deb

# Autenticar
cloudflared tunnel login

# Crear el túnel
cloudflared tunnel create mi-tunnel

# Configurar el túnel
mkdir -p ~/.cloudflared
nano ~/.cloudflared/config.yml
```

```yaml
tunnel: <ID-DEL-TUNEL>
credentials-file: /home/TU_USUARIO/.cloudflared/<ID>.json

ingress:
  - hostname: miapp.tudominio.com
    service: http://localhost:3000
  - service: http_status:404
```

```bash
# Enrutar el dominio al túnel
cloudflared tunnel route dns mi-tunnel miapp.tudominio.com

# Iniciar el túnel
cloudflared tunnel run mi-tunnel

# Como servicio del sistema
sudo cloudflared service install
sudo systemctl start cloudflared
```

```powershell
# Windows: Instalar cloudflared
winget install Cloudflare.cloudflared

# Los demás comandos son idénticos a Linux
cloudflared tunnel login
cloudflared tunnel create mi-tunnel
# ... (misma configuración)
cloudflared tunnel run mi-tunnel

# Instalar como servicio de Windows
cloudflared service install
```

#### Ngrok (ideal para pruebas rápidas)

```bash
# Instalar en Linux
curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok

# Autenticar (necesitas cuenta en ngrok.com)
ngrok config add-authtoken TU_TOKEN

# Exponer el puerto 3000
ngrok http 3000
```

```powershell
# Windows
winget install ngrok.ngrok
ngrok config add-authtoken TU_TOKEN
ngrok http 3000
```

> ⚠️ **Ngrok** es ideal para desarrollo y pruebas. Para producción usa Cloudflare Tunnel (más estable y sin límites de tiempo en el plan gratuito).

---

## 7. Proyecto práctico: Deploy completo de una API REST

Vamos a hacer un deploy completo de una API REST desde cero, funcionando en internet desde tu propio PC.

### Estructura del proyecto

```
mi-api-casera/
├── app.js
├── package.json
├── Dockerfile
├── docker-compose.yml
└── nginx.conf
```

### Paso 1: Crear la API

```bash
mkdir ~/mi-api-casera && cd ~/mi-api-casera
npm init -y
npm install express cors helmet morgan
```

```javascript
// app.js
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');
const os = require('os');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(helmet());
app.use(cors());
app.use(morgan('combined'));
app.use(express.json());

// Base de datos en memoria (para el ejemplo)
let tareas = [
  { id: 1, titulo: 'Aprender hosting casero', completada: false },
  { id: 2, titulo: 'Configurar Nginx', completada: true },
];

// Rutas
app.get('/', (req, res) => {
  res.json({
    nombre: 'Mi API Casera',
    version: '1.0.0',
    servidor: os.hostname(),
    uptime: `${Math.floor(process.uptime())}s`,
    timestamp: new Date().toISOString()
  });
});

app.get('/salud', (req, res) => res.json({ estado: 'ok' }));

app.get('/tareas', (req, res) => res.json(tareas));

app.post('/tareas', (req, res) => {
  const { titulo } = req.body;
  if (!titulo) return res.status(400).json({ error: 'El título es requerido' });
  const nueva = { id: tareas.length + 1, titulo, completada: false };
  tareas.push(nueva);
  res.status(201).json(nueva);
});

app.put('/tareas/:id', (req, res) => {
  const tarea = tareas.find(t => t.id === parseInt(req.params.id));
  if (!tarea) return res.status(404).json({ error: 'Tarea no encontrada' });
  Object.assign(tarea, req.body);
  res.json(tarea);
});

app.delete('/tareas/:id', (req, res) => {
  const idx = tareas.findIndex(t => t.id === parseInt(req.params.id));
  if (idx === -1) return res.status(404).json({ error: 'Tarea no encontrada' });
  tareas.splice(idx, 1);
  res.status(204).send();
});

app.listen(PORT, () => console.log(`API escuchando en http://localhost:${PORT}`));
```

### Paso 2: Dockerfile

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY app.js .

EXPOSE 3000
CMD ["node", "app.js"]
```

### Paso 3: nginx.conf

```nginx
server {
    listen 80;
    server_name _;

    # Seguridad básica
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    add_header X-XSS-Protection "1; mode=block";

    # Límite de tamaño de petición
    client_max_body_size 10M;

    location / {
        proxy_pass         http://api:3000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection 'upgrade';
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
        proxy_read_timeout 300;
        proxy_connect_timeout 300;
    }

    # Endpoint de salud sin logs
    location /salud {
        proxy_pass http://api:3000/salud;
        access_log off;
    }
}
```

### Paso 4: docker-compose.yml

```yaml
version: '3.8'

services:
  api:
    build: .
    container_name: mi-api
    restart: unless-stopped
    environment:
      - NODE_ENV=production
      - PORT=3000
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost:3000/salud"]
      interval: 30s
      timeout: 10s
      retries: 3

  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      api:
        condition: service_healthy
```

### Paso 5: Desplegar

```bash
# Linux
cd ~/mi-api-casera
docker compose up -d
docker compose ps

# Windows (PowerShell)
cd C:\apps\mi-api-casera
docker compose up -d
docker compose ps
```

### Paso 6: Verificar desde internet

```bash
# Desde tu red local
curl http://localhost/tareas

# Desde internet (reemplaza con tu IP pública o dominio DDNS)
curl http://TU_IP_PUBLICA/tareas
curl http://tu-subdominio.duckdns.org/tareas

# Crear una tarea
curl -X POST http://TU_IP_PUBLICA/tareas \
  -H "Content-Type: application/json" \
  -d '{"titulo": "Tarea de prueba"}'
```

### Resultado esperado

```json
{
  "nombre": "Mi API Casera",
  "version": "1.0.0",
  "servidor": "mi-pc",
  "uptime": "3600s",
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

---

## 8. Comparación: Linux vs Windows para hosting casero

| Característica | Linux | Windows |
|----------------|-------|---------|
| 💰 **Coste** | Gratuito (Ubuntu, Debian) | Licencia (Windows 10/11 Home/Pro) |
| ⚡ **Rendimiento** | Menor consumo de RAM y CPU | Mayor consumo base del sistema |
| 🔧 **Configuración** | Por terminal (más control) | Mixto: GUI + PowerShell |
| 🌐 **Nginx/Apache** | Nativo, paquete apt | Disponible, requiere más pasos |
| 🪟 **IIS** | No disponible | Nativo, muy integrado |
| 🐳 **Docker** | Primera clase | Docker Desktop (WSL2) |
| 🔐 **Seguridad** | Históricamente más seguro | Requiere más configuración |
| 🔄 **Actualizaciones** | Sin reinicios (mayoría) | Reinicios frecuentes |
| 📚 **Documentación** | Abundante para hosting | Buena pero más dispersa |
| 🎓 **Curva aprendizaje** | Más alta al inicio | Más familiar para usuarios de Windows |

### ¿Cuándo elegir Linux?

- Quieres **máximo control y eficiencia**
- Vas a dejarlo corriendo **24/7** (servidor dedicado, una Raspberry Pi, un PC viejo)
- Prefieres la **línea de comandos**
- Quieres aprender administración de sistemas como se hace en empresas

### ¿Cuándo elegir Windows?

- Usas el PC también para **trabajo o gaming** y no quieres otro sistema operativo
- Tus aplicaciones son **.NET/ASP.NET** (IIS es ideal)
- Quieres una configuración **más visual** al principio
- Ya tienes experiencia con Windows y no quieres cambiar

---

## 9. Seguridad básica del servidor

### Cambiar el puerto SSH (Linux)

```bash
sudo nano /etc/ssh/sshd_config
# Cambiar: Port 22  →  Port 2222
sudo systemctl restart ssh

# Actualizar el firewall
sudo ufw allow 2222/tcp
sudo ufw delete allow ssh
```

### Deshabilitar el acceso root por SSH (Linux)

```bash
sudo nano /etc/ssh/sshd_config
# Establecer: PermitRootLogin no
sudo systemctl restart ssh
```

### Usar autenticación por clave SSH en lugar de contraseña

```bash
# En tu PC local (el que se conectará al servidor)
ssh-keygen -t ed25519 -C "mi@correo.com"
ssh-copy-id -p 2222 usuario@TU_IP_PUBLICA

# Deshabilitar autenticación por contraseña
sudo nano /etc/ssh/sshd_config
# PasswordAuthentication no
sudo systemctl restart ssh
```

### Instalar Fail2ban (protección contra ataques de fuerza bruta)

```bash
sudo apt install -y fail2ban

sudo nano /etc/fail2ban/jail.local
```

```ini
[DEFAULT]
bantime  = 1h
findtime = 10m
maxretry = 5

[sshd]
enabled = true
port    = 2222

[nginx-http-auth]
enabled = true
```

```bash
sudo systemctl enable --now fail2ban
sudo fail2ban-client status
```

### Actualizaciones automáticas de seguridad (Linux)

```bash
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

### Actualizaciones automáticas (Windows)

- **Panel de control** → **Windows Update** → **Opciones avanzadas** → Activa las actualizaciones automáticas.

### Headers de seguridad en Nginx

```nginx
# Añade en tu bloque server {}
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self'" always;
```

### Rate limiting en Nginx

```nginx
# En el bloque http {} de nginx.conf
limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;

# En tu bloque server/location
location /api/ {
    limit_req zone=api burst=20 nodelay;
    proxy_pass http://localhost:3000;
}
```

---

## 10. Buenas prácticas

### General

- ✅ Usa siempre HTTPS (Let's Encrypt es gratuito)
- ✅ Mantén el sistema operativo y las dependencias actualizadas
- ✅ Usa un gestor de procesos (PM2, systemd, NSSM) para que la app arranque automáticamente
- ✅ Configura backups automáticos de tus datos
- ✅ Monitoriza el servidor (CPU, RAM, disco) con herramientas como `htop`, `Glances` o Netdata
- ✅ Usa variables de entorno para secretos, nunca los hardcodees en el código

### Estructura de puertos recomendada

```
Puerto 80  (HTTP)   → Nginx → Redirige a HTTPS
Puerto 443 (HTTPS)  → Nginx → Tu aplicación
Puerto 2222 (SSH)   → Acceso seguro al servidor (solo tú)
Tu app: solo localhost:3000 (nunca expuesta directamente)
```

### Gestión de logs

```bash
# Linux: Rotar logs automáticamente con logrotate
sudo nano /etc/logrotate.d/mi-app
```

```
/var/log/nginx/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    sharedscripts
    postrotate
        systemctl reload nginx
    endscript
}
```

### Script de deploy rápido (Linux)

```bash
#!/bin/bash
# deploy.sh - actualizar y reiniciar la aplicación

set -e

echo "🚀 Iniciando deploy..."

# Actualizar código (si usas git)
git pull origin main

# Reconstruir y reiniciar con Docker Compose
docker compose pull
docker compose up -d --build

# Verificar que todo funciona
sleep 5
curl -sf http://localhost/salud && echo "✅ Deploy exitoso" || echo "❌ Error en el deploy"

echo "📋 Estado de los contenedores:"
docker compose ps
```

```bash
chmod +x deploy.sh
./deploy.sh
```

---

## 11. Solución de problemas comunes

### ❌ "Connection refused" al acceder desde internet

```bash
# 1. Verificar que la app está corriendo
curl http://localhost:3000

# 2. Verificar que Nginx está corriendo
sudo systemctl status nginx          # Linux
nssm status nginx                    # Windows

# 3. Verificar que el firewall permite el puerto
sudo ufw status                      # Linux
Get-NetFirewallRule | Where-Object { $_.LocalPort -eq 80 }  # Windows

# 4. Verificar el port forwarding del router
# → Accede a la interfaz de tu router y revisa la configuración

# 5. Verificar que el ISP no bloquea el puerto
# → Prueba desde un móvil con datos (sin WiFi)
nmap -p 80 TU_IP_PUBLICA
```

### ❌ Nginx no arranca

```bash
# Ver el error exacto
sudo nginx -t
sudo journalctl -u nginx -n 50      # Linux

# En Windows
type C:\nginx\logs\error.log
```

### ❌ PM2 no arranca con el sistema

```bash
# Linux
pm2 startup systemd
# (ejecutar el comando que muestre)
pm2 save

# Windows
pm2-startup install
pm2 save
```

### ❌ Certificado SSL no funciona o ha expirado

```bash
# Renovar manualmente
sudo certbot renew

# Verificar fecha de expiración
sudo certbot certificates

# Forzar renovación
sudo certbot renew --force-renewal
```

### ❌ La IP pública cambia y el dominio deja de funcionar

```bash
# Verificar que el script DDNS está corriendo
crontab -l                         # Linux
Get-ScheduledTask -TaskName "DuckDNS-Update"  # Windows

# Ver la IP actual
curl ifconfig.me

# Actualizar manualmente
~/duckdns/duck.sh                  # Linux
```

### ❌ Docker no encuentra la red o los contenedores no se comunican

```bash
# Ver redes de Docker
docker network ls

# Inspeccionar la red del compose
docker network inspect mi-api-casera_default

# Reiniciar Docker
sudo systemctl restart docker       # Linux
# → Reiniciar Docker Desktop desde la barra de tareas  (Windows)
```

### ❌ Alto consumo de CPU o RAM

```bash
# Ver qué proceso consume más (Linux)
htop
# o
top -b -n 1 | head -20

# Ver qué proceso consume más (Windows)
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10

# Ver estadísticas de Docker
docker stats
```

---

> 💡 **Consejo final**: Empieza con algo sencillo (un sitio estático o una API mínima) y ve añadiendo complejidad poco a poco. El hosting casero tiene una curva de aprendizaje, pero es una de las mejores formas de entender cómo funciona internet de verdad.

---

*Tutorial creado para la comunidad de estudiantes de informática. Para contribuir, consulta el [README principal](../README.md).*
