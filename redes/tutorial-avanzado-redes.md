# 🌐 Tutorial Avanzado de Redes

Este tutorial cubre conceptos avanzados de redes que te permitirán diseñar, implementar y administrar infraestructuras de red empresariales y complejas.

---

## 📋 Tabla de Contenidos

1. [Diseño Avanzado de Redes](#diseño-avanzado-de-redes)
2. [Enrutamiento Avanzado](#enrutamiento-avanzado)
3. [VLANs y Segmentación de Red](#vlans-y-segmentación-de-red)
4. [Calidad de Servicio (QoS)](#calidad-de-servicio-qos)
5. [Alta Disponibilidad y Redundancia](#alta-disponibilidad-y-redundancia)
6. [IPv6 Avanzado](#ipv6-avanzado)
7. [Software Defined Networking (SDN)](#software-defined-networking-sdn)
8. [Análisis y Monitoreo de Tráfico](#análisis-y-monitoreo-de-tráfico)
9. [Automatización de Redes](#automatización-de-redes)
10. [Troubleshooting Avanzado](#troubleshooting-avanzado)
11. [Ejercicios Prácticos](#ejercicios-prácticos)

---

## Diseño Avanzado de Redes

### Arquitectura de Tres Capas (Three-Tier)

El modelo jerárquico de tres capas es el estándar en redes empresariales:

\`\`\`
┌─────────────────────────────────────────────────────────────────┐
│                         CAPA CORE                                │
│            (Alta velocidad, enrutamiento rápido)                 │
│                    ┌──────────┐                                  │
│                    │  Core    │                                  │
│                    │ Router   │                                  │
│                    └────┬─────┘                                  │
└─────────────────────────┼───────────────────────────────────────┘
                          │
┌─────────────────────────┼───────────────────────────────────────┐
│                    CAPA DISTRIBUCIÓN                             │
│           (Políticas, filtrado, agregación)                      │
│         ┌───────────┐           ┌───────────┐                    │
│         │  Switch   │───────────│  Switch   │                    │
│         │  Dist-1   │           │  Dist-2   │                    │
│         └─────┬─────┘           └─────┬─────┘                    │
└───────────────┼───────────────────────┼─────────────────────────┘
                │                       │
┌───────────────┼───────────────────────┼─────────────────────────┐
│                    CAPA ACCESO                                   │
│            (Conexión de usuarios finales)                        │
│    ┌─────────┐ ┌─────────┐     ┌─────────┐ ┌─────────┐          │
│    │ Switch  │ │ Switch  │     │ Switch  │ │ Switch  │          │
│    │ Acc-1   │ │ Acc-2   │     │ Acc-3   │ │ Acc-4   │          │
│    └────┬────┘ └────┬────┘     └────┬────┘ └────┬────┘          │
│         │           │               │           │                │
│      [PCs]       [PCs]           [PCs]       [PCs]               │
└─────────────────────────────────────────────────────────────────┘
\`\`\`

### Arquitectura Spine-Leaf (Data Centers)

Modelo moderno para centros de datos con baja latencia:

\`\`\`
                         SPINE LAYER
           ┌──────────┐  ┌──────────┐  ┌──────────┐
           │ Spine-1  │  │ Spine-2  │  │ Spine-3  │
           └────┬─────┘  └────┬─────┘  └────┬─────┘
                │             │             │
    ┌───────────┼─────────────┼─────────────┼───────────┐
    │           │             │             │           │
┌───┴───┐   ┌───┴───┐     ┌───┴───┐     ┌───┴───┐   ┌───┴───┐
│Leaf-1 │   │Leaf-2 │     │Leaf-3 │     │Leaf-4 │   │Leaf-5 │
└───┬───┘   └───┬───┘     └───┬───┘     └───┬───┘   └───┬───┘
    │           │             │             │           │
 [Servers]   [Servers]     [Servers]     [Servers]   [Servers]
\`\`\`

**Características:**
- Cada Leaf conecta a todos los Spines
- Máximo 2 saltos entre cualquier servidor
- Escalabilidad horizontal
- Ancho de banda predecible

### Cálculo de Capacidad de Red

\`\`\`python
# Cálculo de ancho de banda necesario
def calcular_ancho_banda(usuarios, consumo_medio_mbps, factor_sobresuscripcion):
    """
    Calcula el ancho de banda necesario para uplinks
    
    Args:
        usuarios: número de usuarios/dispositivos
        consumo_medio_mbps: consumo promedio por usuario en Mbps
        factor_sobresuscripcion: típicamente 4:1 a 20:1 para acceso
    
    Returns:
        float: ancho de banda necesario para el uplink en Mbps
    """
    trafico_total = usuarios * consumo_medio_mbps
    uplink_necesario = trafico_total / factor_sobresuscripcion
    return uplink_necesario

# Ejemplo: 500 usuarios, 100 Mbps cada uno, sobresuscripción 10:1
uplink = calcular_ancho_banda(500, 100, 10)
print(f"Uplink necesario: {uplink} Mbps = {uplink/1000} Gbps")
\`\`\`

---

## Enrutamiento Avanzado

### OSPF (Open Shortest Path First)

Protocolo de enrutamiento de estado de enlace para redes internas.

#### Configuración en Linux (FRRouting)

\`\`\`bash
# Instalar FRRouting
sudo apt install frr

# Habilitar OSPF
sudo sed -i 's/ospfd=no/ospfd=yes/' /etc/frr/daemons
sudo systemctl restart frr

# Configuración vía vtysh
sudo vtysh
\`\`\`

\`\`\`cisco
! Configuración OSPF
configure terminal
router ospf
  ospf router-id 1.1.1.1
  network 192.168.1.0/24 area 0
  network 10.0.0.0/24 area 1
  passive-interface eth0
  
! Redistribución de rutas
  redistribute connected
  redistribute static
  
! Autenticación MD5
interface eth1
  ip ospf authentication message-digest
  ip ospf message-digest-key 1 md5 SecretKey123

! Costos personalizados
interface eth2
  ip ospf cost 100

exit
write memory
\`\`\`

#### Áreas OSPF y Tipos de LSA

\`\`\`
                    ┌─────────────┐
                    │   Area 0    │
                    │  (Backbone) │
                    │  ABR-1/ABR-2│
                    └──────┬──────┘
           ┌───────────────┼───────────────┐
           │               │               │
    ┌──────┴──────┐ ┌──────┴──────┐ ┌──────┴──────┐
    │   Area 1    │ │   Area 2    │ │   Area 3    │
    │   (Normal)  │ │   (Stub)    │ │   (NSSA)    │
    └─────────────┘ └─────────────┘ └─────────────┘
\`\`\`

| Tipo LSA | Nombre | Descripción |
|----------|--------|-------------|
| 1 | Router | Describe enlaces del router |
| 2 | Network | Describe red multi-acceso |
| 3 | Summary | Rutas inter-área (ABR) |
| 4 | ASBR Summary | Ruta hacia ASBR |
| 5 | External | Rutas externas |
| 7 | NSSA External | Externas en áreas NSSA |

### BGP (Border Gateway Protocol)

Protocolo para enrutamiento entre sistemas autónomos.

\`\`\`cisco
! Configuración BGP básica
router bgp 65001
  bgp router-id 1.1.1.1
  
  ! Vecino eBGP
  neighbor 203.0.113.1 remote-as 65002
  neighbor 203.0.113.1 description ISP-Principal
  neighbor 203.0.113.1 update-source eth0
  
  ! Vecino iBGP
  neighbor 192.168.1.2 remote-as 65001
  neighbor 192.168.1.2 next-hop-self
  
  ! Anunciar redes
  network 198.51.100.0/24
  
  ! Route-maps para políticas
  neighbor 203.0.113.1 route-map ISP-IN in
  neighbor 203.0.113.1 route-map ISP-OUT out

! Route-map para modificar atributos
route-map ISP-IN permit 10
  set local-preference 200
  
route-map ISP-OUT permit 10
  set as-path prepend 65001 65001
\`\`\`

#### Atributos BGP

| Atributo | Tipo | Uso |
|----------|------|-----|
| Weight | Cisco propietario | Mayor valor preferido (local) |
| Local Preference | Well-known | Mayor valor preferido (iBGP) |
| AS Path | Well-known | Menor longitud preferida |
| Origin | Well-known | IGP > EGP > Incomplete |
| MED | Optional | Menor valor preferido (entre AS) |
| Community | Optional | Etiquetado de rutas |

### MPLS y VPNs de Capa 3

\`\`\`
┌─────────┐          ┌─────────┐          ┌─────────┐
│   CE1   │──────────│   PE1   │──────────│    P    │
│  (Site) │          │(Provider│  MPLS    │(Provider│
└─────────┘          │  Edge)  │  Core    │  Core)  │
                     └─────────┘          └────┬────┘
                                               │
┌─────────┐          ┌─────────┐               │
│   CE2   │──────────│   PE2   │───────────────┘
│  (Site) │          │         │
└─────────┘          └─────────┘
\`\`\`

**Conceptos clave:**
- **Label Switching**: Enrutamiento basado en etiquetas
- **VRF**: Virtual Routing and Forwarding
- **RD**: Route Distinguisher
- **RT**: Route Target

---

## VLANs y Segmentación de Red

### Configuración de VLANs en Linux

\`\`\`bash
# Crear interfaz VLAN
sudo ip link add link eth0 name eth0.100 type vlan id 100
sudo ip addr add 192.168.100.1/24 dev eth0.100
sudo ip link set dev eth0.100 up

# Persistente en Netplan (Ubuntu)
cat > /etc/netplan/01-vlans.yaml << 'EOF'
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
  vlans:
    vlan100:
      id: 100
      link: eth0
      addresses: [192.168.100.1/24]
    vlan200:
      id: 200
      link: eth0
      addresses: [192.168.200.1/24]
EOF

sudo netplan apply
\`\`\`

### Inter-VLAN Routing con Linux

\`\`\`bash
# Habilitar enrutamiento
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward

# Configuración permanente
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Firewall para controlar tráfico entre VLANs
sudo iptables -A FORWARD -i vlan100 -o vlan200 -j ACCEPT
sudo iptables -A FORWARD -i vlan200 -o vlan100 -m state --state ESTABLISHED,RELATED -j ACCEPT
\`\`\`

### Private VLANs (PVLAN)

\`\`\`
┌─────────────────────────────────────────────────────┐
│                  VLAN Primaria (100)                 │
│                                                      │
│  ┌──────────────────┐    ┌──────────────────┐       │
│  │ VLAN Aislada     │    │ VLAN Comunidad   │       │
│  │     (101)        │    │     (102)        │       │
│  │                  │    │                  │       │
│  │ [Host] [Host]    │    │ [Host] [Host]    │       │
│  │   ↕     ↕        │    │   ↕     ↕        │       │
│  │   ✗     ✗        │    │   ✓     ✓        │       │
│  └────────┬─────────┘    └────────┬─────────┘       │
│           │                       │                  │
│           └───────────┬───────────┘                  │
│                       │                              │
│               ┌───────┴───────┐                      │
│               │   Promiscuous │                      │
│               │   (Gateway)   │                      │
│               └───────────────┘                      │
└─────────────────────────────────────────────────────┘
\`\`\`

**Tipos de puertos PVLAN:**
- **Promiscuous**: Puede comunicarse con todos
- **Isolated**: No puede comunicarse con otros isolated
- **Community**: Puede comunicarse dentro de su comunidad

---

## Calidad de Servicio (QoS)

### Clasificación de Tráfico

\`\`\`bash
# Marcar paquetes con iptables/nftables
# DSCP values: EF (46), AF41 (34), AF21 (18), BE (0)

# Marcar VoIP como EF (Expedited Forwarding)
sudo iptables -t mangle -A POSTROUTING -p udp --dport 5060:5061 -j DSCP --set-dscp 46
sudo iptables -t mangle -A POSTROUTING -p udp --dport 10000:20000 -j DSCP --set-dscp 46

# Marcar video como AF41
sudo iptables -t mangle -A POSTROUTING -p tcp --dport 443 -m string --string "zoom" --algo bm -j DSCP --set-dscp 34
\`\`\`

### Traffic Control (tc) en Linux

\`\`\`bash
# Configurar HTB (Hierarchical Token Bucket)
TC="sudo tc"

# Limpiar configuración anterior
\$TC qdisc del dev eth0 root 2>/dev/null

# Crear qdisc raíz
\$TC qdisc add dev eth0 root handle 1: htb default 30

# Clase principal (100 Mbps total)
\$TC class add dev eth0 parent 1: classid 1:1 htb rate 100mbit burst 15k

# Clase para VoIP (alta prioridad, 10 Mbps garantizados)
\$TC class add dev eth0 parent 1:1 classid 1:10 htb rate 10mbit ceil 20mbit prio 1

# Clase para video (30 Mbps)
\$TC class add dev eth0 parent 1:1 classid 1:20 htb rate 30mbit ceil 50mbit prio 2

# Clase para tráfico general (resto)
\$TC class add dev eth0 parent 1:1 classid 1:30 htb rate 60mbit ceil 100mbit prio 3

# Aplicar SFQ a cada clase para fairness
\$TC qdisc add dev eth0 parent 1:10 handle 10: sfq perturb 10
\$TC qdisc add dev eth0 parent 1:20 handle 20: sfq perturb 10
\$TC qdisc add dev eth0 parent 1:30 handle 30: sfq perturb 10

# Filtros para clasificar tráfico
# VoIP (puerto 5060 SIP y RTP)
\$TC filter add dev eth0 parent 1: protocol ip prio 1 u32 \\
  match ip dport 5060 0xffff flowid 1:10

# Video conferencia (puertos Zoom)
\$TC filter add dev eth0 parent 1: protocol ip prio 2 u32 \\
  match ip dport 8801 0xffff flowid 1:20

# Verificar configuración
\$TC -s qdisc show dev eth0
\$TC -s class show dev eth0
\`\`\`

### Valores DSCP Comunes

| Clase | DSCP | Valor | Uso |
|-------|------|-------|-----|
| EF | 101110 | 46 | VoIP, tiempo real |
| AF41 | 100010 | 34 | Video interactivo |
| AF31 | 011010 | 26 | Streaming |
| AF21 | 010010 | 18 | Transaccional |
| AF11 | 001010 | 10 | Bulk data |
| BE | 000000 | 0 | Best effort |

---

## Alta Disponibilidad y Redundancia

### VRRP (Virtual Router Redundancy Protocol)

\`\`\`bash
# Instalar keepalived
sudo apt install keepalived

# Configuración Master (/etc/keepalived/keepalived.conf)
cat > /etc/keepalived/keepalived.conf << 'EOF'
global_defs {
    router_id LVS_ROUTER1
}

vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 1
    
    authentication {
        auth_type PASS
        auth_pass secret123
    }
    
    virtual_ipaddress {
        192.168.1.1/24
    }
    
    track_script {
        chk_nginx
    }
}

vrrp_script chk_nginx {
    script "/usr/bin/pgrep nginx"
    interval 2
    weight -20
}
EOF

sudo systemctl enable --now keepalived
\`\`\`

\`\`\`bash
# Configuración Backup (segundo nodo)
cat > /etc/keepalived/keepalived.conf << 'EOF'
global_defs {
    router_id LVS_ROUTER2
}

vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 90
    advert_int 1
    
    authentication {
        auth_type PASS
        auth_pass secret123
    }
    
    virtual_ipaddress {
        192.168.1.1/24
    }
}
EOF
\`\`\`

### Link Aggregation (LACP)

\`\`\`bash
# Crear bonding con modo 802.3ad (LACP)
sudo modprobe bonding

# Configuración con Netplan
cat > /etc/netplan/02-bonding.yaml << 'EOF'
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
    eth1:
      dhcp4: no
  bonds:
    bond0:
      interfaces: [eth0, eth1]
      addresses: [192.168.1.10/24]
      gateway4: 192.168.1.1
      parameters:
        mode: 802.3ad
        lacp-rate: fast
        mii-monitor-interval: 100
        transmit-hash-policy: layer3+4
EOF

sudo netplan apply
\`\`\`

**Modos de Bonding:**

| Modo | Nombre | Descripción |
|------|--------|-------------|
| 0 | balance-rr | Round-robin |
| 1 | active-backup | Solo uno activo |
| 2 | balance-xor | Hash XOR |
| 3 | broadcast | Envía por todos |
| 4 | 802.3ad | LACP |
| 5 | balance-tlb | Balance TX |
| 6 | balance-alb | Balance TX+RX |

### Load Balancing con HAProxy

\`\`\`bash
# Instalar HAProxy
sudo apt install haproxy

# Configuración (/etc/haproxy/haproxy.cfg)
cat > /etc/haproxy/haproxy.cfg << 'EOF'
global
    log /dev/log local0
    maxconn 4096
    user haproxy
    group haproxy
    daemon
    stats socket /run/haproxy/admin.sock mode 660 level admin

defaults
    log     global
    mode    http
    option  httplog
    option  dontlognull
    timeout connect 5000
    timeout client  50000
    timeout server  50000

frontend http_front
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/haproxy.pem
    redirect scheme https if !{ ssl_fc }
    default_backend http_back
    
    # ACL para diferentes backends
    acl is_api path_beg /api
    use_backend api_back if is_api

backend http_back
    balance roundrobin
    option httpchk GET /health
    http-check expect status 200
    server web1 192.168.1.11:8080 check weight 1
    server web2 192.168.1.12:8080 check weight 1
    server web3 192.168.1.13:8080 check weight 2 backup

backend api_back
    balance leastconn
    option httpchk GET /api/health
    server api1 192.168.1.21:3000 check
    server api2 192.168.1.22:3000 check

listen stats
    bind *:8404
    stats enable
    stats uri /stats
    stats refresh 10s
    stats auth admin:password
EOF

sudo systemctl restart haproxy
\`\`\`

---

## IPv6 Avanzado

### Tipos de Direcciones IPv6

\`\`\`
┌────────────────────────────────────────────────────────────────┐
│                     Dirección IPv6 (128 bits)                   │
├────────────────────────────────┬───────────────────────────────┤
│     Prefijo de Red (64 bits)   │   Interface ID (64 bits)      │
└────────────────────────────────┴───────────────────────────────┘

Tipos:
- Unicast Global:    2000::/3    (Internet)
- Unique Local:      fc00::/7    (Privadas)
- Link-Local:        fe80::/10   (Solo enlace)
- Multicast:         ff00::/8    (Grupo)
- Loopback:          ::1/128     (Local)
\`\`\`

### Configuración Dual-Stack

\`\`\`bash
# Netplan con IPv4 e IPv6
cat > /etc/netplan/01-network.yaml << 'EOF'
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.10/24
        - 2001:db8:1::10/64
      gateway4: 192.168.1.1
      gateway6: 2001:db8:1::1
      nameservers:
        addresses:
          - 8.8.8.8
          - 2001:4860:4860::8888
EOF

sudo netplan apply
\`\`\`

### Router Advertisement (SLAAC)

\`\`\`bash
# Instalar radvd
sudo apt install radvd

# Configuración (/etc/radvd.conf)
cat > /etc/radvd.conf << 'EOF'
interface eth0 {
    AdvSendAdvert on;
    MinRtrAdvInterval 30;
    MaxRtrAdvInterval 100;
    
    # Prefijo para SLAAC
    prefix 2001:db8:1::/64 {
        AdvOnLink on;
        AdvAutonomous on;
        AdvRouterAddr off;
    };
    
    # DNS recursivo
    RDNSS 2001:4860:4860::8888 2001:4860:4860::8844 {
        AdvRDNSSLifetime 300;
    };
    
    # Sufijo DNS
    DNSSL example.com {
        AdvDNSSLLifetime 300;
    };
};
EOF

sudo systemctl enable --now radvd
\`\`\`

### DHCPv6

\`\`\`bash
# Instalar ISC DHCP para IPv6
sudo apt install isc-dhcp-server

# Configuración (/etc/dhcp/dhcpd6.conf)
cat > /etc/dhcp/dhcpd6.conf << 'EOF'
authoritative;

subnet6 2001:db8:1::/64 {
    range6 2001:db8:1::1000 2001:db8:1::1fff;
    
    option dhcp6.name-servers 2001:4860:4860::8888, 2001:4860:4860::8844;
    option dhcp6.domain-search "example.com";
    
    # Reservación (reemplazar xx:xx:xx:xx con el DUID real del cliente)
    host server1 {
        host-identifier option dhcp6.client-id 00:01:00:01:aa:bb:cc:dd;
        fixed-address6 2001:db8:1::100;
    }
}
EOF

# Configurar interfaz
echo 'INTERFACESv6="eth0"' | sudo tee /etc/default/isc-dhcp-server
sudo systemctl restart isc-dhcp-server6
\`\`\`

### Túneles IPv6

\`\`\`bash
# Túnel 6in4 (IPv6 sobre IPv4)
sudo ip tunnel add he-ipv6 mode sit remote 216.66.80.26 local 203.0.113.5 ttl 255
sudo ip link set he-ipv6 up
sudo ip addr add 2001:470:1f06:abc::2/64 dev he-ipv6
sudo ip route add ::/0 dev he-ipv6
\`\`\`

---

## Software Defined Networking (SDN)

### OpenFlow con Open vSwitch

\`\`\`bash
# Instalar Open vSwitch
sudo apt install openvswitch-switch

# Crear bridge
sudo ovs-vsctl add-br br0

# Agregar puertos
sudo ovs-vsctl add-port br0 eth0
sudo ovs-vsctl add-port br0 eth1

# Configurar controlador OpenFlow
sudo ovs-vsctl set-controller br0 tcp:192.168.1.100:6653

# Ver configuración
sudo ovs-vsctl show
sudo ovs-ofctl dump-flows br0
\`\`\`

### Reglas OpenFlow

\`\`\`bash
# Agregar flujos manualmente
# Permitir tráfico ARP
sudo ovs-ofctl add-flow br0 "priority=100,arp,actions=normal"

# Redirigir HTTP a puerto de inspección
sudo ovs-ofctl add-flow br0 "priority=200,tcp,tp_dst=80,actions=output:3"

# Rate limiting
sudo ovs-vsctl set interface eth0 ingress_policing_rate=1000000
sudo ovs-vsctl set interface eth0 ingress_policing_burst=100000

# Listar flujos
sudo ovs-ofctl dump-flows br0
\`\`\`

### Network Namespaces

\`\`\`bash
# Crear namespaces (simulando hosts virtuales)
sudo ip netns add ns1
sudo ip netns add ns2

# Crear veth pairs
sudo ip link add veth1 type veth peer name veth1-br
sudo ip link add veth2 type veth peer name veth2-br

# Mover interfaces a namespaces
sudo ip link set veth1 netns ns1
sudo ip link set veth2 netns ns2

# Conectar al bridge
sudo ovs-vsctl add-port br0 veth1-br
sudo ovs-vsctl add-port br0 veth2-br

# Configurar IPs
sudo ip netns exec ns1 ip addr add 10.0.0.1/24 dev veth1
sudo ip netns exec ns1 ip link set veth1 up
sudo ip netns exec ns1 ip link set lo up

sudo ip netns exec ns2 ip addr add 10.0.0.2/24 dev veth2
sudo ip netns exec ns2 ip link set veth2 up
sudo ip netns exec ns2 ip link set lo up

sudo ip link set veth1-br up
sudo ip link set veth2-br up

# Probar conectividad
sudo ip netns exec ns1 ping 10.0.0.2
\`\`\`

---

## Análisis y Monitoreo de Tráfico

### Captura Avanzada con tcpdump

\`\`\`bash
# Capturar tráfico HTTP con contenido
sudo tcpdump -i eth0 -A -s 0 'tcp port 80'

# Capturar solo paquetes SYN (nuevas conexiones)
sudo tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'

# Capturar DNS
sudo tcpdump -i eth0 -n 'port 53'

# Capturar paquetes fragmentados
sudo tcpdump -i eth0 '((ip[6:2] & 0x1fff) != 0)'

# Guardar a archivo PCAP con rotación
sudo tcpdump -i eth0 -w /var/log/capture_%Y%m%d_%H%M%S.pcap -G 3600 -W 24

# Leer archivo PCAP
tcpdump -r capture.pcap -n 'host 192.168.1.100'
\`\`\`

### Análisis con tshark

\`\`\`bash
# Estadísticas de conversaciones
tshark -r capture.pcap -q -z conv,ip

# Top talkers
tshark -r capture.pcap -q -z endpoints,ip

# Estadísticas HTTP
tshark -r capture.pcap -q -z http,tree

# Extraer campos específicos
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport \\
  -E header=y -E separator=, > output.csv

# Seguir stream TCP
tshark -r capture.pcap -z follow,tcp,ascii,0
\`\`\`

### NetFlow/IPFIX con nfdump

\`\`\`bash
# Instalar nfdump
sudo apt install nfdump

# Iniciar colector
nfcapd -w -D -l /var/cache/nfdump -p 2055

# Analizar flujos
nfdump -R /var/cache/nfdump -s srcip/bytes -n 10
nfdump -R /var/cache/nfdump -s dstport/flows -n 20

# Flujos por protocolo
nfdump -R /var/cache/nfdump -s proto/bytes

# Filtrar por tiempo y dirección
nfdump -R /var/cache/nfdump -t 2024/01/15.09:00:00-2024/01/15.10:00:00 \\
  'src ip 192.168.1.0/24 and dst port 443'
\`\`\`

### Monitoreo con Prometheus + Grafana

\`\`\`yaml
# docker-compose.yml para stack de monitoreo
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    ports:
      - "9090:9090"
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.retention.time=30d'

  snmp_exporter:
    image: prom/snmp-exporter:latest
    volumes:
      - ./snmp.yml:/etc/snmp_exporter/snmp.yml
    ports:
      - "9116:9116"

  blackbox_exporter:
    image: prom/blackbox-exporter:latest
    volumes:
      - ./blackbox.yml:/etc/blackbox/config.yml
    ports:
      - "9115:9115"
    command:
      - '--config.file=/etc/blackbox/config.yml'

  grafana:
    image: grafana/grafana:latest
    volumes:
      - grafana_data:/var/lib/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin

volumes:
  prometheus_data:
  grafana_data:
\`\`\`

\`\`\`yaml
# prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'snmp'
    static_configs:
      - targets:
        - 192.168.1.1  # Router
        - 192.168.1.2  # Switch
    metrics_path: /snmp
    params:
      module: [if_mib]
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: snmp_exporter:9116

  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [icmp]
    static_configs:
      - targets:
        - 8.8.8.8
        - 1.1.1.1
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: __address__
        replacement: blackbox_exporter:9115
\`\`\`

---

## Automatización de Redes

### Ansible para Networking

\`\`\`yaml
# inventario (inventory.yml)
all:
  children:
    routers:
      hosts:
        router1:
          ansible_host: 192.168.1.1
          ansible_network_os: ios
        router2:
          ansible_host: 192.168.1.2
          ansible_network_os: ios
    switches:
      hosts:
        switch1:
          ansible_host: 192.168.1.10
          ansible_network_os: ios

  vars:
    ansible_connection: network_cli
    ansible_user: admin
    ansible_password: "{{ vault_password }}"
\`\`\`

\`\`\`yaml
# playbook.yml - Configurar interfaces y VLANs
---
- name: Configurar switches
  hosts: switches
  gather_facts: no
  
  tasks:
    - name: Configurar VLANs
      cisco.ios.ios_vlans:
        config:
          - vlan_id: 10
            name: USUARIOS
          - vlan_id: 20
            name: SERVIDORES
          - vlan_id: 30
            name: VOIP
        state: merged

    - name: Configurar interfaces de acceso
      cisco.ios.ios_l2_interfaces:
        config:
          - name: GigabitEthernet0/1
            mode: access
            access:
              vlan: 10
          - name: GigabitEthernet0/2
            mode: trunk
            trunk:
              allowed_vlans: "10,20,30"
        state: merged

    - name: Guardar configuración
      cisco.ios.ios_config:
        save_when: modified
\`\`\`

### Python con Netmiko

\`\`\`python
#!/usr/bin/env python3
"""
Script para automatizar configuración de red con Netmiko
"""

from netmiko import ConnectHandler
from concurrent.futures import ThreadPoolExecutor
import json

# Dispositivos
devices = [
    {
        'device_type': 'cisco_ios',
        'host': '192.168.1.1',
        'username': 'admin',
        'password': 'secret',
        'secret': 'enable_secret'
    },
    {
        'device_type': 'cisco_ios',
        'host': '192.168.1.2',
        'username': 'admin',
        'password': 'secret',
        'secret': 'enable_secret'
    }
]

def configure_device(device, commands):
    """Configura un dispositivo con lista de comandos"""
    try:
        with ConnectHandler(**device) as conn:
            conn.enable()
            output = conn.send_config_set(commands)
            conn.save_config()
            return {
                'host': device['host'],
                'status': 'success',
                'output': output
            }
    except Exception as e:
        return {
            'host': device['host'],
            'status': 'error',
            'message': str(e)
        }

def backup_config(device):
    """Realiza backup de la configuración"""
    try:
        with ConnectHandler(**device) as conn:
            conn.enable()
            config = conn.send_command('show running-config')
            
            filename = f"backup_{device['host']}.cfg"
            with open(filename, 'w') as f:
                f.write(config)
            
            return {'host': device['host'], 'status': 'success', 'file': filename}
    except Exception as e:
        return {'host': device['host'], 'status': 'error', 'message': str(e)}

# Comandos a aplicar
commands = [
    'interface loopback100',
    'ip address 10.100.100.1 255.255.255.0',
    'description Loopback de monitoreo',
    'no shutdown',
    'exit',
    'ip access-list extended BLOCK_TELNET',
    'deny tcp any any eq 23',
    'permit ip any any',
    'exit'
]

# Ejecutar en paralelo
if __name__ == '__main__':
    with ThreadPoolExecutor(max_workers=5) as executor:
        # Backup primero
        backup_results = list(executor.map(backup_config, devices))
        print("Backups completados:", json.dumps(backup_results, indent=2))
        
        # Configurar
        config_results = list(executor.map(
            lambda d: configure_device(d, commands), 
            devices
        ))
        print("Configuración completada:", json.dumps(config_results, indent=2))
\`\`\`

### NAPALM para Multi-vendor

\`\`\`python
#!/usr/bin/env python3
"""
Ejemplo con NAPALM para gestión multi-vendor
"""

from napalm import get_network_driver

def get_device_info(device_config):
    """Obtiene información del dispositivo"""
    driver = get_network_driver(device_config['driver'])
    
    with driver(
        hostname=device_config['hostname'],
        username=device_config['username'],
        password=device_config['password']
    ) as device:
        facts = device.get_facts()
        interfaces = device.get_interfaces()
        arp_table = device.get_arp_table()
        bgp_neighbors = device.get_bgp_neighbors()
        
        return {
            'facts': facts,
            'interfaces': interfaces,
            'arp': arp_table,
            'bgp': bgp_neighbors
        }

def compare_config(device_config, new_config):
    """Compara configuración actual con nueva"""
    driver = get_network_driver(device_config['driver'])
    
    with driver(
        hostname=device_config['hostname'],
        username=device_config['username'],
        password=device_config['password']
    ) as device:
        device.load_merge_candidate(config=new_config)
        diff = device.compare_config()
        
        if diff:
            print(f"Cambios propuestos:\n{diff}")
            response = input("¿Aplicar cambios? (s/n): ")
            if response.lower() == 's':
                device.commit_config()
                print("Cambios aplicados")
            else:
                device.discard_config()
                print("Cambios descartados")
        else:
            print("No hay cambios necesarios")

# Ejemplo de uso
device = {
    'driver': 'ios',
    'hostname': '192.168.1.1',
    'username': 'admin',
    'password': 'secret'
}

info = get_device_info(device)
print(f"Hostname: {info['facts']['hostname']}")
print(f"Modelo: {info['facts']['model']}")
print(f"Interfaces activas: {sum(1 for i in info['interfaces'].values() if i['is_up'])}")
\`\`\`

---

## Troubleshooting Avanzado

### Diagnóstico de Conectividad

\`\`\`bash
# MTR - traceroute + ping combinado
mtr -rw -c 100 google.com

# Traceroute con diferentes protocolos
traceroute -I google.com    # ICMP
traceroute -T google.com    # TCP
traceroute -U google.com    # UDP

# Ping con tamaño específico (detectar MTU)
ping -M do -s 1472 -c 3 192.168.1.1

# Descubrir MTU del path
ping -M do -s 1500 192.168.1.1  # Si falla, reducir tamaño

# Verificar conectividad TCP
nc -zv 192.168.1.100 22 80 443

# Verificar estado de conexiones
ss -tunapl
netstat -tunapl
\`\`\`

### Análisis de Latencia y Pérdida

\`\`\`bash
# Análisis de latencia con estadísticas
ping -c 100 -i 0.2 google.com | tee ping_results.txt
awk -F'/' '/^rtt/ {print "min="\$4" avg="\$5" max="\$6}' ping_results.txt

# hping3 para análisis avanzado
sudo hping3 -S -p 80 -c 100 google.com

# fping para múltiples hosts
fping -c 10 -q 192.168.1.1 192.168.1.2 192.168.1.3

# Smokeping para monitoreo histórico (requiere instalación)
# Ver trends de latencia en web
\`\`\`

### Debug de DNS

\`\`\`bash
# Consulta detallada
dig +trace google.com

# Verificar registros específicos
dig @8.8.8.8 google.com MX +short
dig @8.8.8.8 google.com TXT +short

# Verificar propagación DNS
for ns in 8.8.8.8 1.1.1.1 9.9.9.9; do
    echo "=== \$ns ==="
    dig @\$ns google.com A +short
done

# Resolver inverso
dig -x 142.250.185.46

# Verificar DNSSEC
dig google.com +dnssec
\`\`\`

### Análisis de ARP y Capa 2

\`\`\`bash
# Ver tabla ARP
ip neigh show
arp -an

# Detectar IPs duplicadas
arping -D -I eth0 192.168.1.100

# Escaneo ARP de red
sudo arp-scan -l --interface=eth0

# Detectar cambios en ARP (MITM)
sudo arpwatch -i eth0
\`\`\`

### Debug de Routing

\`\`\`bash
# Ver tabla de rutas detallada
ip route show table all

# Verificar ruta hacia destino
ip route get 8.8.8.8

# Ver decisiones de enrutamiento
tracepath 8.8.8.8

# Cache de rutas
ip route show cache

# Verificar rutas OSPF (FRRouting)
sudo vtysh -c "show ip ospf route"
sudo vtysh -c "show ip ospf neighbor"
sudo vtysh -c "show ip ospf database"

# Verificar rutas BGP (FRRouting)
sudo vtysh -c "show ip bgp summary"
sudo vtysh -c "show ip bgp"
sudo vtysh -c "show ip bgp neighbors 192.168.1.2 advertised-routes"
\`\`\`

### Herramientas de Diagnóstico Adicionales

\`\`\`bash
# iperf3 - Medir ancho de banda
# Servidor
iperf3 -s

# Cliente (-t 30: duración 30s, -P 4: 4 streams paralelos para mayor throughput)
iperf3 -c 192.168.1.100 -t 30 -P 4

# Con UDP
iperf3 -c 192.168.1.100 -u -b 100M

# nload - Monitor de tráfico en tiempo real
nload eth0

# iftop - Top de conexiones por ancho de banda
sudo iftop -i eth0

# bmon - Monitor de ancho de banda
bmon

# nethogs - Tráfico por proceso
sudo nethogs eth0

# vnStat - Estadísticas de tráfico
vnstat -i eth0 -l   # Live
vnstat -i eth0 -d   # Diario
vnstat -i eth0 -m   # Mensual
\`\`\`

---

## Ejercicios Prácticos

### Ejercicio 1: Diseñar Red Empresarial

**Objetivo**: Diseñar una red para una empresa con 500 empleados en 3 pisos.

**Requisitos**:
- VLANs separadas por departamento
- Alta disponibilidad en core
- QoS para VoIP
- Segmentación de red para servidores

**Pasos**:
1. Dibuja la topología física y lógica
2. Define el esquema de direccionamiento IP
3. Planifica VLANs y sus propósitos
4. Configura VRRP para redundancia
5. Implementa QoS

### Ejercicio 2: Implementar BGP Multihoming

**Objetivo**: Configurar conexión a dos ISPs con BGP.

\`\`\`bash
# Simular con GNS3/EVE-NG o contenedores
# Configurar:
# - Router de borde con dos enlaces WAN
# - Políticas de preferencia (ISP primario)
# - Failover automático
# - Anuncio de prefijo propio
\`\`\`

### Ejercicio 3: Monitoreo de Red

**Objetivo**: Implementar stack de monitoreo completo.

1. Desplegar Prometheus + Grafana + SNMP Exporter
2. Configurar SNMP en dispositivos de red
3. Crear dashboards para:
   - Utilización de interfaces
   - Errores y descartes
   - Latencia a destinos críticos
4. Configurar alertas

### Ejercicio 4: Automatización con Ansible

**Objetivo**: Automatizar configuración de VLANs en múltiples switches.

1. Crear inventario de dispositivos
2. Desarrollar playbook para:
   - Crear VLANs
   - Asignar puertos
   - Configurar trunks
3. Implementar backup automático
4. Añadir validación post-cambio

### Ejercicio 5: Troubleshooting Challenge

**Escenario**: Los usuarios reportan lentitud intermitente al acceder a aplicaciones web internas.

**Investigar**:
1. Capturar tráfico con tcpdump/Wireshark
2. Analizar latencia con mtr
3. Verificar QoS y saturación de enlaces
4. Revisar logs de servidores y switches
5. Documentar hallazgos y solución

---

## 📚 Recursos Adicionales

### Documentación Oficial

- [FRRouting Documentation](https://docs.frrouting.org/)
- [Open vSwitch Documentation](https://docs.openvswitch.org/)
- [Linux Advanced Routing & Traffic Control](https://lartc.org/)
- [Ansible Network Modules](https://docs.ansible.com/ansible/latest/network/index.html)

### Herramientas Recomendadas

| Herramienta | Uso |
|-------------|-----|
| GNS3 | Simulador de redes |
| EVE-NG | Emulador de redes |
| Wireshark | Análisis de paquetes |
| Netbox | IPAM/DCIM |
| Oxidized | Backup de configuraciones |
| LibreNMS | Monitoreo SNMP |

### Certificaciones Recomendadas

| Certificación | Nivel | Enfoque |
|---------------|-------|---------|
| CCNA | Inicial | Fundamentos Cisco |
| CCNP Enterprise | Intermedio | Routing/Switching avanzado |
| CCIE | Experto | Diseño e implementación |
| Juniper JNCIA/JNCIS | Inicial-Intermedio | Junos |
| AWS Advanced Networking | Intermedio | Cloud networking |

### Libros Recomendados

- "TCP/IP Illustrated" - W. Richard Stevens
- "Network Warrior" - Gary A. Donahue
- "OSPF: Anatomy of an Internet Routing Protocol" - John Moy
- "BGP: Building Reliable Networks with the Border Gateway Protocol" - Iljitsch van Beijnum

---

## 🎯 Siguiente Paso

Una vez dominados estos conceptos avanzados, considera explorar:
- **Network Automation a escala**: Terraform, Pulumi para infraestructura
- **SD-WAN**: Redes WAN definidas por software
- **Zero Trust Networking**: Arquitectura de seguridad moderna
- **Intent-Based Networking**: Redes basadas en intención

**Recuerda**: La práctica constante en laboratorios es esencial. Utiliza simuladores como GNS3, EVE-NG o contenerizadores para experimentar sin riesgo.
