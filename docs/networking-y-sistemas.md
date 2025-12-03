# 🌍 Tutorial Completo de Networking y Sistemas

Este tutorial cubre los fundamentos de redes y sistemas que todo estudiante de informática debe conocer.

---

## 📋 Tabla de Contenidos

1. [Fundamentos de Redes](#fundamentos-de-redes)
2. [Modelo OSI y TCP/IP](#modelo-osi-y-tcpip)
3. [Direccionamiento IP](#direccionamiento-ip)
4. [Protocolos de Red](#protocolos-de-red)
5. [DNS - Sistema de Nombres de Dominio](#dns---sistema-de-nombres-de-dominio)
6. [Configuración de Servidores](#configuración-de-servidores)
7. [SSH y Conexiones Remotas](#ssh-y-conexiones-remotas)
8. [Virtualización](#virtualización)
9. [Cloud Computing](#cloud-computing)
10. [Ejercicios Prácticos](#ejercicios-prácticos)

---

## Fundamentos de Redes

### ¿Qué es una Red?

Una red de computadoras es un conjunto de dispositivos interconectados que pueden compartir recursos y comunicarse entre sí.

### Tipos de Redes

| Tipo | Descripción | Alcance |
|------|-------------|---------|
| **PAN** | Red de Área Personal | Pocos metros |
| **LAN** | Red de Área Local | Edificio/Campus |
| **MAN** | Red de Área Metropolitana | Ciudad |
| **WAN** | Red de Área Amplia | País/Continente |

### Topologías de Red

- **Estrella**: Todos los dispositivos conectados a un punto central (switch/hub)
- **Bus**: Todos los dispositivos conectados a un cable principal
- **Anillo**: Dispositivos conectados en forma circular
- **Malla**: Cada dispositivo conectado a varios otros dispositivos

---

## Modelo OSI y TCP/IP

### Modelo OSI (7 Capas)

```
┌─────────────────────────────────────┐
│ 7. Aplicación    (HTTP, FTP, SMTP)  │
├─────────────────────────────────────┤
│ 6. Presentación  (SSL, Cifrado)     │
├─────────────────────────────────────┤
│ 5. Sesión        (NetBIOS, RPC)     │
├─────────────────────────────────────┤
│ 4. Transporte    (TCP, UDP)         │
├─────────────────────────────────────┤
│ 3. Red           (IP, ICMP, ARP)    │
├─────────────────────────────────────┤
│ 2. Enlace de Datos (Ethernet, WiFi) │
├─────────────────────────────────────┤
│ 1. Física        (Cables, Señales)  │
└─────────────────────────────────────┘
```

### Modelo TCP/IP (4 Capas)

```
┌─────────────────────────────────────┐
│ 4. Aplicación    (HTTP, FTP, DNS)   │
├─────────────────────────────────────┤
│ 3. Transporte    (TCP, UDP)         │
├─────────────────────────────────────┤
│ 2. Internet      (IP, ICMP)         │
├─────────────────────────────────────┤
│ 1. Acceso a Red  (Ethernet, WiFi)   │
└─────────────────────────────────────┘
```

### Comparación entre Modelos

| OSI | TCP/IP | Función |
|-----|--------|---------|
| Aplicación, Presentación, Sesión | Aplicación | Interfaz con el usuario |
| Transporte | Transporte | Control de flujo y errores |
| Red | Internet | Enrutamiento |
| Enlace de Datos, Física | Acceso a Red | Transmisión física |

---

## Direccionamiento IP

### IPv4

Una dirección IPv4 consta de 32 bits divididos en 4 octetos.

```
Ejemplo: 192.168.1.100
         |---------|---------|
            Red     |   Host
                    |
                 Subred
```

### Clases de Direcciones IP

| Clase | Rango | Máscara por defecto | Uso |
|-------|-------|---------------------|-----|
| A | 1.0.0.0 - 126.255.255.255 | 255.0.0.0 | Redes grandes |
| B | 128.0.0.0 - 191.255.255.255 | 255.255.0.0 | Redes medianas |
| C | 192.0.0.0 - 223.255.255.255 | 255.255.255.0 | Redes pequeñas |

### Direcciones IP Privadas

```
Clase A: 10.0.0.0 - 10.255.255.255
Clase B: 172.16.0.0 - 172.31.255.255
Clase C: 192.168.0.0 - 192.168.255.255
```

### Subnetting (División en Subredes)

```bash
# Ejemplo: Red 192.168.1.0/24 dividida en 4 subredes
192.168.1.0/26   → 192.168.1.0 - 192.168.1.63
192.168.1.64/26  → 192.168.1.64 - 192.168.1.127
192.168.1.128/26 → 192.168.1.128 - 192.168.1.191
192.168.1.192/26 → 192.168.1.192 - 192.168.1.255
```

### IPv6

Direcciones de 128 bits representadas en hexadecimal.

```
Ejemplo: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
Abreviado: 2001:db8:85a3::8a2e:370:7334
```

---

## Protocolos de Red

### HTTP/HTTPS (Puerto 80/443)

Protocolo de transferencia de hipertexto.

```
GET /index.html HTTP/1.1
Host: www.ejemplo.com
User-Agent: Mozilla/5.0
Accept: text/html
```

### TCP vs UDP

| Característica | TCP | UDP |
|----------------|-----|-----|
| Conexión | Orientado a conexión | Sin conexión |
| Fiabilidad | Garantiza entrega | No garantiza |
| Velocidad | Más lento | Más rápido |
| Uso | Web, Email, FTP | Streaming, DNS, Gaming |

### Puertos Comunes

| Puerto | Protocolo | Servicio |
|--------|-----------|----------|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |

---

## DNS - Sistema de Nombres de Dominio

### ¿Cómo funciona DNS?

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Cliente   │────▶│  DNS Local  │────▶│  DNS Root   │
└─────────────┘     └─────────────┘     └─────────────┘
                           │                    │
                           ▼                    ▼
                    ┌─────────────┐     ┌─────────────┐
                    │   Caché     │     │  DNS TLD    │
                    └─────────────┘     └─────────────┘
                                               │
                                               ▼
                                        ┌─────────────┐
                                        │ DNS Autorit.│
                                        └─────────────┘
```

### Tipos de Registros DNS

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **A** | Dirección IPv4 | ejemplo.com → 192.168.1.1 |
| **AAAA** | Dirección IPv6 | ejemplo.com → 2001:db8::1 |
| **CNAME** | Alias | www.ejemplo.com → ejemplo.com |
| **MX** | Servidor de correo | mail.ejemplo.com |
| **NS** | Servidor de nombres | ns1.ejemplo.com |
| **TXT** | Texto | Verificaciones, SPF |

### Comandos útiles

```bash
# Consultar DNS
nslookup google.com
dig google.com
host google.com

# Ver registros específicos
dig google.com MX
dig google.com NS
dig google.com TXT
```

---

## Configuración de Servidores

### Apache

```bash
# Instalación en Ubuntu/Debian
sudo apt update
sudo apt install apache2

# Iniciar/Detener servicio
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl restart apache2

# Habilitar al inicio
sudo systemctl enable apache2
```

**Configuración básica** (`/etc/apache2/sites-available/000-default.conf`):

```apache
<VirtualHost *:80>
    ServerName midominio.com
    ServerAdmin webmaster@midominio.com
    DocumentRoot /var/www/html
    
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### Nginx

```bash
# Instalación en Ubuntu/Debian
sudo apt update
sudo apt install nginx

# Iniciar/Detener servicio
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
```

**Configuración básica** (`/etc/nginx/sites-available/default`):

```nginx
server {
    listen 80;
    server_name midominio.com;
    root /var/www/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### Comparación Apache vs Nginx

| Característica | Apache | Nginx |
|----------------|--------|-------|
| Arquitectura | Basada en procesos | Basada en eventos |
| Memoria | Mayor consumo | Menor consumo |
| Configuración | .htaccess | Archivo central |
| Mejor para | Contenido dinámico | Contenido estático, proxy |

---

## SSH y Conexiones Remotas

### Conexión SSH Básica

```bash
# Conectar a servidor remoto
ssh usuario@servidor.com

# Conectar con puerto específico
ssh -p 2222 usuario@servidor.com

# Conectar con clave privada
ssh -i ~/.ssh/mi_clave usuario@servidor.com
```

### Generación de Claves SSH

```bash
# Generar par de claves RSA
ssh-keygen -t rsa -b 4096 -C "tu@email.com"

# Generar par de claves Ed25519 (más moderno)
ssh-keygen -t ed25519 -C "tu@email.com"

# Copiar clave pública al servidor
ssh-copy-id usuario@servidor.com
```

### Configuración SSH (`~/.ssh/config`)

```
Host mi-servidor
    HostName servidor.ejemplo.com
    User mi_usuario
    Port 22
    IdentityFile ~/.ssh/id_rsa

Host produccion
    HostName 192.168.1.100
    User admin
    Port 2222
```

### Transferencia de Archivos

```bash
# SCP - Copiar archivos
scp archivo.txt usuario@servidor:/ruta/destino/
scp -r directorio/ usuario@servidor:/ruta/destino/

# SFTP - Transferencia interactiva
sftp usuario@servidor.com
sftp> put archivo_local.txt
sftp> get archivo_remoto.txt
sftp> ls
sftp> cd /ruta
```

### Túneles SSH

```bash
# Túnel local (acceder a servicio remoto localmente)
ssh -L 8080:localhost:80 usuario@servidor.com

# Túnel remoto (exponer servicio local al servidor)
ssh -R 8080:localhost:80 usuario@servidor.com

# Túnel dinámico (proxy SOCKS)
ssh -D 1080 usuario@servidor.com
```

---

## Virtualización

### Tipos de Virtualización

- **Tipo 1 (Bare Metal)**: VMware ESXi, Microsoft Hyper-V, Xen
- **Tipo 2 (Hosted)**: VirtualBox, VMware Workstation

### VirtualBox

```bash
# Instalación en Ubuntu
sudo apt install virtualbox

# Comandos CLI básicos
VBoxManage list vms                    # Listar VMs
VBoxManage startvm "nombre_vm"         # Iniciar VM
VBoxManage controlvm "nombre_vm" poweroff  # Apagar VM
VBoxManage snapshot "nombre_vm" take "snapshot_name"
```

### Docker (Contenedores)

```bash
# Instalación de Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Comandos básicos
docker pull nginx                      # Descargar imagen
docker run -d -p 80:80 nginx           # Ejecutar contenedor
docker ps                              # Listar contenedores activos
docker stop <container_id>             # Detener contenedor
docker rm <container_id>               # Eliminar contenedor
docker images                          # Listar imágenes
docker rmi <image_id>                  # Eliminar imagen
```

**Dockerfile ejemplo**:

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y nginx
COPY index.html /var/www/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**Docker Compose** (`docker-compose.yml`):

```yaml
version: '3.8'
services:
  web:
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
  
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: myapp
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```

---

## Cloud Computing

### Modelos de Servicio

| Modelo | Descripción | Ejemplos |
|--------|-------------|----------|
| **IaaS** | Infraestructura como Servicio | AWS EC2, Azure VMs |
| **PaaS** | Plataforma como Servicio | Heroku, Google App Engine |
| **SaaS** | Software como Servicio | Gmail, Dropbox, Slack |

### AWS - Servicios Básicos

```bash
# Instalar AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Configurar credenciales
aws configure

# Comandos básicos EC2
aws ec2 describe-instances
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# Comandos básicos S3
aws s3 ls
aws s3 cp archivo.txt s3://mi-bucket/
aws s3 sync ./local-folder s3://mi-bucket/
```

### Servicios Cloud Principales

| Servicio | AWS | Azure | GCP |
|----------|-----|-------|-----|
| Compute | EC2 | Virtual Machines | Compute Engine |
| Storage | S3 | Blob Storage | Cloud Storage |
| Database | RDS | SQL Database | Cloud SQL |
| Serverless | Lambda | Functions | Cloud Functions |
| Container | ECS/EKS | AKS | GKE |

---

## Ejercicios Prácticos

### Ejercicio 1: Configurar una red local

1. Configura un servidor DHCP en tu red local
2. Asigna direcciones IP estáticas a algunos dispositivos
3. Configura un servidor DNS local

### Ejercicio 2: Montar un servidor web

1. Instala Apache o Nginx
2. Configura un VirtualHost
3. Habilita HTTPS con Let's Encrypt

```bash
# Instalar Certbot para SSL gratuito
sudo apt install certbot python3-certbot-apache
sudo certbot --apache -d midominio.com
```

### Ejercicio 3: Configurar SSH seguro

1. Genera un par de claves SSH
2. Deshabilita autenticación por contraseña
3. Cambia el puerto por defecto

```bash
# Editar /etc/ssh/sshd_config
Port 2222
PasswordAuthentication no
PermitRootLogin no
```

### Ejercicio 4: Docker básico

1. Crea un Dockerfile para una aplicación simple
2. Construye y ejecuta el contenedor
3. Crea un docker-compose.yml con múltiples servicios

### Ejercicio 5: AWS básico

1. Crea una cuenta de AWS (tier gratuito)
2. Lanza una instancia EC2
3. Configura un bucket S3

---

## 📚 Recursos Adicionales

### Documentación Oficial
- [Cisco Networking Academy](https://www.netacad.com/)
- [Linux Documentation](https://www.kernel.org/doc/html/latest/)
- [Docker Docs](https://docs.docker.com/)
- [AWS Documentation](https://docs.aws.amazon.com/)

### Herramientas Útiles
- **Wireshark**: Análisis de paquetes de red
- **Nmap**: Escaneo de puertos y redes
- **Netcat**: Herramienta de red versátil
- **tcpdump**: Captura de paquetes en línea de comandos

### Certificaciones Recomendadas
- CompTIA Network+
- Cisco CCNA
- AWS Certified Solutions Architect
- Linux Foundation Certified System Administrator

---

## 🎯 Siguiente Paso

Una vez dominados estos conceptos, continúa con el [Tutorial de Seguridad Informática](./seguridad-informatica.md) para aprender a proteger tus sistemas y redes.
