# Guía paso a paso: Convierte un PC viejo en servidor casero

Esta guía te lleva de cero a un servidor casero funcional. Empieza con “Pasos rápidos” y luego avanza por ejemplos en orden: Básico → Intermedio → Avanzado. Se asume Ubuntu Server LTS como sistema base y Docker/Compose para desplegar servicios (por su simplicidad). Cuando el ejemplo implique instalar otro sistema (p. ej. Proxmox, pfSense u OpenMediaVault), se indica explícitamente.

> Nota: Ejecuta los comandos con un usuario con sudo. Cambia rutas y contraseñas a tus valores. Para exponer servicios en Internet, usa un dominio propio y certificados TLS (Let’s Encrypt).

## Requisitos previos recomendados
- CPU: cualquier x86_64 de los últimos 10–12 años sirve.
- RAM: 4–8 GB básico; 16 GB si usarás ZFS, VMs o muchos contenedores.
- Disco: SSD para el sistema/contendores; HDD(s) para datos.
- Red: Gigabit. Si será router/firewall, necesitas 2 NICs.
- Energía y ruido: limpia el equipo, cambia pasta térmica, activa C-States en BIOS si procede.

---

## Pasos rápidos (15–30 min)

1) Instala Ubuntu Server LTS y fija IP estática
- Durante la instalación, asigna IP estática o hazlo luego con Netplan.

2) Configura base del sistema
```bash
sudo apt update && sudo apt -y upgrade
sudo apt -y install curl ca-certificates ufw
sudo ufw allow OpenSSH
sudo ufw enable
```

3) Instala Docker y Docker Compose plugin
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker "$USER"
newgrp docker
sudo apt -y install docker-compose-plugin
```

4) Crea estructura de carpetas para stacks
```bash
mkdir -p ~/stacks/{npm,pi-hole,wireguard,jellyfin,nextcloud}
```

5) Despliega un reverse proxy con HTTPS automático (Nginx Proxy Manager)
```bash
cd ~/stacks/npm
cat > docker-compose.yml << 'YAML'
services:
  npm:
    image: jc21/nginx-proxy-manager:latest
    restart: unless-stopped
    ports:
      - "80:80"
      - "81:81"   # panel
      - "443:443"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
YAML

docker compose up -d
```
- Accede a http://IP:81 (admin@example.com / changeme). Cambia credenciales.
- Para exponer dominios, abre puertos 80 y 443 en tu router hacia este servidor.

6) (Opcional) Bloqueo de publicidad para toda la red con Pi-hole

En Ubuntu, libera el puerto 53 (DNS) si fuera necesario:
```bash
echo -e "[Resolve]\nDNSStubListener=no" | sudo tee /etc/systemd/resolved.conf
sudo systemctl restart systemd-resolved
```
Despliega Pi-hole:
```bash
cd ~/stacks/pi-hole
cat > docker-compose.yml << 'YAML'
services:
  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    restart: unless-stopped
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "8089:80"   # panel en 8089 para no chocar con proxy
    environment:
      TZ: "Europe/Madrid"
      WEBPASSWORD: "cambia-esta-contraseña"
    volumes:
      - ./etc-pihole:/etc/pihole
      - ./etc-dnsmasq.d:/etc/dnsmasq.d
    cap_add:
      - NET_ADMIN
YAML

docker compose up -d
```
Configura tu router para que entregue como DNS la IP del servidor.

7) (Opcional) Acceso remoto seguro con Tailscale (más fácil que WireGuard)
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --ssh
```
- Inicia sesión en Tailscale y tendrás VPN + SSH seguro sin abrir puertos.

8) Despliega tu primer servicio (ejemplo: Jellyfin)
```bash
cd ~/stacks/jellyfin
mkdir -p ./config /srv/media/{peliculas,series,musica}
cat > docker-compose.yml << 'YAML'
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    restart: unless-stopped
    user: "$(id -u):$(id -g)"
    volumes:
      - ./config:/config
      - /srv/media:/media:ro
    ports:
      - "8096:8096"
YAML

docker compose up -d
```
Accede a http://IP:8096. Si tienes dominio, publícalo detrás de NPM con HTTPS.

9) Backups básicos
- Copias de `~/stacks/*/` y datos en `/srv/*` con `rsync`, `restic` o `borg` a otro disco/NAS.
- Monitoriza discos con SMART: `sudo apt -y install smartmontools` y configura `smartd`.

---

## Ejemplos por nivel

### Nivel Básico

1) NAS / Backup
- Opción A (dedicado): Instala OpenMediaVault (OMV) o TrueNAS SCALE como sistema principal. Sigue su asistente para crear pools/comparticiones (SMB/NFS) y snapshots.
- Opción B (en Ubuntu actual): Comparte carpetas con Samba en Docker.
```bash
mkdir -p ~/stacks/samba /srv/datos
cd ~/stacks/samba
cat > docker-compose.yml << 'YAML'
services:
  samba:
    image: dperson/samba
    restart: unless-stopped
    ports:
      - "139:139"
      - "445:445"
    environment:
      USERID: "$(id -u)"
      GROUPID: "$(id -g)"
      TZ: "Europe/Madrid"
    command: >-
      -s "Datos;/srv/datos;yes;no;no;all;$(id -un)"
      -u "$(id -un);password"
    volumes:
      - /srv/datos:/srv/datos
YAML

docker compose up -d
```
- En Windows/macOS/Linux mapea `\\IP\Datos` o `smb://IP/Datos`.

Copias de seguridad con Restic (ejemplo a un disco USB montado en `/mnt/usb`):
```bash
sudo apt -y install restic
export RESTIC_PASSWORD="pon-una-contraseña"
restic -r /mnt/usb/backup init
restic -r /mnt/usb/backup backup /srv /home/$USER/stacks
```

2) Nube personal (Nextcloud)
```bash
mkdir -p ~/stacks/nextcloud/{db,app,redis}
cd ~/stacks/nextcloud
cat > docker-compose.yml << 'YAML'
services:
  db:
    image: mariadb:11
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: cambia_root
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_PASSWORD: cambia_db
    volumes:
      - ./db:/var/lib/mysql

  redis:
    image: redis:7
    restart: unless-stopped

  app:
    image: nextcloud:28
    restart: unless-stopped
    depends_on:
      - db
      - redis
    environment:
      MYSQL_HOST: db
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_PASSWORD: cambia_db
      REDIS_HOST: redis
    ports:
      - "8080:80"
    volumes:
      - ./app:/var/www/html
      - /srv/datos:/data
YAML

docker compose up -d
```
- Inicializa en http://IP:8080, crea admin y en Ajustes apunta el directorio de datos a `/data`.
- Publica detrás de NPM para HTTPS y dominio.

3) Servidor multimedia (Jellyfin) – ya visto en Pasos rápidos
- Añade Sonarr/Radarr/Bazarr y un cliente BitTorrent si quieres automatizar.

4) Bloqueo de anuncios (Pi-hole o AdGuard Home)
- Pi-hole ya visto. Alternativa: AdGuard Home en `~/stacks/adguard` (puertos 53 y 3000 para setup inicial).

5) Acceso remoto seguro (WireGuard o Tailscale)
- Tailscale ya visto. WireGuard en contenedor (ejemplo rápido con `linuxserver/wireguard`).

6) Reverse proxy con HTTPS (NPM o Traefik)
- NPM ya visto. Alternativa Traefik con auto-Discovery y certificados.

---

### Nivel Intermedio

1) Home Assistant
```bash
mkdir -p ~/stacks/homeassistant
cd ~/stacks/homeassistant
cat > docker-compose.yml << 'YAML'
services:
  homeassistant:
    image: ghcr.io/home-assistant/home-assistant:stable
    container_name: homeassistant
    restart: unless-stopped
    network_mode: host
    privileged: true
    volumes:
      - ./config:/config
      - /run/dbus:/run/dbus:ro
YAML

docker compose up -d
```
Accede a http://IP:8123. Para Zigbee/Z-Wave, añade el dispositivo USB (p. ej. `/dev/ttyUSB0`) en `devices:` y pertenencia a grupos `dialout`/`plugdev`.

2) Desarrollo y CI (Gitea + Runner sencillo)
```bash
mkdir -p ~/stacks/gitea
cd ~/stacks/gitea
cat > docker-compose.yml << 'YAML'
services:
  gitea:
    image: gitea/gitea:1.22
    restart: unless-stopped
    ports:
      - "3000:3000"
      - "2222:22"
    volumes:
      - ./data:/data
    environment:
      USER_UID: "1000"
      USER_GID: "1000"
YAML

docker compose up -d
```
- Accede a http://IP:3000. Para CI ligera, usa Woodpecker o actúa con runners dedicados (fuera del alcance aquí).

3) Monitorización (Prometheus + node-exporter + Grafana)
```bash
mkdir -p ~/stacks/monitoring
cd ~/stacks/monitoring
cat > docker-compose.yml << 'YAML'
services:
  nodeexporter:
    image: prom/node-exporter
    restart: unless-stopped
    network_mode: host

  prometheus:
    image: prom/prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana:latest
    restart: unless-stopped
    ports:
      - "3001:3000"
    volumes:
      - ./grafana:/var/lib/grafana
YAML

cat > prometheus.yml << 'YAML'
scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
YAML

docker compose up -d
```
Abre http://IP:3001 para Grafana, añade Prometheus como datasource `http://prometheus:9090`.

4) Documentos y notas (Wiki.js)
```bash
mkdir -p ~/stacks/wikijs
cd ~/stacks/wikijs
cat > docker-compose.yml << 'YAML'
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: cambia_db
      POSTGRES_USER: wiki
      POSTGRES_DB: wiki
    volumes:
      - ./db:/var/lib/postgresql/data
  wiki:
    image: requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wiki
      DB_PASS: cambia_db
      DB_NAME: wiki
    ports:
      - "3002:3000"
    volumes:
      - ./data:/data
YAML

docker compose up -d
```

5) Digitaliza y ordena PDFs (Paperless-ngx)
```bash
mkdir -p ~/stacks/paperless/{data,media,export,consume}
cd ~/stacks/paperless
cat > docker-compose.yml << 'YAML'
services:
  broker:
    image: redis:7
    restart: unless-stopped
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: paperless
      POSTGRES_USER: paperless
      POSTGRES_PASSWORD: cambia_db
    volumes:
      - ./data:/var/lib/postgresql/data
  web:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
    depends_on:
      - db
      - broker
    environment:
      PAPERLESS_REDIS: redis://broker:6379
      PAPERLESS_DBHOST: db
      PAPERLESS_DBNAME: paperless
      PAPERLESS_DBUSER: paperless
      PAPERLESS_DBPASS: cambia_db
    ports:
      - "8010:8000"
    volumes:
      - ./data:/usr/src/paperless/data
      - ./media:/usr/src/paperless/media
      - ./export:/usr/src/paperless/export
      - ./consume:/usr/src/paperless/consume
YAML

docker compose up -d
```

6) Fotos (Immich)
```bash
mkdir -p ~/stacks/immich
cd ~/stacks/immich
cat > docker-compose.yml << 'YAML'
services:
  database:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: cambia_db
      POSTGRES_DB: immich
      POSTGRES_USER: immich
    volumes:
      - ./db:/var/lib/postgresql/data
  redis:
    image: redis:7
  server:
    image: ghcr.io/immich-app/immich-server:release
    depends_on: [database, redis]
    environment:
      DB_HOSTNAME: database
      DB_USERNAME: immich
      DB_PASSWORD: cambia_db
      DB_DATABASE_NAME: immich
    ports:
      - "2283:2283"
    volumes:
      - ./uploads:/usr/src/app/upload
  machine-learning:
    image: ghcr.io/immich-app/immich-machine-learning:release
    depends_on: [server]
YAML

docker compose up -d
```

7) Gestor de contraseñas (Vaultwarden)
```bash
mkdir -p ~/stacks/vaultwarden
cd ~/stacks/vaultwarden
cat > docker-compose.yml << 'YAML'
services:
  vaultwarden:
    image: vaultwarden/server:latest
    restart: unless-stopped
    ports:
      - "8081:80"
    volumes:
      - ./data:/data
YAML

docker compose up -d
```
Publica detrás de NPM para HTTPS. Activa 2FA en tu cuenta.

8) Impresión y escaneo (CUPS/SANE en el sistema)
```bash
sudo apt -y install cups sane-airscan avahi-daemon
sudo usermod -aG lpadmin "$USER"
sudo systemctl enable --now cups
```
- Panel en http://IP:631. Añade impresoras y comparte.

9) Servicios de red (Syslog/NTP/DHCP/DNS)
- Syslog: `rsyslog` en el host y configura clientes para enviar logs.
- NTP: `chrony` como servidor de hora.
- DHCP/DNS: `dnsmasq` o `kea` (requiere configuración cuidadosa para no chocar con Pi-hole).

---

### Nivel Avanzado

1) Virtualización/Homelab con Proxmox VE (sustituye a Ubuntu)
- Descarga ISO de Proxmox VE y crea USB booteable.
- Instala en el PC, asigna IP estática.
- Desde https://IP:8006 crea:
  - CT (LXC) ligeros para servicios (p. ej. Jellyfin, Nextcloud, etc.).
  - VMs si necesitas kernels completos o Windows.
- Configura backups programados (PBS opcional) y snapshots.

2) Router/Firewall con pfSense u OPNsense (requiere 2 NICs)
- Instala el sistema y define WAN/LAN.
- Cambia la LAN a tu rango (p. ej. 192.168.10.1/24), activa DHCP, DNS Resolver, WireGuard.
- Crea reglas, VLANs, port-forward de 80/443 al reverse proxy.

3) Cámaras/NVR (Frigate)
```bash
mkdir -p ~/stacks/frigate
cd ~/stacks/frigate
cat > docker-compose.yml << 'YAML'
services:
  frigate:
    image: ghcr.io/blakeblackshear/frigate:stable
    privileged: true
    shm_size: "512m"
    devices:
      - /dev/dri/renderD128 # si tienes iGPU Intel/AMD
    volumes:
      - ./config:/config
      - /etc/localtime:/etc/localtime:ro
      - ./media:/media
    ports:
      - "8971:8971" # UI
      - "8554:8554" # RTSP
      - "8555:8555/tcp"
      - "8555:8555/udp"
YAML

cat > config/config.yml << 'YAML'
detect:
  enabled: true
cameras: {}
YAML

docker compose up -d
```

4) Servidores de juegos (ejemplo: Minecraft)
```bash
mkdir -p ~/stacks/minecraft
cd ~/stacks/minecraft
cat > docker-compose.yml << 'YAML'
services:
  mc:
    image: itzg/minecraft-server
    restart: unless-stopped
    ports:
      - "25565:25565"
    environment:
      EULA: "TRUE"
      MEMORY: 2G
    volumes:
      - ./data:/data
YAML

docker compose up -d
```

5) Video/voz y chat (Jitsi o Matrix/Element)
- Jitsi: usa el despliegue con Docker (requiere abrir puertos 80/443/10000-20000 UDP, tuning de TURN/STUN).
- Matrix (Synapse) + Element: despliegue con Postgres y reverse proxy. Añade coturn para llamadas.

6) Email autogestionado (Mailcow/Mailu)
- Requisitos: dominio con DNS correcto (A/AAAA, MX, SPF, DKIM, DMARC), IP fija y reputación.
- Mailcow (resumen):
```bash
sudo apt -y install git
cd ~/stacks
git clone https://github.com/mailcow/mailcow-dockerized.git
cd mailcow-dockerized
./generate_config.sh   # introduce tu dominio (ej. mail.tudominio.com)
# revisa mailcow.conf
sudo sysctl -w vm.max_map_count=262144
sudo sysctl -w fs.file-max=262144
docker compose pull && docker compose up -d
```
- Verifica puertos (25, 465, 587, 993), certificados y registros DNS.

---

## Mantenimiento y buenas prácticas
- Actualiza contenedores con `docker compose pull && docker compose up -d` por stack.
- Registra variables sensibles en `.env` y no las subas a repos públicos.
- Separa volúmenes de datos del sistema (SSD para contenedores, HDD para datos).
- Implementa backups fuera del equipo (NAS, otro disco, nube cifrada).
- Monitoriza recursos: `docker stats`, Grafana, alertas SMART.
- Seguridad: usa VPN para administración, fail2ban, y mínimos puertos expuestos.

## Problemas frecuentes
- Puerto 53 ocupado: deshabilita DNSStubListener de systemd-resolved (visto en Pasos rápidos).
- Puertos 80/443 ocupados: detén servicios que los usen o cambia mapeos.
- Permisos en volúmenes: ejecuta contenedores con tu UID/GID y ajusta `chown`/`chmod` según necesites.
- Aceleración por hardware (transcodificación): monta `/dev/dri` y usa imágenes compatibles; configura Jellyfin para VAAPI/QuickSync.

---

¡Listo! Con esto puedes empezar sencillo e ir escalando hasta un homelab completo.