# 🔐 Guía Completa: DNS, Proxies, VPN, Tor, HTTPS y Privacidad en Redes

Guía práctica por niveles —básico, intermedio y avanzado— sobre privacidad y seguridad en redes. Aprende qué protege cada tecnología, qué no protege, cuándo usarla y qué mitos existen sobre el anonimato en Internet.

---

## 📋 Tabla de Contenidos

### Nivel Básico
1. [¿Qué es la privacidad en Internet?](#1-qué-es-la-privacidad-en-internet)
2. [Cómo funciona una conexión a Internet](#2-cómo-funciona-una-conexión-a-internet)
3. [DNS: El directorio de Internet](#3-dns-el-directorio-de-internet)
4. [HTTPS: Cifrado en la web](#4-https-cifrado-en-la-web)
5. [Mitos comunes sobre privacidad](#5-mitos-comunes-sobre-privacidad)

### Nivel Intermedio
6. [Proxies: tipos, usos y limitaciones](#6-proxies-tipos-usos-y-limitaciones)
7. [VPN: qué protege y qué no](#7-vpn-qué-protege-y-qué-no)
8. [DNS seguro: DoH, DoT y DNSSEC](#8-dns-seguro-doh-dot-y-dnssec)
9. [Fingerprinting y rastreo avanzado](#9-fingerprinting-y-rastreo-avanzado)
10. [Comparativa de tecnologías de privacidad](#10-comparativa-de-tecnologías-de-privacidad)

### Nivel Avanzado
11. [Tor: anonimato real y sus límites](#11-tor-anonimato-real-y-sus-límites)
12. [I2P y redes alternativas](#12-i2p-y-redes-alternativas)
13. [Modelo de amenazas (Threat Modeling)](#13-modelo-de-amenazas-threat-modeling)
14. [Configuraciones avanzadas de privacidad](#14-configuraciones-avanzadas-de-privacidad)
15. [Ataques reales y contramedidas](#15-ataques-reales-y-contramedidas)

### Recursos Finales
16. [Ruta de aprendizaje](#16-ruta-de-aprendizaje)
17. [Ejercicios prácticos](#17-ejercicios-prácticos)

---

## NIVEL BÁSICO

---

## 1. ¿Qué es la privacidad en Internet?

### Privacidad vs. Anonimato vs. Seguridad

Antes de hablar de herramientas, es fundamental distinguir tres conceptos que frecuentemente se confunden:

| Concepto | Definición | Ejemplo |
|----------|-----------|---------|
| **Privacidad** | Controlar quién ve tu información | Que tu ISP no sepa qué lees |
| **Anonimato** | Que nadie pueda relacionar tus acciones contigo | Publicar sin revelar tu identidad |
| **Seguridad** | Proteger datos e infraestructura de ataques | Que nadie intercepte tu contraseña |

> ⚠️ **Punto clave:** Puedes tener seguridad sin privacidad (HTTPS cifra tu tráfico, pero Google sigue sabiendo qué buscas), privacidad sin anonimato (nadie ve tu tráfico, pero tu cuenta de Gmail te identifica), o ninguna de las dos.

### ¿Quién puede ver tu tráfico?

Cuando navegas por Internet, múltiples actores pueden observar tu actividad:

```
Tu dispositivo
    │
    ▼
Tu router doméstico     ← Puede ver: todos tus dominios consultados
    │
    ▼
Tu ISP (proveedor)      ← Puede ver: IP destino, volumen, frecuencia
    │
    ▼
Routers intermedios     ← Pueden ver: IP origen y destino
    │
    ▼
Servidor de destino     ← Puede ver: todo el contenido de tu petición
    │
    ▼
Terceros (CDN, DNS)     ← Pueden ver: metadatos de la conexión
```

### El modelo OSI y la privacidad

Diferentes herramientas protegen diferentes capas del modelo de red:

```
Capa 7 - Aplicación  → HTTPS protege el contenido
Capa 4 - Transporte  → TLS cifra el transporte
Capa 3 - Red         → VPN oculta las IPs
Capa 2 - Enlace      → Wi-Fi cifrado protege el medio local
```

---

## 2. Cómo funciona una conexión a Internet

### El viaje de un paquete

Cuando escribes `https://ejemplo.com` en tu navegador, ocurre lo siguiente:

```
1. Tu PC pregunta al DNS: "¿cuál es la IP de ejemplo.com?"
   PC → DNS: "¿ejemplo.com?" → DNS responde: "93.184.216.34"

2. Tu PC abre una conexión TCP con esa IP
   PC → 93.184.216.34:443 (SYN)
   93.184.216.34 → PC (SYN-ACK)
   PC → 93.184.216.34 (ACK)

3. Se negocia TLS (certificado, cifrado)
   PC ↔ Servidor: Handshake TLS

4. Se envía la petición HTTP cifrada
   PC → Servidor: "GET / HTTP/1.1"

5. El servidor responde con el contenido
   Servidor → PC: HTML, CSS, JS, imágenes
```

### Metadatos: lo que siempre se ve

Aunque uses HTTPS, ciertos **metadatos** siempre son visibles para tu ISP y routers intermedios:

- **IP de destino** (a qué servidor te conectas)
- **Puerto** (443 indica HTTPS)
- **Tamaño y frecuencia** de los paquetes
- **SNI** (*Server Name Indication*): el dominio exacto, visible en texto plano en TLS 1.2 y en TLS 1.3 sin ECH; cifrado con TLS 1.3 + ECH

> 💡 **Ejemplo real:** Tu ISP no sabe qué artículo lees en Wikipedia, pero sí sabe que te conectas a `en.wikipedia.org` a las 3am durante 45 minutos.

### ¿Qué es una dirección IP y por qué importa?

Tu dirección IP pública es asignada por tu ISP y es como tu "dirección postal" en Internet:

```bash
# Ver tu IP pública desde terminal
curl https://api.ipify.org

# Ver información de geolocalización de una IP
curl https://ipapi.co/json/
```

La IP revela aproximadamente:
- Tu país y ciudad (con margen de error)
- Tu ISP
- Si usas un datacenter o conexión residencial

---

## 3. DNS: El directorio de Internet

### ¿Qué es DNS?

El Sistema de Nombres de Dominio (DNS) traduce nombres legibles como `google.com` a direcciones IP como `142.250.185.46`. Es como la agenda telefónica de Internet.

```
Usuario escribe: www.google.com
         │
         ▼
Resolver DNS local (caché del SO)
         │ (si no está en caché)
         ▼
Resolver del ISP o configurado (ej: 8.8.8.8)
         │
         ▼
Servidores raíz (.)
         │
         ▼
Servidor TLD (.com)
         │
         ▼
Servidor autoritativo (google.com)
         │
         ▼
Respuesta: 142.250.185.46
```

### Tipos de registros DNS

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **A** | IPv4 del dominio | `ejemplo.com → 93.184.216.34` |
| **AAAA** | IPv6 del dominio | `ejemplo.com → 2606:2800::1` |
| **CNAME** | Alias a otro dominio | `www → ejemplo.com` |
| **MX** | Servidor de correo | `mail.ejemplo.com` |
| **TXT** | Texto arbitrario | SPF, DKIM, verificaciones |
| **NS** | Servidores de nombres | `ns1.ejemplo.com` |
| **PTR** | Inverso: IP → nombre | `34.216.184.93.in-addr.arpa` |

### El problema de privacidad del DNS

Por defecto, las consultas DNS viajan **en texto plano** usando UDP en el puerto 53. Esto significa:

```
Tu PC ──── UDP/53 (¡texto plano!) ────► Servidor DNS
           "quiero la IP de netflix.com"
           ← "aquí tienes: 52.86.x.x"
```

**¿Quién ve esto?**
- Tu ISP: puede ver cada dominio que consultas
- Tu router: si está comprometido
- Cualquier atacante en tu red (ej: Wi-Fi público)

### Cómo cambiar tu servidor DNS

```bash
# Linux: editar /etc/resolv.conf
echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf

# macOS: desde Terminal
networksetup -setdnsservers Wi-Fi 1.1.1.1 8.8.8.8

# Windows PowerShell
Set-DnsClientServerAddress -InterfaceAlias "Wi-Fi" -ServerAddresses "1.1.1.1","8.8.8.8"
```

### Servidores DNS populares

| Proveedor | DNS primario | DNS secundario | Características |
|-----------|-------------|----------------|-----------------|
| **Cloudflare** | 1.1.1.1 | 1.0.0.1 | Rápido, privacidad prometida |
| **Google** | 8.8.8.8 | 8.8.4.4 | Muy confiable, recopila datos |
| **Quad9** | 9.9.9.9 | 149.112.112.112 | Bloquea malware, sin logs |
| **OpenDNS** | 208.67.222.222 | 208.67.220.220 | Filtros familiares, Cisco |
| **AdGuard** | 94.140.14.14 | 94.140.15.15 | Bloquea anuncios y rastreadores |

> ⚠️ **Advertencia:** Cambiar tu DNS a 1.1.1.1 o 8.8.8.8 **no te hace anónimo**. Tu ISP sigue viendo a qué IPs te conectas.

---

## 4. HTTPS: Cifrado en la web

### ¿Qué es HTTPS?

HTTPS = HTTP + TLS (Transport Layer Security). El candado en tu navegador indica que la conexión entre tu dispositivo y el servidor está **cifrada e integridad verificada**.

```
HTTP  (sin cifrar):
PC ──── "GET /mi-contraseña HTTP/1.1" ────► Servidor
         ↑ cualquiera puede leer esto

HTTPS (cifrado con TLS):
PC ──── "xK9#mP2@..." ──────────────────► Servidor
         ↑ solo PC y Servidor entienden esto
```

### Qué protege HTTPS

✅ **Sí protege:**
- El contenido de tus peticiones (formularios, contraseñas, datos)
- El contenido de las respuestas (páginas web, archivos)
- Integridad: nadie puede modificar el contenido en tránsito

❌ **No protege:**
- Que te estás conectando a ese dominio (el ISP lo ve)
- Con qué frecuencia y cuánto tiempo navegas
- Tu dirección IP y la del servidor
- Metadatos de conexión

### El handshake TLS explicado

```
Cliente                              Servidor
  │                                     │
  │──── ClientHello (versiones TLS, ────►│
  │      cipher suites soportados)       │
  │                                     │
  │◄─── ServerHello (versión elegida, ──│
  │      certificado, clave pública)     │
  │                                     │
  │  [Cliente verifica certificado      │
  │   contra CA de confianza]           │
  │                                     │
  │──── ClientKeyExchange ─────────────►│
  │     (clave de sesión cifrada        │
  │      con clave pública del servidor)│
  │                                     │
  │◄══════ Comunicación cifrada ═══════►│
```

### Certificados SSL/TLS

Los certificados son emitidos por **Autoridades Certificadoras (CA)**:

```
Root CA (DigiCert, Let's Encrypt, etc.)
    │
    └─► Intermediate CA
              │
              └─► Tu certificado (ejemplo.com)
```

**Let's Encrypt** ha democratizado HTTPS ofreciendo certificados gratuitos:

```bash
# Obtener certificado con Certbot
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d tudominio.com -d www.tudominio.com

# Renovación automática
sudo certbot renew --dry-run
```

### Verificar la seguridad de HTTPS

```bash
# Comprobar certificado desde terminal
openssl s_client -connect ejemplo.com:443 -servername ejemplo.com

# Ver detalles del certificado
echo | openssl s_client -connect ejemplo.com:443 2>/dev/null | \
  openssl x509 -noout -dates -subject -issuer

# Escaneo con nmap
nmap --script ssl-enum-ciphers -p 443 ejemplo.com
```

---

## 5. Mitos comunes sobre privacidad

### ❌ Mito 1: "El modo incógnito me hace anónimo"

**Realidad:** El modo incógnito solo evita que tu navegador guarde historial, cookies y caché **en tu dispositivo**. No oculta nada a:
- Tu ISP
- El sitio web que visitas
- Tu red corporativa o escolar
- Tu router doméstico

```
Modo incógnito protege: historial local del navegador
Modo incógnito NO protege: nada en la red
```

### ❌ Mito 2: "Con VPN soy completamente anónimo"

**Realidad:** Una VPN desplaza la confianza de tu ISP al proveedor de VPN. Si la VPN lleva logs o es comprometida, expone tu actividad. Además:
- El sitio web que visitas ve la IP de la VPN
- La VPN ve todo tu tráfico
- Si la VPN tiene fugas de DNS o IP real, no sirve de nada

### ❌ Mito 3: "HTTPS protege todo mi tráfico"

**Realidad:** HTTPS cifra el contenido, pero los metadatos (a qué te conectas, cuándo, cuánto) son visibles. Además, los sitios web pueden rastrearte con cookies y fingerprinting incluso con HTTPS.

### ❌ Mito 4: "Tor me hace completamente invisible"

**Realidad:** Tor proporciona anonimato muy fuerte, pero tiene límites:
- Si inicias sesión en tu cuenta de Gmail por Tor, Google te identifica
- El nodo de salida de Tor puede ver tu tráfico si no usas HTTPS
- Los patrones de tráfico pueden revelar información
- El tiempo y correlación de tráfico pueden desanonimizarte

### ❌ Mito 5: "Si no tengo nada que ocultar, no necesito privacidad"

**Realidad:** La privacidad no es sobre ocultar crímenes; es sobre control sobre tu información personal. Datos expuestos permiten:
- Discriminación de precios (te cobran más si detectan tu dispositivo caro)
- Manipulación mediante publicidad hiperpersonalizada
- Exposición de información sensible en contextos incorrectos
- Riesgo ante cambios legales o políticos futuros

---

## NIVEL INTERMEDIO

---

## 6. Proxies: tipos, usos y limitaciones

### ¿Qué es un proxy?

Un proxy es un intermediario entre tu dispositivo y el servidor destino. Tu tráfico pasa por el proxy, que hace la petición en tu nombre.

```
Sin proxy:
Tu PC ──────────────────────────────► Servidor destino
(IP: 192.168.1.10)                    (ve tu IP real)

Con proxy:
Tu PC ──► Proxy ───────────────────► Servidor destino
(IP: 192.168.1.10)  (IP: 45.67.89.1)  (ve IP del proxy)
```

### Tipos de proxies

#### 1. Proxy HTTP/HTTPS

Solo para tráfico web. El más básico y común:

```bash
# Usar proxy HTTP en curl
curl -x http://proxy.ejemplo.com:8080 https://google.com

# Configurar en Firefox
# about:preferences → Network Settings → Manual proxy

# Variables de entorno en Linux
export http_proxy="http://usuario:contraseña@proxy:8080"
export https_proxy="http://usuario:contraseña@proxy:8080"
```

#### 2. Proxy SOCKS5

Más flexible: funciona con cualquier protocolo TCP/UDP:

```bash
# SOCKS5 con curl
curl --socks5 127.0.0.1:1080 https://ejemplo.com

# SOCKS5 con autenticación
curl --socks5 usuario:contraseña@proxy.host:1080 https://ejemplo.com

# SSH como proxy SOCKS5 (muy útil)
ssh -D 1080 -N usuario@servidor-remoto.com
# Ahora configura tu navegador con SOCKS5 en 127.0.0.1:1080
```

#### 3. Proxy transparente

Intercepta el tráfico sin que el cliente lo configure. Usado por ISPs, escuelas y empresas:

```
Tu PC ──► Router ──► [Proxy transparente oculto] ──► Internet
          (redirige todo el tráfico automáticamente)
```

#### 4. Proxy inverso (Reverse Proxy)

Protege servidores, no clientes. Nginx y Cloudflare son ejemplos:

```
Clientes ──► Cloudflare (proxy inverso) ──► Tu servidor real
              (oculta IP real del servidor,
               protege contra DDoS, cachea)
```

#### 5. Proxies de élite vs. anónimos vs. transparentes

| Tipo | El servidor ve | Uso |
|------|---------------|-----|
| **Transparente** | Tu IP real + que usas proxy | Filtrado de contenido |
| **Anónimo** | IP del proxy + que usas proxy | Privacidad básica |
| **Élite/Alta anonimidad** | Solo IP del proxy | Privacidad mejorada |

### Limitaciones de los proxies

```
Problema 1: Sin cifrado en proxies HTTP
Tu PC ──►  Proxy HTTP  ──► Servidor
           (ve TODO tu tráfico en texto plano)

Problema 2: Solo redirigen una aplicación
El proxy de tu navegador no afecta a otras apps

Problema 3: Confianza en el proveedor
El proxy puede registrar y vender tus datos

Problema 4: Proxies gratuitos = producto eres tú
```

### Proxy vs. VPN: diferencias clave

| Característica | Proxy | VPN |
|----------------|-------|-----|
| Cifrado | Generalmente no | Sí |
| Alcance | Por aplicación | Todo el sistema |
| Protocolos | HTTP/SOCKS | Todos (IP) |
| Velocidad | Más rápido | Más lento |
| Costo | Frecuentemente gratis | Generalmente de pago |
| Confianza requerida | Alta | Alta |

---

## 7. VPN: qué protege y qué no

### ¿Qué es una VPN?

Una Red Privada Virtual (VPN) crea un **túnel cifrado** entre tu dispositivo y un servidor VPN. Todo tu tráfico de red pasa por ese túnel.

```
Sin VPN:
Tu PC (IP: 80.x.x.x) ──────────────────► google.com
    ISP ve: que te conectas a google.com

Con VPN:
Tu PC ══[TÚNEL CIFRADO]══► Servidor VPN ──────────► google.com
(IP: 80.x.x.x)            (IP: 45.y.y.y)
    ISP ve: solo tráfico cifrado hacia el servidor VPN
    Google ve: IP del servidor VPN, no la tuya
```

### Protocolos VPN

#### OpenVPN
El más probado y fiable:
```bash
# Conectar con archivo .ovpn
sudo openvpn --config mi-servidor.ovpn

# Con usuario y contraseña
sudo openvpn --config mi-servidor.ovpn --auth-user-pass credenciales.txt
```

#### WireGuard
Moderno, rápido, código más simple (auditado más fácilmente):
```bash
# Instalar WireGuard
sudo apt install wireguard

# Configuración básica (/etc/wireguard/wg0.conf)
[Interface]
PrivateKey = <tu-clave-privada>
Address = 10.0.0.2/24
DNS = 1.1.1.1

[Peer]
PublicKey = <clave-publica-servidor>
Endpoint = vpn.ejemplo.com:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25

# Activar
sudo wg-quick up wg0

# Desactivar
sudo wg-quick down wg0

# Ver estado
sudo wg show
```

#### IKEv2/IPsec
Nativo en móviles, muy estable para cambios de red:
```
Ventaja: se reconecta automáticamente al cambiar de Wi-Fi a 4G
Uso típico: dispositivos móviles corporativos
```

### Comparativa de protocolos VPN

| Protocolo | Velocidad | Seguridad | Compatibilidad | Uso recomendado |
|-----------|-----------|-----------|----------------|-----------------|
| **WireGuard** | ⚡⚡⚡ | ⭐⭐⭐⭐⭐ | Media | Uso general moderno |
| **OpenVPN** | ⚡⚡ | ⭐⭐⭐⭐⭐ | Alta | Máxima compatibilidad |
| **IKEv2** | ⚡⚡⚡ | ⭐⭐⭐⭐ | Alta (móviles) | Dispositivos móviles |
| **L2TP/IPsec** | ⚡⚡ | ⭐⭐⭐ | Muy alta | Legado, evitar |
| **PPTP** | ⚡⚡⚡⚡ | ⭐ | Universal | **No usar** (roto) |

### Qué protege una VPN

✅ **Protege:**
- Tu ISP no puede ver los dominios que visitas
- Tu ISP no puede ver el contenido de tu tráfico
- Los sitios web no ven tu IP real
- Protección en redes Wi-Fi públicas no confiables
- Eludir censura geográfica

❌ **No protege:**
- Los sitios web siguen pudiendo rastrearte con cookies y fingerprinting
- El proveedor de VPN ve todo tu tráfico
- Si estás logueado en Google/Facebook, te identifican aunque uses VPN
- No protege contra malware en tu dispositivo
- No te hace "completamente anónimo"

### Kill Switch: seguridad ante fallos de VPN

Si la VPN se desconecta sin Kill Switch, tu tráfico real se expone momentáneamente:

```bash
# Kill Switch con iptables (Linux)
# Bloquear todo el tráfico excepto por la VPN
sudo iptables -F
sudo iptables -P INPUT DROP
sudo iptables -P OUTPUT DROP
sudo iptables -P FORWARD DROP

# Permitir loopback
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT

# Permitir tráfico por la interfaz VPN (tun0)
sudo iptables -A INPUT -i tun0 -j ACCEPT
sudo iptables -A OUTPUT -o tun0 -j ACCEPT

# Permitir conectar al servidor VPN
sudo iptables -A OUTPUT -p udp --dport 1194 -j ACCEPT
sudo iptables -A INPUT -p udp --sport 1194 -j ACCEPT
```

### Fugas de DNS (DNS Leaks)

Uno de los problemas más comunes con VPNs: tu DNS sigue usando el servidor del ISP aunque el tráfico web vaya por la VPN.

```bash
# Comprobar si hay fuga de DNS
# 1. Conecta tu VPN
# 2. Visita https://dnsleaktest.com
# 3. Si aparece tu ISP real → hay una fuga de DNS

# Solución con systemd-resolved (Linux)
sudo nano /etc/systemd/resolved.conf
# Agregar:
# DNS=1.1.1.1
# FallbackDNS=8.8.8.8
# DNSOverTLS=yes

sudo systemctl restart systemd-resolved
```

### VPN gratuitas: ¿son seguras?

```
Proveedores de VPN gratuitas típicamente:
├── Venden tus datos de navegación a anunciantes
├── Tienen límites de ancho de banda
├── Pueden inyectar anuncios en tu tráfico
├── Tienen servidores lentos y saturados
└── Pueden ser honeypots gubernamentales

Excepciones respetables (con limitaciones):
├── ProtonVPN Free (velocidad limitada, sin logs verificado)
└── Windscribe Free (10GB/mes, política de privacidad transparente)
```

### Cómo montar tu propia VPN

Con WireGuard en un servidor VPS:

```bash
# En el servidor (Ubuntu)
sudo apt update && sudo apt install wireguard

# Generar claves del servidor
wg genkey | sudo tee /etc/wireguard/server_private.key
sudo cat /etc/wireguard/server_private.key | wg pubkey | \
  sudo tee /etc/wireguard/server_public.key

# Configuración del servidor (/etc/wireguard/wg0.conf)
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = <server_private_key>
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; \
         iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; \
           iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
PublicKey = <client_public_key>
AllowedIPs = 10.0.0.2/32

# Habilitar IP forwarding
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Iniciar VPN
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0
```

---

## 8. DNS seguro: DoH, DoT y DNSSEC

### El problema del DNS tradicional

DNS clásico usa UDP/53 sin cifrado. Soluciones modernas:

### DNS over HTTPS (DoH)

Encapsula las consultas DNS dentro de HTTPS:

```
DNS clásico:  PC ──UDP/53──► Servidor DNS  (¡visible para todos!)
DoH:          PC ══HTTPS══► Servidor DoH   (cifrado como web normal)
```

```bash
# Probar DoH con curl (Cloudflare)
curl -s -H "accept: application/dns-json" \
  "https://cloudflare-dns.com/dns-query?name=ejemplo.com&type=A" | \
  python3 -m json.tool

# Configurar DoH en Firefox
# about:config → network.trr.mode = 2
# about:config → network.trr.uri = https://cloudflare-dns.com/dns-query
```

### DNS over TLS (DoT)

Similar a DoH pero usa un puerto dedicado (853):

```bash
# Instalar y configurar stubby (cliente DoT)
sudo apt install stubby

# Configuración (/etc/stubby/stubby.yml)
resolution_type: GETDNS_RESOLUTION_STUB
dns_transport_list:
  - GETDNS_TRANSPORT_TLS
tls_authentication: GETDNS_AUTHENTICATION_REQUIRED
upstream_recursive_servers:
  - address_data: 1.1.1.1
    tls_auth_name: "cloudflare-dns.com"
  - address_data: 8.8.8.8
    tls_auth_name: "dns.google"

# Iniciar stubby
sudo systemctl start stubby
sudo systemctl enable stubby
```

### DNSSEC: integridad sin privacidad

DNSSEC firma criptográficamente las respuestas DNS para prevenir spoofing, pero **no cifra** las consultas:

```bash
# Verificar si un dominio tiene DNSSEC
dig +dnssec ejemplo.com

# Comprobar la firma
dig @8.8.8.8 +dnssec cloudflare.com | grep -E "RRSIG|AD"
# "AD" en las flags indica DNSSEC verificado
```

### Comparativa DNS seguro

| Tecnología | Privacidad | Integridad | Compatibilidad | Puerto |
|------------|-----------|------------|----------------|--------|
| DNS clásico | ❌ | ❌ | ✅ Universal | 53 |
| DNSSEC | ❌ (sin cifrado) | ✅ | ✅ Alta | 53 |
| DoT | ✅ | ✅ | ✅ Media | 853 |
| DoH | ✅ | ✅ | ✅ Alta | 443 |

### Encrypted Client Hello (ECH): la próxima frontera

TLS 1.3 con ECH oculta el SNI (el dominio al que te conectas), completando la privacidad a nivel de transporte:

```
Sin ECH:  [IP visible][SNI: secreto.com visible][datos cifrados]
Con ECH:  [IP visible][SNI: cifrado con clave pública][datos cifrados]
```

Actualmente en adopción gradual (soporte en Firefox y Cloudflare desde 2023).

---

## 9. Fingerprinting y rastreo avanzado

### ¿Qué es el browser fingerprinting?

Los sitios web pueden identificarte sin cookies usando características únicas de tu navegador:

```javascript
// Ejemplo de atributos usados para fingerprinting
const fingerprint = {
  userAgent: navigator.userAgent,
  language: navigator.language,
  screenResolution: `${screen.width}x${screen.height}`,
  timezone: Intl.DateTimeFormat().resolvedOptions().timeZone,
  plugins: Array.from(navigator.plugins).map(p => p.name),
  canvas: getCanvasFingerprint(),    // Rendering único por GPU/driver
  webGL: getWebGLInfo(),             // Información de GPU
  fonts: detectInstalledFonts(),     // Fuentes del sistema
  audioContext: getAudioFingerprint(), // Comportamiento único de audio
};
// Esta combinación identifica a ~1 en 286,777 usuarios
```

### Tipos de rastreo web

#### Cookies de terceros
```
Visitas amazon.com → Amazon pone cookie en tu navegador
Visitas noticias.com → Hay un anuncio de Amazon que lee tu cookie
Amazon sabe que visitas noticias.com aunque no tengas cuenta
```

#### Píxel de seguimiento
```html
<!-- Invisible 1x1 px en un email -->
<img src="https://rastreador.com/pixel.gif?uid=12345&evento=apertura" 
     width="1" height="1">
<!-- Al abrir el email, revelas: IP, hora, cliente de email -->
```

#### Supercookies (EverCookie)
Almacenan tu identidad en múltiples lugares simultáneamente:
- localStorage y sessionStorage
- IndexedDB
- Cache del navegador
- ETags HTTP
- HSTS headers
- WebSQL

### Contramedidas contra el fingerprinting

```
Nivel 1 - Básico:
├── Usar Firefox con uBlock Origin
├── Activar "Resist Fingerprinting" en Firefox (about:config)
│   privacy.resistFingerprinting = true
└── Usar modo privado para sesiones sensibles

Nivel 2 - Intermedio:
├── Extensión CanvasBlocker
├── Configurar "Strict" en Firefox Enhanced Tracking Protection
├── Desactivar JavaScript (rompe muchos sitios)
└── Usar Privacy Badger + uBlock Origin

Nivel 3 - Avanzado:
├── Usar Tor Browser (todos los usuarios parecen idénticos)
├── Máquina virtual dedicada para actividades sensibles
└── Tails OS para máxima protección
```

### Herramientas para comprobar tu fingerprint

```bash
# Visita estos sitios para ver cómo te identifican
# https://coveryourtracks.eff.org/  (EFF)
# https://browserleaks.com/
# https://amiunique.org/
# https://fingerprintjs.github.io/fingerprintjs/
```

---

## 10. Comparativa de tecnologías de privacidad

### Tabla de protección por capa

| Tecnología | Oculta IP | DNS privado | Cifra tráfico | Anonimato real | Velocidad |
|------------|-----------|-------------|---------------|----------------|-----------|
| **Solo HTTPS** | ❌ | ❌ | Contenido ✅ | ❌ | ⚡⚡⚡⚡⚡ |
| **DNS seguro (DoH)** | ❌ | ✅ | Solo DNS ✅ | ❌ | ⚡⚡⚡⚡⚡ |
| **Proxy HTTP** | ✅ | ❌ | ❌ | ❌ | ⚡⚡⚡⚡ |
| **Proxy SOCKS5** | ✅ | Variable | ❌ | ❌ | ⚡⚡⚡⚡ |
| **VPN** | ✅ | ✅ (si bien conf.) | ✅ | Parcial ⚠️ | ⚡⚡⚡ |
| **Tor** | ✅✅ | ✅ | ✅ | Alto ✅ | ⚡ |
| **Tor + VPN** | ✅✅ | ✅ | ✅ | Alto ✅ | ⚡ |

### ¿Cuándo usar cada tecnología?

```
Escenario: Navegar en Wi-Fi público (cafetería, aeropuerto)
→ VPN es suficiente. Protege contra ataques locales.

Escenario: Eludir bloqueo geográfico (Netflix, Spotify)
→ VPN de pago es suficiente. No necesitas anonimato.

Escenario: Evitar rastreo de tu ISP
→ VPN + DoH. El ISP solo ve tráfico cifrado hacia la VPN.

Escenario: Periodista en país con censura
→ Tor Browser, posiblemente con puentes. Necesitas anonimato real.

Escenario: Informante/whistleblower
→ Tails OS + Tor + SecureDrop. El modelo de amenaza es severo.

Escenario: Descargar torrents sin que tu ISP lo sepa
→ VPN (verifica política "no logs" y si acepta torrents).

Escenario: Uso cotidiano de privacidad
→ Firefox + uBlock Origin + DoH. Suficiente para la mayoría.
```

---

## NIVEL AVANZADO

---

## 11. Tor: anonimato real y sus límites

### ¿Cómo funciona Tor?

Tor (The Onion Router) encamina tu tráfico a través de **tres nodos cifrados** seleccionados aleatoriamente:

```
Tu PC
  │
  ├─► [Cifrado con clave del nodo de salida]
  ├─► [Cifrado con clave del nodo intermedio]
  └─► [Cifrado con clave del nodo de entrada]
       │
       ▼
Nodo de Entrada (Guard) → solo sabe tu IP real
       │ (descifra primera capa)
       ▼
Nodo Intermedio (Middle) → solo sabe que viene del Guard y va al Exit
       │ (descifra segunda capa)
       ▼
Nodo de Salida (Exit) → solo sabe el destino final
       │ (descifra tercera capa)
       ▼
   Servidor destino (no sabe quién eres, ve IP del Exit)
```

Este sistema de "cebolla" (capas de cifrado) garantiza que ningún nodo individual conoce tanto el origen como el destino.

### Instalar y usar Tor

```bash
# Instalar Tor en Ubuntu/Debian
sudo apt install tor

# Iniciar Tor
sudo systemctl start tor
sudo systemctl enable tor

# Usar Tor como proxy SOCKS5 (puerto por defecto: 9050)
curl --socks5-hostname 127.0.0.1:9050 https://check.torproject.org/

# Verificar que usas Tor
curl --socks5-hostname 127.0.0.1:9050 https://api.ipify.org
# La IP devuelta debe ser un nodo de salida de Tor
```

### Tor Browser

La forma recomendada para uso cotidiano:

```bash
# Descargar Tor Browser desde https://www.torproject.org/
# En Linux:
tar -xf tor-browser-linux64-*.tar.xz
cd tor-browser_es-ES
./start-tor-browser.desktop
```

**Tor Browser modifica:**
- Tamaño de ventana estandarizado (evita fingerprinting por resolución)
- User-Agent genérico
- Timezone configurada a UTC
- Fuentes del sistema no detectables
- Canvas fingerprinting bloqueado
- Cookies aisladas por sitio

### Niveles de seguridad en Tor Browser

| Nivel | JavaScript | Otros ajustes | Uso recomendado |
|-------|-----------|---------------|-----------------|
| **Standard** | Habilitado | Por defecto | Uso general |
| **Safer** | En HTTP deshabilitado | Algunos medias bloqueados | Navegación sensible |
| **Safest** | Totalmente deshabilitado | Máxima restricción | Altísimo riesgo |

### Servicios Onion (.onion)

Los servicios onion son servidores dentro de la red Tor. Ofrecen anonimato bidireccional:

```
Cliente (Tor) ──► [circuito Tor] ──► Servicio Onion
                                      (servidor también en Tor)
Nadie conoce:
  - IP del cliente
  - IP del servidor
```

Ejemplos de servicios .onion legítimos:
```
# DuckDuckGo
https://duckduckgogg42xjoc72x3sjasowoarfbgcmvfimaftt6twagswzczad.onion

# The New York Times
https://www.nytimesn7cgmftshazwhfgzm37qxb44r64ytbb2dj3x62d2lljsciiyd.onion

# ProtonMail
https://protonmailrmez3lotccipshtkleegetolb73fuirgj7r4o4vfu7ozyd.onion
```

### Montar un servicio onion propio

```bash
# En /etc/tor/torrc, agregar:
HiddenServiceDir /var/lib/tor/hidden_service/
HiddenServicePort 80 127.0.0.1:8080

# Reiniciar Tor
sudo systemctl restart tor

# Ver tu dirección .onion
sudo cat /var/lib/tor/hidden_service/hostname
# Output: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.onion
```

### Limitaciones y ataques conocidos contra Tor

#### Ataque de correlación de tráfico
Si un adversario controla tanto el ISP del usuario como el nodo de salida, puede correlacionar tiempos y volúmenes de tráfico:

```
Agencia X controla:
├── ISP del usuario (ve cuándo envía paquetes)
└── Nodo de salida (ve cuándo recibe paquetes)
→ Puede correlacionar: "mismo tamaño, mismo tiempo → mismo usuario"
```

**Contramedida:** Añadir latencia artificial (Tor lo hace parcialmente con padding).

#### Ataques al nodo de salida
El nodo de salida ve el tráfico sin cifrar (si no usas HTTPS):

```
Usuario ══[Tor cifrado]══► Nodo de Salida ──[¡texto plano!]──► destino.com
                           ↑ puede ver y modificar el tráfico
```

**Regla de oro:** Siempre usa HTTPS dentro de Tor.

#### Deanonimización por comportamiento
```
Errores comunes que rompen el anonimato:
├── Iniciar sesión en tu cuenta real (Gmail, Facebook)
├── Usar el mismo pseudónimo que en otras plataformas
├── Compartir información personal identificable
├── Abrir archivos descargados por Tor fuera de Tor
└── Usar JavaScript en sitios maliciosos
```

### Tor vs. VPN vs. Tor+VPN

```
VPN antes de Tor (VPN → Tor):
+ Tu ISP no ve que usas Tor
+ El nodo de entrada no ve tu IP real (ve la VPN)
- La VPN sabe que usas Tor
- Si la VPN es comprometida, tu uso de Tor se expone

Tor antes de VPN (Tor → VPN):
+ El nodo de salida no puede ver tu tráfico (la VPN cifra desde el exit)
- La VPN sabe tu IP de salida de Tor
- Generalmente no recomendado

Sin VPN, solo Tor:
+ Más simple, menos puntos de fallo
+ Adecuado para la mayoría de casos de uso de Tor
- Tu ISP sabe que usas Tor
```

---

## 12. I2P y redes alternativas

### I2P: Internet dentro de Internet

I2P (Invisible Internet Project) es una red alternativa diseñada para comunicaciones anónimas dentro de la propia red I2P:

```
Diferencias con Tor:
├── Tor: acceso anónimo a Internet normal
├── I2P: red separada, principalmente para servicios internos (eepsites)
├── Tor: circuitos unidireccionales
└── I2P: túneles bidireccionales, más descentralizado

Casos de uso de I2P:
├── Correo cifrado anónimo (I2P-Bote)
├── Mensajería instantánea anónima
├── Torrents anónimos (I2PSnark)
└── Sitios web internos (eepsites, dominios .i2p)
```

```bash
# Instalar I2P en Ubuntu
sudo apt-add-repository ppa:i2p-maintainers/i2p
sudo apt update
sudo apt install i2p

# Iniciar I2P
i2prouter start

# Consola de administración en http://127.0.0.1:7657
```

### Comparativa Tor vs. I2P vs. Freenet

| Característica | Tor | I2P | Freenet |
|----------------|-----|-----|---------|
| **Acceso a Internet** | ✅ | ❌ (principalmente) | ❌ |
| **Anonimato** | Alto | Alto | Alto |
| **Velocidad** | Lenta | Media | Muy lenta |
| **Facilidad de uso** | Media | Difícil | Media |
| **Madurez** | Alta | Media | Alta |
| **Caso de uso** | Navegación anónima | Red privada interna | Almacenamiento censurado |

---

## 13. Modelo de amenazas (Threat Modeling)

### ¿Por qué necesitas un modelo de amenazas?

No existe "privacidad perfecta". Cada medida tiene coste (velocidad, usabilidad, dinero). Debes adaptar tu protección a **tus amenazas reales**:

```
Preguntas clave del threat modeling:
1. ¿Qué quiero proteger? (activos)
2. ¿De quién me quiero proteger? (adversarios)
3. ¿Qué tan probable es que necesite protegerme?
4. ¿Qué consecuencias tendría si fallo?
5. ¿Cuánto esfuerzo estoy dispuesto a invertir?
```

### Perfiles de amenaza comunes

#### Perfil 1: Usuario doméstico
```
Activos: historial de navegación, datos personales
Adversarios: anunciantes, empresas de datos, ISP
Probabilidad: Alta (siempre ocurre)
Consecuencias: anuncios invasivos, venta de datos, precios discriminatorios

Medidas adecuadas:
✅ Firefox + uBlock Origin
✅ DNS seguro (DoH con 1.1.1.1)
✅ HTTPS everywhere
✅ Gestor de contraseñas
❌ Tor (excesivo para esta amenaza)
```

#### Perfil 2: Periodista/Activista en democracia
```
Activos: fuentes, comunicaciones, identidad
Adversarios: empresas demandantes, actores privados, hackers
Probabilidad: Media (si el trabajo es sensible)
Consecuencias: exposición de fuentes, acoso

Medidas adecuadas:
✅ VPN de confianza verificada (ProtonVPN, Mullvad)
✅ Signal para comunicaciones
✅ Tor Browser para investigación sensible
✅ Contraseñas únicas + 2FA
✅ Cifrado de disco completo
❌ Tor para todo (puede ralentizar el trabajo)
```

#### Perfil 3: Disidente en régimen autoritario
```
Activos: identidad, ubicación, comunicaciones, red de contactos
Adversarios: gobierno nacional, servicios de inteligencia
Probabilidad: Alta si hay activismo
Consecuencias: detención, tortura, muerte

Medidas adecuadas:
✅ Tails OS (sin rastros en el dispositivo)
✅ Tor con puentes obfs4 (oculta el uso de Tor)
✅ Dispositivos dedicados y compartimentados
✅ Comunicación face-to-face para lo crítico
✅ Protocolos de seguridad operacional (OPSEC)
✅ Formación especializada en seguridad digital
```

### La cadena de privacidad es tan fuerte como su eslabón más débil

```
Ejemplo de fallo común:
Usuario usa Tor Browser ✅
Usuario usa VPN ✅
Usuario accede a redes sociales con su cuenta real ❌
→ Todo el anonimato anulado

Otro ejemplo:
Periodista usa Signal para comunicarse ✅
Periodista menciona el nombre de su fuente en un email normal ❌
→ La fuente está expuesta
```

---

## 14. Configuraciones avanzadas de privacidad

### Firefox hardening

```javascript
// about:config - Configuraciones de privacidad avanzadas

// Evitar fingerprinting
privacy.resistFingerprinting = true
privacy.fingerprintingProtection = true

// DNS seguro
network.trr.mode = 2          // 2=preferir DoH, 3=solo DoH
network.trr.uri = "https://cloudflare-dns.com/dns-query"
network.trr.bootstrapAddress = "1.1.1.1"

// Aislar cookies por origen
network.cookie.cookieBehavior = 5  // Total Cookie Protection

// Deshabilitar WebRTC (puede filtrar IP real con VPN)
media.peerconnection.enabled = false

// Evitar telemetría
toolkit.telemetry.enabled = false
datareporting.healthreport.uploadEnabled = false

// HTTPS estricto
dom.security.https_only_mode = true
dom.security.https_only_mode_ever_enabled = true
```

### Configurar un resolver DNS local con Pi-hole + DoH

```bash
# 1. Instalar Pi-hole (en Raspberry Pi o servidor local)
curl -sSL https://install.pi-hole.net | bash

# 2. Instalar cloudflared para DoH
# Elige la versión según tu arquitectura:
#   amd64 (PC/servidor x86_64):  cloudflared-linux-amd64
#   arm64 (Raspberry Pi 4/64bit): cloudflared-linux-arm64
#   arm (Raspberry Pi 32bit):     cloudflared-linux-arm
ARCH="arm64"  # Cambia a "amd64" si no es Raspberry Pi
wget "https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-${ARCH}"
sudo mv "cloudflared-linux-${ARCH}" /usr/local/bin/cloudflared
sudo chmod +x /usr/local/bin/cloudflared

# 3. Configurar cloudflared como DoH proxy
sudo nano /etc/cloudflared/config.yml
# proxy-dns: true
# proxy-dns-port: 5053
# proxy-dns-upstream:
#   - https://1.1.1.1/dns-query
#   - https://1.0.0.1/dns-query

# 4. Configurar Pi-hole para usar cloudflared
# En Pi-hole admin: Settings → DNS → Custom upstream: 127.0.0.1#5053

# 5. Apuntar tu router al Pi-hole para toda la red
# Router → DHCP Settings → DNS Server: <IP del Pi-hole>
```

### Compartimentación digital

Separar identidades digitales para diferentes actividades:

```
Identidad A: trabajo
├── Navegador: Chrome con cuenta de trabajo
├── Email: cuenta corporativa
└── VPN: VPN de empresa

Identidad B: personal
├── Navegador: Firefox con perfil personal
├── Email: ProtonMail
└── VPN: ProtonVPN personal

Identidad C: actividades sensibles
├── Navegador: Tor Browser
├── Email: solo .onion o desechables
└── OS: Tails OS o VM aislada
```

### Tails OS: el sistema operativo de privacidad

Tails es un sistema operativo live que:
- Arranca desde USB
- Enruta **todo** el tráfico por Tor
- No deja rastros en el ordenador
- Se autodestruye en RAM al apagar

```bash
# Verificar imagen descargada
gpg --auto-key-locate nodefault,wkd --locate-keys tails@boum.org
gpg --verify tails-amd64-*.iso.sig tails-amd64-*.iso

# Crear USB booteable (Linux)
sudo dd if=tails-amd64-*.iso of=/dev/sdX bs=4M status=progress
# ADVERTENCIA: verifica bien /dev/sdX antes de ejecutar
```

---

## 15. Ataques reales y contramedidas

### Man-in-the-Middle (MITM)

Un atacante se interpone entre tú y el servidor:

```
Normal:  Tu PC ──────────────────────────► google.com

MITM:    Tu PC ──► Atacante ──────────► google.com
                  (intercepta y puede
                   leer/modificar el tráfico)
```

**Escenario real:** Atacante crea un Wi-Fi falso llamado "Starbucks_Free":

```bash
# Cómo detectar un ataque MITM
# 1. Verificar certificados SSL (¡siempre!)
openssl s_client -connect banco.com:443 | openssl x509 -noout -fingerprint
# Compara el fingerprint con el conocido del sitio

# 2. Usar HSTS preloading
# Los sitios con HSTS fuerzan HTTPS y no aceptan certificados inválidos

# 3. Monitorizar tu tabla ARP
arp -a
# Si ves dos IPs con la misma MAC → posible ARP spoofing
```

**Contramedidas:**
- Siempre usar VPN en Wi-Fi público
- Verificar certificados SSL
- Activar HSTS en tus propios servidores
- No ignorar advertencias de certificados del navegador

### DNS Spoofing / Cache Poisoning

Un atacante modifica respuestas DNS para redirigirte a sitios falsos:

```bash
# Ejemplo de ataque DNS
# Atacante envía respuesta DNS falsa:
# "banco.com → 192.168.1.1 (servidor del atacante)"
# Usuario visita lo que cree es su banco pero es un phishing

# Protección: usar DNSSEC + DoH
# DNSSEC: las respuestas están firmadas digitalmente
# DoH: el canal está cifrado (no puede ser interceptado)

# Verificar si un dominio usa DNSSEC
dig +dnssec +short banco.com
dig @8.8.8.8 banco.com | grep -i "authenticated"
```

### SSL Stripping

Un atacante convierte HTTPS en HTTP:

```
Usuario escribe: banco.com (sin https://)
Atacante intercepta y hace:
  - Conecta a banco.com por HTTPS (comunicación segura con el banco)
  - Sirve al usuario la versión HTTP (sin cifrar)
El usuario cree estar en banco.com, el atacante ve todo
```

**Contramedidas:**
```bash
# Verificar HSTS en un sitio
curl -I https://banco.com | grep -i "strict-transport"
# Respuesta esperada: Strict-Transport-Security: max-age=31536000; includeSubDomains

# Configurar HSTS en tu servidor Nginx
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

# Agregar tu dominio a HSTS Preload List
# https://hstspreload.org/
```

### WebRTC IP Leak

WebRTC puede revelar tu IP real incluso usando VPN:

```javascript
// Cómo funciona el leak de WebRTC
// Los navegadores usan STUN servers para WebRTC y revelan IP local y pública
const pc = new RTCPeerConnection({iceServers: [{urls: "stun:stun.l.google.com:19302"}]});
pc.createDataChannel("");
pc.createOffer().then(o => pc.setLocalDescription(o));
pc.onicecandidate = e => {
  if (!e.candidate) return;
  const ip = e.candidate.candidate.split(' ')[4];
  console.log("Tu IP real:", ip); // ← Fuga aunque uses VPN
};
```

**Solución:**
```javascript
// about:config en Firefox
media.peerconnection.enabled = false

// O en Chrome: extensión WebRTC Leak Prevent
```

### Ataques de correlación en Tor

```
Adversario con capacidad global de vigilancia:
├── Controla muchos nodos de entrada y salida de Tor
├── Monitoriza tiempos y volúmenes de paquetes
└── Correlaciona estadísticamente
    "PC X envió 100KB a las 14:32:15"
    "Nodo de salida Y envió 100KB a destino Z a las 14:32:18"
    → Probable: X visitó Z

Contramedidas:
├── Usar servicios .onion (sin nodo de salida)
├── Añadir latencia artificial (reduce velocidad)
└── Usar Tor con padding de tráfico activado
```

---

## 16. Ruta de aprendizaje

### Fase 1 - Fundamentos (1-2 semanas)

```
Semana 1:
☐ Lee este documento del nivel básico
☐ Instala uBlock Origin en tu navegador
☐ Cambia tu DNS a 1.1.1.1 o 9.9.9.9
☐ Verifica que usas HTTPS (candado en todos los sitios que visitas)
☐ Prueba el modo incógnito y entiende sus límites
☐ Visita coveryourtracks.eff.org para ver tu fingerprint

Semana 2:
☐ Activa DoH en Firefox (network.trr.mode = 2)
☐ Instala un gestor de contraseñas (Bitwarden recomendado, gratis)
☐ Activa 2FA en tus cuentas importantes
☐ Lee sobre el modelo OSI y cómo viajan los paquetes
```

### Fase 2 - Herramientas intermedias (2-4 semanas)

```
Semana 3-4:
☐ Instala y configura una VPN (ProtonVPN free o Mullvad)
☐ Realiza una prueba de fugas de DNS en dnsleaktest.com
☐ Aprende a usar curl con proxies
☐ Configura Firefox con los ajustes de privacidad avanzados
☐ Instala y experimenta con Wireshark para ver tu tráfico

Semana 5-6:
☐ Instala Tor Browser y navega por servicios .onion
☐ Aprende a crear tu propio servidor VPN con WireGuard
☐ Configura un Pi-hole si tienes hardware disponible
☐ Estudia el modelo de amenazas y define el tuyo propio
```

### Fase 3 - Nivel avanzado (1-3 meses)

```
Mes 2:
☐ Experimenta con Tails OS (en USB)
☐ Aprende sobre OPSEC (Operational Security)
☐ Monta un servicio onion propio
☐ Estudia los ataques de correlación y fingerprinting

Mes 3:
☐ Aprende criptografía aplicada (PGP, cifrado de disco)
☐ Estudia I2P y Freenet
☐ Contribuye al proyecto Tor si puedes (monta un relay)
☐ Haz un análisis de tráfico con Wireshark para validar tu setup
```

### Recursos recomendados

```
Documentación oficial:
├── https://www.torproject.org/     (Tor)
├── https://www.privacyguides.org/  (Guía completa de privacidad)
├── https://ssd.eff.org/            (EFF: Surveillance Self-Defense)
└── https://www.wireguard.com/      (WireGuard)

Libros:
├── "The Art of Invisibility" - Kevin Mitnick
├── "Data and Goliath" - Bruce Schneier
└── "Permanent Record" - Edward Snowden

Herramientas recomendadas:
├── Navegador: Firefox (hardened) o Tor Browser
├── VPN: Mullvad, ProtonVPN (política verificada de no logs)
├── DNS: 1.1.1.1 (Cloudflare) o 9.9.9.9 (Quad9)
├── Email: ProtonMail, Tutanota
├── Mensajería: Signal
├── Contraseñas: Bitwarden (código abierto, auto-hosteable)
└── OS privacidad: Tails, Whonix, Qubes OS
```

---

## 17. Ejercicios prácticos

### Ejercicio 1 (Básico): Auditoría de privacidad básica

**Objetivo:** Entender cómo te rastrean actualmente.

```bash
# Paso 1: Ver tu IP pública actual
curl https://api.ipify.org && echo

# Paso 2: Ver información asociada a tu IP
curl "https://ipapi.co/json/" | python3 -m json.tool

# Paso 3: Comprobar tu fingerprint
# Visita: https://coveryourtracks.eff.org/
# ¿Eres único? ¿Qué tan identificable eres?

# Paso 4: Comprobar tu DNS
# Visita: https://dnsleaktest.com
# ¿Qué servidor DNS estás usando?

# Paso 5: Registrar lo que aprendiste
# ¿Qué ve tu ISP? ¿Qué ven los sitios web?
```

### Ejercicio 2 (Básico): Configurar DNS seguro

**Objetivo:** Cifrar tus consultas DNS.

```bash
# Opción A: Configurar DoH en Firefox
# 1. Abre about:config
# 2. Busca network.trr.mode y ponlo en 2
# 3. Busca network.trr.uri
# 4. Pon: https://cloudflare-dns.com/dns-query
# 5. Verifica en: https://1.1.1.1/help

# Opción B: Configurar DNS del sistema en Linux
# Editar /etc/systemd/resolved.conf:
sudo nano /etc/systemd/resolved.conf
# Agregar/modificar:
# [Resolve]
# DNS=1.1.1.1 9.9.9.9
# DNSOverTLS=yes
# FallbackDNS=8.8.8.8

sudo systemctl restart systemd-resolved

# Verificar
resolvectl status | grep "DNS Servers"
```

### Ejercicio 3 (Intermedio): Instalar y configurar WireGuard

**Objetivo:** Crear una VPN propia.

```bash
# REQUISITO: Un servidor VPS (puede ser el más barato, ~3€/mes)
# Proveedores: Hetzner, DigitalOcean, Linode

# === EN EL SERVIDOR ===
# Instalar WireGuard
sudo apt update && sudo apt install wireguard -y

# Generar claves
wg genkey | tee server_private.key | wg pubkey > server_public.key
cat server_private.key   # Guarda esto
cat server_public.key    # Guarda esto también

# También generar claves para el cliente
wg genkey | tee client_private.key | wg pubkey > client_public.key

# Crear configuración del servidor
sudo tee /etc/wireguard/wg0.conf << EOF
[Interface]
PrivateKey = $(cat server_private.key)
Address = 10.0.0.1/24
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
PublicKey = $(cat client_public.key)
AllowedIPs = 10.0.0.2/32
EOF

# Habilitar IP forwarding
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Iniciar
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0

# === EN TU PC (CLIENTE) ===
sudo apt install wireguard -y

sudo tee /etc/wireguard/wg0.conf << EOF
[Interface]
PrivateKey = $(cat client_private.key)
Address = 10.0.0.2/24
DNS = 1.1.1.1

[Peer]
PublicKey = <server_public_key>
Endpoint = <IP_DEL_SERVIDOR>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
EOF

# Conectar
sudo wg-quick up wg0

# Verificar (debe mostrar IP del servidor)
curl https://api.ipify.org && echo

# Probar fugas de DNS
# Visita https://dnsleaktest.com → ¿sale el servidor VPN?
```

### Ejercicio 4 (Intermedio): Analizar tráfico con Wireshark

**Objetivo:** Ver con tus propios ojos qué información viaja sin cifrar.

```bash
# Instalar Wireshark
sudo apt install wireshark -y
# Agregar tu usuario al grupo wireshark
sudo usermod -aG wireshark $USER
# Volver a iniciar sesión para aplicar

# Abrir Wireshark
wireshark &

# Experimento 1: DNS sin cifrar
# 1. En Wireshark: filtrar con "dns"
# 2. En terminal: nslookup google.com
# 3. Observa: el dominio viaja en texto plano

# Experimento 2: HTTP vs HTTPS
# 1. Filtrar con "http"
# 2. Visita un sitio HTTP (http://neverssl.com - ¡funciona en portales!)
# 3. Ve el contenido completo en texto plano
# 4. Ahora visita un sitio HTTPS: verás solo datos cifrados

# Experimento 3: Ver metadatos con VPN activa
# 1. Activa tu VPN
# 2. Filtra con "ip.addr == <IP_DE_LA_VPN>"
# 3. Verás tráfico cifrado hacia la VPN, pero no el destino final
```

### Ejercicio 5 (Avanzado): Comparar fingerprint con Tor Browser

**Objetivo:** Entender cómo Tor Browser oculta tu identidad.

```bash
# Paso 1: Sin Tor Browser
# 1. Ve a https://amiunique.org/ con tu navegador normal
# 2. Anota: ¿Cuántos de X usuarios son como tú?
# 3. Anota: ¿Qué atributos te hacen único?

# Paso 2: Con Tor Browser
# 1. Descarga Tor Browser de https://www.torproject.org/
# 2. Ve al mismo sitio https://amiunique.org/
# 3. Compara: deberías ser mucho menos único

# Paso 3: Verificar que usas Tor
curl --socks5-hostname 127.0.0.1:9150 https://check.torproject.org/ | \
  grep -o "Congratulations.*"
# Puerto 9150 = Tor Browser SOCKS proxy

# Paso 4: Ver los circuitos Tor
# En Tor Browser: click en el candado → Circuit Information
# Verás los 3 países por los que pasa tu tráfico
```

### Ejercicio 6 (Avanzado): Modelo de amenazas personal

**Objetivo:** Definir tu propia estrategia de privacidad.

```
Responde estas preguntas para definir tu modelo de amenazas:

1. ¿Qué información quiero proteger?
   □ Historial de navegación
   □ Comunicaciones personales
   □ Identidad real
   □ Ubicación
   □ Actividades profesionales/activismo

2. ¿De quién me quiero proteger?
   □ Anunciantes y empresas de datos
   □ Mi ISP
   □ Empleadores / escuela
   □ Gobierno de mi país
   □ Gobiernos extranjeros
   □ Criminales / hackers

3. ¿Qué probabilidad hay de ataque?
   □ Baja (solo privacidad básica)
   □ Media (trabajo sensible)
   □ Alta (activismo, periodismo, disidencia)

4. Según tus respuestas, diseña tu stack:
   □ Básico: Firefox + uBlock + DoH
   □ Intermedio: VPN + gestor contraseñas + 2FA
   □ Avanzado: Tor + Tails + Signal + PGP
   □ Máximo: Qubes OS + compartimentación estricta
```

---

## 📊 Resumen de Protecciones por Tecnología

```
                        Tu ISP  Sitios web  Anunciantes  Gobierno  Hackers locales
────────────────────────────────────────────────────────────────────────────────
HTTPS solo              Ver IP  Ver IP      Rastrear     Ver IP    Cifra contenido
HTTPS + DoH             Ver IP  Ver IP      Rastrear     Ver IP    Cifra DNS+contenido
HTTPS + VPN             No ve   Ver VPN IP  Rastrear     Ver VPN   Cifra todo
HTTPS + VPN + DoH       No ve   Ver VPN IP  Rastrear*   Ver VPN   Cifra todo
Tor Browser             Ve Tor  No ve IP    No rastrea** No ve IP  Cifra todo
Tails + Tor             Ve Tor  No ve IP    No rastrea** Sin trace Cifra todo
────────────────────────────────────────────────────────────────────────────────
* Si no estás logueado en servicios de Google/Meta
** A menos que te identifiques mediante comportamiento o login
```

---

> 🎯 **Conclusión final:** La privacidad perfecta no existe, pero con las herramientas adecuadas y un buen modelo de amenazas, puedes protegerte efectivamente de los adversarios que importan en tu caso. Empieza con lo básico, entiende cada herramienta antes de adoptarla, y recuerda que el factor humano (OPSEC) es siempre el eslabón más débil.
