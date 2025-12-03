# Mantenimiento y seguridad del servidor casero

Esta guía complementa la "Guía paso a paso" con prácticas de mantenimiento, seguridad y respuesta ante incidentes. Está pensada para Ubuntu Server LTS usando Docker/Compose.

## Objetivos
- Mantener el servidor estable y actualizado.
- Reducir superficie de ataque y exposición.
- Garantizar backups verificados y recuperación rápida.
- Monitorizar salud y recibir alertas.

## 1. Actualizaciones y parches

### Sistema operativo
```bash
sudo apt update && sudo apt -y upgrade
sudo apt -y autoremove
```
- Programa mantenimiento: por ejemplo, cada semana. Usa `unattended-upgrades` para seguridad:
```bash
sudo apt -y install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

### Contenedores
Por cada stack:
```bash
cd ~/stacks/<stack>
docker compose pull && docker compose up -d
```
- Revisa cambios de breaking en las imágenes antes de actualizar.
- Mantén un archivo `CHANGELOG.md` por stack con notas de actualización.

## 2. Backups y recuperación

### Estrategia 3-2-1
- 3 copias de tus datos.
- 2 medios distintos (p. ej. SSD/HDD y NAS o nube).
- 1 fuera de casa (off-site) y cifrado.

### Herramientas recomendadas
- Restic (cifrado, incremental, múltiples backends).
- BorgBackup.
- Rsync para copias simples.

### Ejemplos con Restic
```bash
sudo apt -y install restic
export RESTIC_PASSWORD="cambia-esta-clave"
# Copia local a disco externo
restic -r /mnt/usb/backup init
restic -r /mnt/usb/backup backup /srv /home/$USER/stacks
# Copia remota (S3 compatible)
export AWS_ACCESS_KEY_ID=...; export AWS_SECRET_ACCESS_KEY=...
restic -r s3:https://s3.example.com/bucket backup /srv /home/$USER/stacks
```

### Verificación y pruebas de restauración
- Lista y verifica:
```bash
restic -r /mnt/usb/backup snapshots
restic -r /mnt/usb/backup check
```
- Prueba restaurar a un directorio temporal:
```bash
restic -r /mnt/usb/backup restore latest --target /tmp/restore-test
```

## 3. Seguridad de red y exposición

### Principios
- Menos puertos expuestos, mejor.
- Administra por VPN (Tailscale/WireGuard), no publiques paneles.
- Usa HTTPS con Let’s Encrypt y reverse proxy.

### Firewall (ufw)
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH
sudo ufw allow 80,443/tcp   # si publicas web
sudo ufw status verbose
```

### SSH seguro
```bash
sudo nano /etc/ssh/sshd_config
# Ajusta:
PasswordAuthentication no
PermitRootLogin no
ClientAliveInterval 60
ClientAliveCountMax 3
```
Reinicia SSH:
```bash
sudo systemctl restart ssh
```
- Usa claves, y opcionalmente `tailscale ssh`.

### VPN
- Tailscale: fácil y segura, evita abrir puertos.
- WireGuard: manual, pero eficiente. Publica solo puerto UDP necesario.

## 4. Gestión de secretos y configuración

- Crea `.env` por stack y no lo subas a repos públicos.
- Usa `docker secret` o herramientas como `doppler`, `vault`, `sops` si necesitas rotación y auditoría.
- Copia de seguridad de archivos de configuración (`docker-compose.yml`, `prometheus.yml`, etc.).

## 5. Hardening del host

- Desinstala servicios innecesarios (`apt purge` lo que no uses).
- Activa `fail2ban` para proteger servicios expuestos.
```bash
sudo apt -y install fail2ban
sudo systemctl enable --now fail2ban
```
- Kernel y sysctl: limita redes si es router, usa `sysctl.d` para ajustes (avanzado).
- Montajes: separa datos en `/srv`, usa permisos mínimos (`chmod`, `chown`).
- Aislamiento: prefiere contenedores a procesos en host, usa `user: UID:GID` en compose.

## 6. Monitorización y alertas

- SMART discos:
```bash
sudo apt -y install smartmontools
sudo systemctl enable --now smartd
sudo smartctl -a /dev/sdX
```
- Recursos contenedores: `docker stats`.
- Stack de observabilidad: Prometheus + Grafana + alerta por email/Telegram.
- Logs centralizados: Loki/Promtail o `rsyslog`.

## 7. Mantenimiento programado

- Tareas cron:
```bash
crontab -e
# Ejemplo: backup diario a las 02:00
0 2 * * * RESTIC_PASSWORD=... restic -r /mnt/usb/backup backup /srv /home/$USER/stacks >> /var/log/restic.log 2>&1
```
- Limpieza de Docker:
```bash
docker system prune -f
```
- Revisión mensual: actualizaciones mayores, espacio en disco (`df -h`), integridad de backups.

## 8. Respuesta ante incidentes

- Ten un plan: identifica, aísla, erradica, recupera.
- Corta exposición: cierra puertos en el router y `ufw`.
- Revisa logs: `/var/log/auth.log`, contenedores (`docker logs`).
- Cambia contraseñas y rota claves si sospechas compromiso.
- Restaura desde backups verificados.

## 9. Buenas prácticas adicionales

- Documenta tus cambios (repositorio privado o wiki interna).
- Usa etiquetas y versiones en imágenes (`image: app:version`), evita `latest` en producción.
- Prueba en un entorno de staging (otra máquina/VM) antes de aplicar cambios críticos.
- UPS/SAI y apagado limpio con NUT si operas 24/7.

## 10. Checklist rápido

- [ ] Sistema y contenedores actualizados.
- [ ] Backups automáticos y verificados.
- [ ] Paneles solo accesibles por VPN.
- [ ] Firewall con reglas mínimas.
- [ ] SSH con claves y sin root.
- [ ] Monitorización activa y alertas.
- [ ] Secretos fuera del repositorio.
- [ ] Pruebas de restauración recientes.

---

Recomendación: Vincula este tutorial desde el índice:
- [Índice](./indice-servidor-casero.md)
- [Guía principal](./guia-servidor-casero.md)