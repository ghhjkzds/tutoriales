# 🐳 Tutorial Completo de Docker

## Índice

1. [¿Qué es Docker?](#1-qué-es-docker)
2. [Instalación](#2-instalación)
3. [Conceptos fundamentales](#3-conceptos-fundamentales)
4. [Comandos básicos](#4-comandos-básicos)
5. [Imágenes Docker](#5-imágenes-docker)
6. [Dockerfile](#6-dockerfile)
7. [Volúmenes y persistencia de datos](#7-volúmenes-y-persistencia-de-datos)
8. [Redes en Docker](#8-redes-en-docker)
9. [Docker Compose](#9-docker-compose)
10. [Variables de entorno y configuración](#10-variables-de-entorno-y-configuración)
11. [Multi-stage builds](#11-multi-stage-builds)
12. [Docker Hub y registros privados](#12-docker-hub-y-registros-privados)
13. [Docker para desarrollo](#13-docker-para-desarrollo)
14. [Docker en producción](#14-docker-en-producción)
15. [Proyectos prácticos completos](#15-proyectos-prácticos-completos)
16. [Buenas prácticas](#16-buenas-prácticas)
17. [Solución de problemas comunes](#17-solución-de-problemas-comunes)

---

## 1. ¿Qué es Docker?

**Docker** es una plataforma de código abierto que permite empaquetar, distribuir y ejecutar aplicaciones dentro de **contenedores**. Un contenedor es una unidad ligera y portable que incluye todo lo necesario para ejecutar una aplicación: código, dependencias, librerías y configuración.

### ¿Por qué usar Docker?

| Ventaja | Descripción |
|---------|-------------|
| 📦 **Portabilidad** | "Funciona en mi máquina" deja de ser un problema |
| 🚀 **Rapidez** | Los contenedores arrancan en milisegundos |
| 🔒 **Aislamiento** | Cada contenedor tiene su propio entorno |
| ♻️ **Reproducibilidad** | El mismo entorno en dev, test y producción |
| 📉 **Eficiencia** | Consume menos recursos que una VM completa |
| 🔧 **Escalabilidad** | Fácil de escalar horizontal y verticalmente |

### Diferencia entre contenedores y máquinas virtuales

```
┌──────────────────────────────────────────────────────────────┐
│          MÁQUINA VIRTUAL              CONTENEDOR             │
│                                                              │
│  ┌──────────┐ ┌──────────┐     ┌────────┐ ┌────────┐        │
│  │  App A   │ │  App B   │     │ App A  │ │ App B  │        │
│  ├──────────┤ ├──────────┤     ├────────┤ ├────────┤        │
│  │  Libs    │ │  Libs    │     │ Libs   │ │ Libs   │        │
│  ├──────────┤ ├──────────┤     └────────┴─┴────────┘        │
│  │  OS      │ │  OS      │     ┌────────────────────┐        │
│  │  Guest   │ │  Guest   │     │   Docker Engine    │        │
│  ├──────────┴─┴──────────┤     ├────────────────────┤        │
│  │     Hypervisor        │     │     OS Host        │        │
│  ├───────────────────────┤     ├────────────────────┤        │
│  │      OS Host          │     │     Hardware       │        │
│  ├───────────────────────┤     └────────────────────┘        │
│  │      Hardware         │                                   │
│  └───────────────────────┘                                   │
└──────────────────────────────────────────────────────────────┘
```

| Característica | Máquina Virtual | Contenedor Docker |
|---|---|---|
| **Tamaño** | Gigabytes | Megabytes |
| **Arranque** | Minutos | Segundos/Milisegundos |
| **Aislamiento** | Completo (hardware virtual) | Proceso (kernel compartido) |
| **Overhead** | Alto | Mínimo |
| **OS** | OS completo por VM | Comparte kernel del host |

### Arquitectura de Docker

```
┌─────────────────────────────────────────────────────────┐
│                    Cliente Docker                        │
│              (docker CLI / Docker Desktop)               │
└──────────────────────────┬──────────────────────────────┘
                           │ REST API
                           ▼
┌─────────────────────────────────────────────────────────┐
│                   Docker Daemon (dockerd)                │
│                                                          │
│  ┌────────────┐  ┌────────────┐  ┌────────────────────┐ │
│  │ Contenedor │  │ Contenedor │  │      Imágenes       │ │
│  │    App A   │  │    App B   │  │  ubuntu:22.04       │ │
│  └────────────┘  └────────────┘  │  node:18-alpine     │ │
│                                  │  nginx:latest       │ │
│  ┌────────────────────────────┐  └────────────────────┘ │
│  │         Volúmenes          │                         │
│  └────────────────────────────┘                         │
│  ┌────────────────────────────┐                         │
│  │          Redes             │                         │
│  └────────────────────────────┘                         │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                   Docker Registry                        │
│                  (Docker Hub, ECR...)                    │
└─────────────────────────────────────────────────────────┘
```

---

## 2. Instalación

### Linux (Ubuntu/Debian)

```bash
# Actualizar repositorios
sudo apt-get update

# Instalar dependencias
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Añadir clave GPG oficial de Docker
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
    sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Añadir repositorio de Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Iniciar el servicio
sudo systemctl start docker
sudo systemctl enable docker

# Añadir usuario actual al grupo docker (evitar usar sudo)
sudo usermod -aG docker $USER
newgrp docker
```

### Linux (CentOS/RHEL/Fedora)

```bash
# Instalar yum-utils
sudo yum install -y yum-utils

# Añadir repositorio
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

# Instalar Docker
sudo yum install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Iniciar y habilitar
sudo systemctl start docker
sudo systemctl enable docker
```

### macOS

```bash
# Opción 1: Docker Desktop (recomendado)
# Descarga desde https://www.docker.com/products/docker-desktop/

# Opción 2: Homebrew
brew install --cask docker

# Opción 3: Solo CLI (sin GUI)
brew install docker
```

### Windows

1. Descarga **Docker Desktop** desde [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
2. Ejecuta el instalador
3. Habilita **WSL 2** cuando se solicite (recomendado)
4. Reinicia el sistema

### Verificar la instalación

```bash
# Versión de Docker
docker --version
# Docker version 24.0.7, build afdd53b

# Información completa del sistema
docker info

# Prueba con el contenedor "hello-world"
docker run hello-world
```

### Instalar Docker Compose (standalone)

```bash
# Linux - Descargar binario
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
    -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verificar
docker-compose --version
# Docker Compose version v2.23.0
```

---

## 3. Conceptos fundamentales

### Imágenes

Una **imagen** es una plantilla de solo lectura que contiene el sistema de archivos y la configuración necesaria para crear un contenedor. Se construye en capas (_layers_).

```
┌─────────────────────────────┐
│     Imagen: mi-app:1.0      │
├─────────────────────────────┤
│  Capa 4: COPY app/ /app/    │  ← Tu código
├─────────────────────────────┤
│  Capa 3: RUN npm install    │  ← Dependencias
├─────────────────────────────┤
│  Capa 2: WORKDIR /app       │  ← Directorio de trabajo
├─────────────────────────────┤
│  Capa 1: FROM node:18       │  ← Imagen base
└─────────────────────────────┘
```

### Contenedores

Un **contenedor** es una instancia en ejecución de una imagen. Es la capa de escritura sobre la imagen (copy-on-write).

```
Imagen (solo lectura)  +  Capa de escritura  =  Contenedor
```

### Volúmenes

Los **volúmenes** permiten persistir datos más allá del ciclo de vida de un contenedor y compartir datos entre contenedores.

### Redes

Las **redes** permiten la comunicación entre contenedores y con el mundo exterior de forma controlada.

### Registros

Un **registro** (registry) es un repositorio de imágenes. El más conocido es **Docker Hub**.

---

## 4. Comandos básicos

### Gestión de contenedores

```bash
# Ejecutar un contenedor
docker run nginx

# Ejecutar en segundo plano (detached)
docker run -d nginx

# Ejecutar con nombre personalizado
docker run -d --name mi-nginx nginx

# Ejecutar con mapeo de puertos (host:contenedor)
docker run -d -p 8080:80 --name web nginx

# Ejecutar con terminal interactiva
docker run -it ubuntu bash

# Ejecutar comando en contenedor existente
docker exec -it mi-nginx bash

# Ver contenedores en ejecución
docker ps

# Ver todos los contenedores (incluidos detenidos)
docker ps -a

# Ver solo los IDs
docker ps -q

# Detener un contenedor
docker stop mi-nginx

# Detener forzosamente
docker kill mi-nginx

# Iniciar un contenedor detenido
docker start mi-nginx

# Reiniciar
docker restart mi-nginx

# Eliminar un contenedor detenido
docker rm mi-nginx

# Eliminar un contenedor en ejecución (forzar)
docker rm -f mi-nginx

# Eliminar todos los contenedores detenidos
docker container prune

# Ver logs de un contenedor
docker logs mi-nginx

# Ver logs en tiempo real
docker logs -f mi-nginx

# Ver últimas N líneas de logs
docker logs --tail 100 mi-nginx

# Ver estadísticas de recursos
docker stats

# Ver estadísticas de un contenedor específico
docker stats mi-nginx

# Inspeccionar configuración completa
docker inspect mi-nginx

# Ver procesos dentro del contenedor
docker top mi-nginx

# Copiar archivo del host al contenedor
docker cp archivo.txt mi-nginx:/ruta/destino/

# Copiar archivo del contenedor al host
docker cp mi-nginx:/ruta/archivo.txt ./
```

### Ejemplo práctico: servidor web Nginx

```bash
# Lanzar Nginx accesible en http://localhost:8080
docker run -d \
  --name mi-web \
  -p 8080:80 \
  --restart unless-stopped \
  nginx:alpine

# Verificar que funciona
curl http://localhost:8080

# Ver los logs
docker logs mi-web

# Acceder al shell del contenedor
docker exec -it mi-web sh

# Detener y eliminar
docker stop mi-web && docker rm mi-web
```

### Ejemplo práctico: base de datos PostgreSQL

```bash
# Lanzar PostgreSQL con contraseña y base de datos
docker run -d \
  --name mi-postgres \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secreto \
  -e POSTGRES_DB=miapp \
  -p 5432:5432 \
  postgres:15-alpine

# Conectarse con psql
docker exec -it mi-postgres psql -U admin -d miapp

# Ejecutar una consulta directamente
docker exec -it mi-postgres psql -U admin -d miapp -c "SELECT version();"
```

---

## 5. Imágenes Docker

### Gestión de imágenes

```bash
# Descargar una imagen
docker pull ubuntu:22.04

# Descargar imagen con tag específico
docker pull node:18-alpine

# Listar imágenes locales
docker images

# Listar imágenes con formato personalizado
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Buscar imágenes en Docker Hub
docker search nginx

# Buscar con filtros (imagen oficial con más de 100 estrellas)
docker search --filter is-official=true --filter stars=100 nginx

# Eliminar una imagen
docker rmi nginx:latest

# Eliminar imágenes sin usar
docker image prune

# Eliminar TODAS las imágenes sin usar (incluyendo las no referenciadas)
docker image prune -a

# Ver historial de capas de una imagen
docker history node:18-alpine

# Inspeccionar una imagen
docker inspect nginx:latest

# Etiquetar una imagen
docker tag mi-app:latest mi-usuario/mi-app:1.0.0

# Guardar imagen a archivo tar
docker save -o mi-app.tar mi-app:latest

# Cargar imagen desde archivo tar
docker load -i mi-app.tar
```

### Capas de imágenes

```bash
# Ver las capas de una imagen y su tamaño
docker history --no-trunc node:18-alpine

# IMAGE          CREATED        CREATED BY                    SIZE
# sha256:abc...  2 weeks ago    /bin/sh -c #(nop)  CMD [...]  0B
# sha256:def...  2 weeks ago    /bin/sh -c apk add --no-cache  45.2MB
# sha256:ghi...  2 weeks ago    /bin/sh -c #(nop) COPY ...    1.23kB
```

---

## 6. Dockerfile

El **Dockerfile** es el archivo de texto que contiene las instrucciones para construir una imagen Docker.

### Instrucciones principales

| Instrucción | Descripción |
|-------------|-------------|
| `FROM` | Imagen base |
| `RUN` | Ejecuta comandos durante la construcción |
| `COPY` | Copia archivos del host a la imagen |
| `ADD` | Como COPY pero también descomprime tarballs y admite URLs |
| `WORKDIR` | Establece el directorio de trabajo |
| `ENV` | Define variables de entorno |
| `ARG` | Define argumentos de construcción |
| `EXPOSE` | Declara el puerto que usa la aplicación |
| `CMD` | Comando por defecto al iniciar el contenedor |
| `ENTRYPOINT` | Punto de entrada del contenedor |
| `VOLUME` | Declara puntos de montaje |
| `USER` | Usuario con el que se ejecuta el contenedor |
| `LABEL` | Añade metadatos a la imagen |
| `HEALTHCHECK` | Define cómo verificar la salud del contenedor |

### Ejemplo 1: Aplicación Node.js

```dockerfile
# Dockerfile para una app Node.js

# Imagen base oficial de Node.js versión 18 con Alpine Linux (más ligera)
FROM node:18-alpine

# Metadatos
LABEL maintainer="tu@email.com"
LABEL version="1.0"
LABEL description="Mi aplicación Node.js"

# Directorio de trabajo dentro del contenedor
WORKDIR /app

# Copiar primero los archivos de dependencias (aprovecha caché)
COPY package.json package-lock.json ./

# Instalar dependencias (esta capa se cachea si package.json no cambia)
RUN npm ci --only=production

# Copiar el resto del código fuente
COPY . .

# Exponer el puerto que usa la aplicación
EXPOSE 3000

# Crear usuario no-root para mayor seguridad
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Comando para iniciar la aplicación
CMD ["node", "src/index.js"]
```

```bash
# Construir la imagen
docker build -t mi-node-app:1.0 .

# Construir con argumento personalizado
docker build --build-arg NODE_ENV=production -t mi-node-app:prod .

# Ejecutar la imagen construida
docker run -d -p 3000:3000 --name mi-app mi-node-app:1.0

# Acceder a la aplicación
curl http://localhost:3000
```

### Ejemplo 2: Aplicación Python/Flask

```dockerfile
# Dockerfile para una app Flask

FROM python:3.11-slim

WORKDIR /app

# Copiar solo los requisitos primero (optimización de caché)
COPY requirements.txt .

# Instalar dependencias del sistema y de Python
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
    && pip install --no-cache-dir -r requirements.txt \
    && apt-get purge -y --auto-remove gcc \
    && rm -rf /var/lib/apt/lists/*

# Copiar código fuente
COPY . .

# Variable de entorno para Flask
ENV FLASK_APP=app.py
ENV FLASK_ENV=production

EXPOSE 5000

# Usuario no-root
RUN useradd --create-home appuser
USER appuser

CMD ["flask", "run", "--host=0.0.0.0"]
```

**Archivo `requirements.txt`:**
```
Flask==3.0.0
gunicorn==21.2.0
```

**Archivo `app.py`:**
```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def home():
    return jsonify({"message": "¡Hola desde Docker!", "status": "ok"})

@app.route('/health')
def health():
    return jsonify({"status": "healthy"})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

```bash
# Construir y ejecutar
docker build -t mi-flask-app .
docker run -d -p 5000:5000 --name flask-app mi-flask-app
curl http://localhost:5000
# {"message": "¡Hola desde Docker!", "status": "ok"}
```

### Ejemplo 3: Aplicación Java/Spring Boot

```dockerfile
# Dockerfile para una aplicación Spring Boot

FROM eclipse-temurin:17-jdk-alpine AS builder

WORKDIR /app

# Copiar archivos de Maven
COPY mvnw pom.xml ./
COPY .mvn .mvn

# Descargar dependencias (cacheable)
RUN ./mvnw dependency:go-offline -B

# Copiar código fuente y compilar
COPY src ./src
RUN ./mvnw package -DskipTests

# --- Segunda etapa: imagen de ejecución ---
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

# Copiar solo el JAR compilado
COPY --from=builder /app/target/*.jar app.jar

# Puerto por defecto de Spring Boot
EXPOSE 8080

# Usuario no-root
RUN addgroup -S spring && adduser -S spring -G spring
USER spring

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Ejemplo 4: Aplicación estática con Nginx

```dockerfile
# Dockerfile para servir una web estática con Nginx

FROM nginx:alpine

# Eliminar la configuración por defecto
RUN rm /etc/nginx/conf.d/default.conf

# Copiar configuración personalizada
COPY nginx.conf /etc/nginx/conf.d/

# Copiar archivos estáticos
COPY dist/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

**Archivo `nginx.conf`:**
```nginx
server {
    listen 80;
    server_name localhost;
    root /usr/share/nginx/html;
    index index.html;

    # Compresión gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;

    # Soporte para Single Page Applications
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache para assets estáticos
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

### .dockerignore

El archivo `.dockerignore` funciona como `.gitignore` pero para Docker. Excluye archivos y directorios del contexto de construcción:

```dockerignore
# Dependencias
node_modules/
vendor/
__pycache__/
*.pyc
.venv/
target/

# Git
.git/
.gitignore

# Logs
*.log
logs/

# Variables de entorno con secretos
.env
.env.local
.env.*.local

# Archivos de IDE
.idea/
.vscode/
*.swp

# Archivos de test
**/*.test.js
**/*.spec.js
coverage/

# Documentación
README.md
docs/

# Docker
Dockerfile
docker-compose*.yml
.dockerignore
```

---

## 7. Volúmenes y persistencia de datos

### Tipos de almacenamiento

```
┌─────────────────────────────────────────────────────┐
│                    Docker Host                       │
│                                                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │
│  │  Volumen    │  │Bind Mount   │  │  tmpfs      │  │
│  │  Gestionado │  │ Directorio  │  │  (memoria)  │  │
│  │  por Docker │  │  del host   │  │             │  │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  │
│         │                │                │          │
│         └────────────────┼────────────────┘          │
│                          │                           │
│                    ┌─────▼─────┐                     │
│                    │Contenedor │                     │
│                    └───────────┘                     │
└─────────────────────────────────────────────────────┘
```

### Volúmenes gestionados por Docker

```bash
# Crear un volumen
docker volume create mis-datos

# Listar volúmenes
docker volume ls

# Inspeccionar un volumen
docker volume inspect mis-datos

# Eliminar un volumen
docker volume rm mis-datos

# Eliminar volúmenes sin usar
docker volume prune

# Usar un volumen en un contenedor
docker run -d \
  --name mi-postgres \
  -v mis-datos:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secreto \
  postgres:15

# Ver dónde se almacenan los datos en el host
docker volume inspect mis-datos
# "Mountpoint": "/var/lib/docker/volumes/mis-datos/_data"
```

### Bind Mounts

Montan un directorio específico del host en el contenedor:

```bash
# Montar directorio actual en el contenedor (desarrollo)
docker run -d \
  --name mi-app-dev \
  -v $(pwd)/src:/app/src \
  -p 3000:3000 \
  mi-node-app

# Montar archivo específico (solo lectura)
docker run -d \
  --name nginx \
  -v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ro \
  -p 80:80 \
  nginx

# Montar con ruta absoluta
docker run -d \
  -v /home/usuario/datos:/data \
  ubuntu
```

### tmpfs Mounts (solo en memoria)

```bash
# Montar en memoria (datos no se persisten)
docker run -d \
  --name mi-app \
  --tmpfs /tmp:rw,noexec,nosuid,size=100m \
  mi-app-imagen
```

### Ejemplo práctico: Base de datos con datos persistentes

```bash
# Crear volumen para los datos
docker volume create postgres-data

# Lanzar PostgreSQL con volumen persistente
docker run -d \
  --name postgres-db \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secreto123 \
  -e POSTGRES_DB=produccion \
  -v postgres-data:/var/lib/postgresql/data \
  -p 5432:5432 \
  --restart unless-stopped \
  postgres:15-alpine

# Los datos sobreviven al reinicio del contenedor
docker stop postgres-db
docker start postgres-db
# Los datos siguen ahí

# Hacer backup del volumen
docker run --rm \
  -v postgres-data:/datos \
  -v $(pwd):/backup \
  alpine tar czf /backup/postgres-backup.tar.gz -C /datos .

# Restaurar backup
docker run --rm \
  -v postgres-data:/datos \
  -v $(pwd):/backup \
  alpine tar xzf /backup/postgres-backup.tar.gz -C /datos
```

---

## 8. Redes en Docker

### Tipos de redes

| Tipo | Descripción | Uso |
|------|-------------|-----|
| `bridge` | Red virtual privada (por defecto) | Contenedores en el mismo host |
| `host` | Comparte la red del host | Alta performance, sin aislamiento |
| `none` | Sin acceso a red | Contenedores aislados |
| `overlay` | Red entre múltiples hosts | Docker Swarm |
| `macvlan` | Dirección MAC propia | Integración con red física |

### Comandos de redes

```bash
# Ver redes disponibles
docker network ls

# Crear una red bridge personalizada
docker network create mi-red

# Crear red con subred y gateway específicos
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  --gateway 172.20.0.1 \
  mi-red-custom

# Inspeccionar una red
docker network inspect mi-red

# Conectar un contenedor a una red
docker network connect mi-red mi-contenedor

# Desconectar
docker network disconnect mi-red mi-contenedor

# Eliminar red
docker network rm mi-red

# Eliminar redes sin usar
docker network prune
```

### Comunicación entre contenedores

```bash
# Crear una red personalizada
docker network create app-network

# Lanzar base de datos en la red
docker run -d \
  --name db \
  --network app-network \
  -e POSTGRES_PASSWORD=secreto \
  postgres:15-alpine

# Lanzar aplicación en la misma red
# La app puede acceder a la DB usando el hostname "db"
docker run -d \
  --name api \
  --network app-network \
  -e DATABASE_URL=postgresql://postgres:secreto@db:5432/postgres \
  -p 3000:3000 \
  mi-api-imagen

# Verificar conectividad
docker exec api ping db
docker exec api curl http://db:5432
```

> 💡 **Importante**: En redes bridge personalizadas, los contenedores se pueden resolver por **nombre**. En la red bridge por defecto (docker0), solo por IP.

### Publicar puertos

```bash
# Mapear puerto del host al contenedor
docker run -p 8080:80 nginx          # localhost:8080 → contenedor:80

# Múltiples puertos
docker run -p 80:80 -p 443:443 nginx

# Solo accesible desde una IP específica
docker run -p 127.0.0.1:8080:80 nginx  # Solo desde localhost

# Puerto aleatorio del host
docker run -p 80 nginx               # Docker elige el puerto del host

# Ver el puerto asignado
docker port mi-contenedor 80
```

---

## 9. Docker Compose

**Docker Compose** permite definir y gestionar aplicaciones multi-contenedor con un archivo YAML. Es ideal para entornos de desarrollo y para aplicaciones que necesitan múltiples servicios.

### Estructura básica del archivo docker-compose.yml

```yaml
version: '3.8'

services:
  nombre-servicio:
    image: imagen:tag          # O usa 'build' para construir
    build:
      context: .               # Directorio con el Dockerfile
      dockerfile: Dockerfile
    ports:
      - "host:contenedor"
    environment:
      - VARIABLE=valor
    volumes:
      - tipo-volumen:/ruta
    networks:
      - nombre-red
    depends_on:
      - otro-servicio
    restart: unless-stopped

volumes:
  tipo-volumen:

networks:
  nombre-red:
    driver: bridge
```

### Ejemplo 1: Stack completo (API + Base de datos + Redis)

```yaml
# docker-compose.yml
version: '3.8'

services:
  # Base de datos PostgreSQL
  db:
    image: postgres:15-alpine
    container_name: postgres-db
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secreto123
      POSTGRES_DB: miapp
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql  # Script de inicialización
    ports:
      - "5432:5432"
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d miapp"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # Cache Redis
  redis:
    image: redis:7-alpine
    container_name: redis-cache
    command: redis-server --requirepass ${REDIS_PASSWORD}
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - backend
    healthcheck:
      test: ["CMD", "redis-cli", "--no-auth-warning", "-a", "${REDIS_PASSWORD}", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # API Backend
  api:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: api-backend
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://admin:${POSTGRES_PASSWORD}@db:5432/miapp
      - REDIS_URL=redis://:${REDIS_PASSWORD}@redis:6379
      - PORT=3000
    ports:
      - "3000:3000"
    volumes:
      - ./backend/src:/app/src  # Hot reload en desarrollo
    networks:
      - frontend
      - backend
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    restart: unless-stopped

  # Frontend Nginx
  web:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: web-frontend
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/ssl:/etc/nginx/ssl:ro
    networks:
      - frontend
    depends_on:
      - api
    restart: unless-stopped

volumes:
  postgres-data:
    driver: local
  redis-data:
    driver: local

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true  # No accesible desde fuera
```

### Ejemplo 2: WordPress con MySQL

```yaml
# docker-compose.yml
version: '3.8'

services:
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_password
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - wordpress-net
    restart: always

  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_password
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress-data:/var/www/html
    networks:
      - wordpress-net
    depends_on:
      - db
    restart: always

volumes:
  mysql-data:
  wordpress-data:

networks:
  wordpress-net:
```

### Ejemplo 3: Stack de monitorización (Prometheus + Grafana)

```yaml
# docker-compose.yml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
      - prometheus-data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
    ports:
      - "9090:9090"
    networks:
      - monitoring
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=grafana123
      - GF_USERS_ALLOW_SIGN_UP=false
    volumes:
      - grafana-data:/var/lib/grafana
    ports:
      - "3000:3000"
    networks:
      - monitoring
    depends_on:
      - prometheus
    restart: unless-stopped

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
    ports:
      - "9100:9100"
    networks:
      - monitoring
    restart: unless-stopped

volumes:
  prometheus-data:
  grafana-data:

networks:
  monitoring:
    driver: bridge
```

### Comandos de Docker Compose

```bash
# Iniciar todos los servicios
docker compose up

# Iniciar en segundo plano
docker compose up -d

# Construir imágenes antes de iniciar
docker compose up -d --build

# Iniciar solo un servicio específico
docker compose up -d api

# Ver estado de los servicios
docker compose ps

# Ver logs de todos los servicios
docker compose logs

# Ver logs en tiempo real
docker compose logs -f

# Ver logs de un servicio específico
docker compose logs -f api

# Detener todos los servicios
docker compose stop

# Detener y eliminar contenedores
docker compose down

# Detener, eliminar contenedores y volúmenes
docker compose down -v

# Detener y eliminar también las imágenes
docker compose down --rmi all

# Ejecutar comando en un servicio
docker compose exec api bash
docker compose exec db psql -U admin -d miapp

# Escalar un servicio (múltiples instancias)
docker compose up -d --scale api=3

# Ver configuración resultante (con variables sustituidas)
docker compose config

# Reiniciar un servicio
docker compose restart api

# Reconstruir imagen de un servicio
docker compose build api

# Forzar recreación de contenedores
docker compose up -d --force-recreate
```

### Archivos de composición por entorno

```bash
# Estructura recomendada
├── docker-compose.yml           # Configuración base
├── docker-compose.override.yml  # Sobrescrituras para desarrollo (auto-cargado)
├── docker-compose.prod.yml      # Configuración de producción
└── docker-compose.test.yml      # Configuración de tests
```

**docker-compose.yml** (base):
```yaml
version: '3.8'
services:
  api:
    image: mi-api:${TAG:-latest}
    environment:
      - NODE_ENV=${NODE_ENV:-production}
    networks:
      - app-net

networks:
  app-net:
```

**docker-compose.override.yml** (desarrollo, auto-cargado):
```yaml
version: '3.8'
services:
  api:
    build: .
    volumes:
      - ./src:/app/src   # Hot reload
    environment:
      - NODE_ENV=development
      - DEBUG=*
    ports:
      - "3000:3000"
```

**docker-compose.prod.yml** (producción):
```yaml
version: '3.8'
services:
  api:
    restart: always
    deploy:
      replicas: 3
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
```

```bash
# Usar en desarrollo (auto-carga override)
docker compose up -d

# Usar en producción
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

---

## 10. Variables de entorno y configuración

### Archivo .env

```bash
# .env
POSTGRES_USER=admin
POSTGRES_PASSWORD=secreto123
POSTGRES_DB=miapp
API_PORT=3000
NODE_ENV=development
```

```yaml
# docker-compose.yml usando .env
version: '3.8'
services:
  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${POSTGRES_DB}

  api:
    image: mi-api
    ports:
      - "${API_PORT}:3000"
    env_file:
      - .env         # Cargar todas las variables del archivo
      - .env.local   # Sobreescribir con variables locales
```

### Pasar variables de entorno del host

```bash
# Pasar variable del host
export MI_SECRETO=valor-secreto
docker run -e MI_SECRETO mi-imagen

# Pasar todas las variables del host con el mismo nombre
docker run --env-file .env mi-imagen
```

### Secretos con Docker Secrets (Docker Swarm)

```yaml
version: '3.8'
services:
  db:
    image: postgres:15
    secrets:
      - db_password
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

---

## 11. Multi-stage builds

Los **multi-stage builds** permiten usar múltiples instrucciones `FROM` en un Dockerfile, copiando solo los artefactos necesarios entre etapas. Esto reduce drásticamente el tamaño de la imagen final.

### Ejemplo: Aplicación React (build + producción)

```dockerfile
# Etapa 1: Build de la aplicación React
FROM node:18-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
RUN npm run build

# Etapa 2: Imagen de producción con Nginx
FROM nginx:alpine AS production

# Copiar solo los archivos compilados de la etapa anterior
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

```bash
# Construir
docker build -t mi-react-app:prod .

# Comparar tamaños
# node:18-alpine:           ~180MB
# nginx:alpine:              ~23MB
# mi-react-app:prod:         ~30MB (solo nginx + archivos compilados)
```

### Ejemplo: Aplicación Go (compilación estática)

```dockerfile
# Etapa 1: Compilar la aplicación Go
FROM golang:1.21-alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

# Compilar binario estático (sin dependencias externas)
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o servidor ./cmd/servidor

# Etapa 2: Imagen mínima (scratch = imagen vacía)
FROM scratch

# Copiar certificados SSL
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Copiar solo el binario compilado
COPY --from=builder /app/servidor /servidor

EXPOSE 8080
ENTRYPOINT ["/servidor"]
```

```bash
# Imagen resultante: ~10-15MB (solo el binario y certificados SSL)
docker build -t mi-api-go:prod .
docker images mi-api-go:prod
# REPOSITORY    TAG    SIZE
# mi-api-go     prod   12.4MB
```

### Ejemplo: Java Spring Boot optimizado

```dockerfile
# Etapa 1: Extraer dependencias del JAR (para mejor cacheo)
FROM eclipse-temurin:17-jre-alpine AS extractor
WORKDIR /app
COPY target/*.jar app.jar
RUN java -Djarmode=layertools -jar app.jar extract

# Etapa 2: Imagen final con capas separadas
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

RUN addgroup -S spring && adduser -S spring -G spring

# Copiar capas en orden de menor a mayor cambio frecuente
COPY --from=extractor /app/dependencies/ ./
COPY --from=extractor /app/spring-boot-loader/ ./
COPY --from=extractor /app/snapshot-dependencies/ ./
COPY --from=extractor /app/application/ ./

USER spring
EXPOSE 8080

ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

---

## 12. Docker Hub y registros privados

### Docker Hub

```bash
# Iniciar sesión
docker login

# Iniciar sesión con credenciales
docker login -u tu-usuario -p tu-token

# Etiquetar imagen para Docker Hub
docker tag mi-app:latest tu-usuario/mi-app:latest
docker tag mi-app:latest tu-usuario/mi-app:1.0.0

# Publicar en Docker Hub
docker push tu-usuario/mi-app:latest
docker push tu-usuario/mi-app:1.0.0

# Descargar imagen publicada
docker pull tu-usuario/mi-app:latest

# Cerrar sesión
docker logout
```

### Registro privado (self-hosted)

```bash
# Lanzar registro privado local
docker run -d \
  -p 5000:5000 \
  --name mi-registry \
  -v registry-data:/var/lib/registry \
  registry:2

# Etiquetar imagen para el registro privado
docker tag mi-app:latest localhost:5000/mi-app:latest

# Publicar en el registro privado
docker push localhost:5000/mi-app:latest

# Descargar del registro privado
docker pull localhost:5000/mi-app:latest

# Listar imágenes en el registro
curl http://localhost:5000/v2/_catalog
# {"repositories":["mi-app"]}

# Listar tags de una imagen
curl http://localhost:5000/v2/mi-app/tags/list
# {"name":"mi-app","tags":["latest","1.0.0"]}
```

### GitHub Container Registry (GHCR)

```bash
# Autenticarse con token de GitHub
echo $GITHUB_TOKEN | docker login ghcr.io -u tu-usuario --password-stdin

# Etiquetar imagen
docker tag mi-app:latest ghcr.io/tu-usuario/mi-app:latest

# Publicar
docker push ghcr.io/tu-usuario/mi-app:latest
```

---

## 13. Docker para desarrollo

### Entorno de desarrollo con hot reload

```yaml
# docker-compose.dev.yml
version: '3.8'

services:
  api:
    build:
      context: .
      target: development   # Etapa específica del Dockerfile
    volumes:
      - .:/app
      - /app/node_modules   # Excluir node_modules del bind mount
    ports:
      - "3000:3000"
      - "9229:9229"   # Puerto para debugger de Node.js
    environment:
      - NODE_ENV=development
    command: npm run dev   # Comando con hot reload (nodemon, etc.)
```

**Dockerfile con etapa de desarrollo:**
```dockerfile
# Etapa de desarrollo
FROM node:18-alpine AS development

WORKDIR /app
COPY package*.json ./
RUN npm install   # Incluye devDependencies

COPY . .
EXPOSE 3000 9229
CMD ["npm", "run", "dev"]

# Etapa de producción
FROM node:18-alpine AS production

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
EXPOSE 3000
CMD ["node", "src/index.js"]
```

### Herramientas de desarrollo en contenedores

```bash
# Lanzar una base de datos solo para desarrollo
docker run -d \
  --name dev-postgres \
  -e POSTGRES_PASSWORD=dev \
  -e POSTGRES_DB=desarrollo \
  -p 5432:5432 \
  postgres:15-alpine

# Lanzar un servidor de email de desarrollo (Mailpit)
docker run -d \
  --name dev-mail \
  -p 1025:1025 \
  -p 8025:8025 \
  axllent/mailpit

# Lanzar Adminer (gestor de bases de datos web)
docker run -d \
  --name adminer \
  -p 8080:8080 \
  adminer

# Lanzar Redis Commander (GUI para Redis)
docker run -d \
  --name redis-commander \
  -p 8081:8081 \
  -e REDIS_HOSTS=local:redis:6379 \
  rediscommander/redis-commander
```

### Entorno de desarrollo completo

```yaml
# docker-compose.yml para desarrollo
version: '3.8'

services:
  app:
    build:
      context: .
      target: development
    volumes:
      - .:/app
      - node_modules:/app/node_modules
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://dev:dev@db:5432/devdb
      REDIS_URL: redis://redis:6379
      SMTP_HOST: mailpit
      SMTP_PORT: 1025
    depends_on:
      - db
      - redis
      - mailpit

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: dev
      POSTGRES_DB: devdb
    ports:
      - "5432:5432"
    volumes:
      - postgres-dev:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  mailpit:
    image: axllent/mailpit
    ports:
      - "1025:1025"   # SMTP
      - "8025:8025"   # Web UI

  adminer:
    image: adminer
    ports:
      - "8080:8080"

volumes:
  postgres-dev:
  node_modules:
```

---

## 14. Docker en producción

### Límites de recursos

```bash
# Limitar memoria y CPU
docker run -d \
  --name mi-app \
  --memory="512m" \
  --memory-swap="1g" \
  --cpus="1.5" \
  mi-imagen

# Ver estadísticas de recursos
docker stats mi-app
```

```yaml
# docker-compose.yml con límites
version: '3.8'
services:
  api:
    image: mi-api
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 128M
```

### Health checks

```dockerfile
# En el Dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
```

```yaml
# En docker-compose.yml
services:
  api:
    image: mi-api
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

```bash
# Ver estado de salud
docker inspect --format='{{.State.Health.Status}}' mi-contenedor
# healthy / unhealthy / starting
```

### Política de reinicio

```bash
# Opciones de restart:
# no            - No reiniciar nunca (por defecto)
# always        - Siempre reiniciar
# unless-stopped - Reiniciar excepto si se detuvo manualmente
# on-failure[:N] - Reiniciar solo si falla (opcionalmente N veces máx)

docker run -d --restart unless-stopped nginx
docker run -d --restart on-failure:5 mi-app
```

### Logging en producción

```bash
# Ver drivers de logging disponibles
docker info --format '{{.LoggingDriver}}'

# Usar driver de logging
docker run -d \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  mi-app

# Enviar logs a syslog
docker run -d \
  --log-driver syslog \
  --log-opt syslog-address=udp://logs.miempresa.com:514 \
  mi-app
```

```yaml
# docker-compose.yml con logging configurado
version: '3.8'
services:
  api:
    image: mi-api
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "5"
```

### Seguridad en producción

```dockerfile
# 1. Usar imagen base mínima
FROM alpine:3.18    # En vez de ubuntu

# 2. No ejecutar como root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# 3. No almacenar secretos en la imagen
# MAL: ENV API_KEY=mi-secreto
# BIEN: Usar variables de entorno al ejecutar o Docker Secrets

# 4. Usar COPY en vez de ADD cuando sea posible
COPY . .

# 5. Imagen de solo lectura
# docker run --read-only mi-imagen
```

```bash
# Ejecutar contenedor con filesystem de solo lectura
docker run -d \
  --read-only \
  --tmpfs /tmp \
  --tmpfs /run \
  mi-app

# No permitir escalada de privilegios
docker run -d \
  --security-opt=no-new-privileges:true \
  mi-app

# Limitar capabilities
docker run -d \
  --cap-drop ALL \
  --cap-add NET_BIND_SERVICE \
  mi-app
```

---

## 15. Proyectos prácticos completos

### Proyecto 1: API REST con Node.js + PostgreSQL + Redis

**Estructura del proyecto:**
```
mi-api/
├── src/
│   ├── index.js
│   ├── db.js
│   └── cache.js
├── Dockerfile
├── .dockerignore
├── docker-compose.yml
├── docker-compose.prod.yml
└── package.json
```

**src/index.js:**
```javascript
const express = require('express');
const { Pool } = require('pg');
const redis = require('redis');

const app = express();
app.use(express.json());

// Conexión a PostgreSQL
const pool = new Pool({
  connectionString: process.env.DATABASE_URL
});

// Conexión a Redis
const redisClient = redis.createClient({
  url: process.env.REDIS_URL
});
redisClient.connect();

// Endpoint de salud
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date() });
});

// Obtener usuarios (con caché)
app.get('/usuarios', async (req, res) => {
  try {
    // Intentar desde caché
    const cached = await redisClient.get('usuarios');
    if (cached) {
      return res.json({ source: 'cache', data: JSON.parse(cached) });
    }

    // Obtener de la base de datos
    const result = await pool.query('SELECT * FROM usuarios ORDER BY id');
    await redisClient.setEx('usuarios', 60, JSON.stringify(result.rows));

    res.json({ source: 'database', data: result.rows });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

// Crear usuario
app.post('/usuarios', async (req, res) => {
  const { nombre, email } = req.body;
  try {
    const result = await pool.query(
      'INSERT INTO usuarios (nombre, email) VALUES ($1, $2) RETURNING *',
      [nombre, email]
    );
    await redisClient.del('usuarios');  // Invalidar caché
    res.status(201).json(result.rows[0]);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Servidor en puerto ${PORT}`));
```

**Dockerfile:**
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY src/ ./src/

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

CMD ["node", "src/index.js"]
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgresql://admin:${POSTGRES_PASSWORD}@db:5432/apidb
      REDIS_URL: redis://:${REDIS_PASSWORD}@redis:6379
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    networks:
      - app-net
    restart: unless-stopped

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: apidb
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - app-net
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d apidb"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    command: redis-server --requirepass ${REDIS_PASSWORD}
    volumes:
      - redis-data:/data
    networks:
      - app-net
    healthcheck:
      test: ["CMD", "redis-cli", "--no-auth-warning", "-a", "${REDIS_PASSWORD}", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

volumes:
  postgres-data:
  redis-data:

networks:
  app-net:
    driver: bridge
```

**init.sql:**
```sql
CREATE TABLE IF NOT EXISTS usuarios (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    creado_en TIMESTAMP DEFAULT NOW()
);

INSERT INTO usuarios (nombre, email) VALUES
    ('Ana García', 'ana@example.com'),
    ('Carlos López', 'carlos@example.com');
```

```bash
# Crear archivo .env con las credenciales (no commitear a git)
cat > .env <<EOF
POSTGRES_PASSWORD=secreto123
REDIS_PASSWORD=redis123
EOF

# Iniciar todo el stack
docker compose up -d --build

# Probar la API
curl http://localhost:3000/health
curl http://localhost:3000/usuarios
curl -X POST http://localhost:3000/usuarios \
  -H "Content-Type: application/json" \
  -d '{"nombre": "María Pérez", "email": "maria@example.com"}'
```

---

### Proyecto 2: Reverse Proxy con Nginx + múltiples servicios

```yaml
# docker-compose.yml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - app1
      - app2
    networks:
      - frontend

  app1:
    image: nginx:alpine
    volumes:
      - ./app1:/usr/share/nginx/html:ro
    networks:
      - frontend

  app2:
    image: nginx:alpine
    volumes:
      - ./app2:/usr/share/nginx/html:ro
    networks:
      - frontend

networks:
  frontend:
    driver: bridge
```

**nginx/nginx.conf:**
```nginx
events {
    worker_connections 1024;
}

http {
    upstream app1 {
        server app1:80;
    }
    upstream app2 {
        server app2:80;
    }

    server {
        listen 80;

        location /app1/ {
            proxy_pass http://app1/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }

        location /app2/ {
            proxy_pass http://app2/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}
```

---

### Proyecto 3: Pipeline CI/CD con Docker

**Dockerfile de múltiples etapas para CI/CD:**
```dockerfile
# Etapa base
FROM node:18-alpine AS base
WORKDIR /app
COPY package*.json ./
RUN npm ci

# Etapa de tests
FROM base AS test
COPY . .
RUN npm run lint
RUN npm run test

# Etapa de build
FROM base AS builder
COPY . .
RUN npm run build

# Etapa de producción
FROM node:18-alpine AS production
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY --from=builder /app/dist ./dist
USER node
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

```bash
# Ejecutar tests en CI
docker build --target test -t mi-app:test .
docker run --rm mi-app:test npm test

# Construir imagen de producción solo si los tests pasan
docker build --target production -t mi-app:prod .
```

---

## 16. Buenas prácticas

### 1. Optimizar el tamaño de las imágenes

```dockerfile
# ✅ BIEN: Combinar comandos RUN y limpiar en la misma capa
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        curl \
        git \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

# ❌ MAL: Capas separadas que se quedan en la imagen
RUN apt-get update
RUN apt-get install -y curl git
RUN rm -rf /var/lib/apt/lists/*
```

```dockerfile
# ✅ BIEN: Usar Alpine o Distroless cuando sea posible
FROM node:18-alpine          # ~180MB
FROM gcr.io/distroless/nodejs18  # ~60MB

# ❌ MAL: Imagen base completa
FROM node:18                 # ~910MB
```

### 2. Aprovechar la caché de capas

```dockerfile
# ✅ BIEN: Copiar package.json primero, código después
COPY package.json package-lock.json ./
RUN npm ci
COPY . .    # Solo invalida caché cuando cambia el código

# ❌ MAL: Todo de una vez (invalida caché npm en cada cambio de código)
COPY . .
RUN npm ci
```

### 3. Nunca ejecutar como root

```dockerfile
# ✅ BIEN: Crear y usar usuario específico
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser
USER appuser

# ❌ MAL: Usuario root por defecto
# (sin instrucción USER = root)
```

### 4. Usar .dockerignore siempre

```dockerignore
# .dockerignore - siempre incluir
node_modules/
.git/
.env*
*.log
dist/
build/
coverage/
```

### 5. Etiquetas semánticas en imágenes

```bash
# ✅ BIEN: Versión semántica + latest
docker tag mi-app:latest mi-usuario/mi-app:1.2.3
docker tag mi-app:latest mi-usuario/mi-app:1.2
docker tag mi-app:latest mi-usuario/mi-app:latest

# ❌ MAL: Solo latest (pierde historial)
docker tag mi-app mi-usuario/mi-app:latest
```

### 6. Health checks para todos los servicios

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
  CMD curl -f http://localhost:${PORT}/health || exit 1
```

### 7. Secrets seguros

```bash
# ✅ BIEN: Variables de entorno al ejecutar
docker run -e DATABASE_PASSWORD=$SECRET mi-app

# ✅ BIEN: Docker Secrets (Swarm)
echo "mi-secreto" | docker secret create db_password -

# ❌ MAL: Hardcoded en Dockerfile
ENV DATABASE_PASSWORD=mi-contraseña-insegura
```

### 8. Limitar recursos

```yaml
services:
  api:
    image: mi-api
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: '1.0'
```

### Resumen de buenas prácticas

| Práctica | Impacto |
|----------|---------|
| Usar Alpine/Distroless | Reducción de tamaño 5-10x |
| Multi-stage builds | Imagen final mínima |
| Aprovechar caché de capas | Builds hasta 10x más rápidos |
| .dockerignore | Contexto de build más pequeño |
| Usuario no-root | Menor superficie de ataque |
| Health checks | Detección automática de fallos |
| Limit de recursos | Estabilidad del host |
| Secrets seguros | Sin credenciales en imagen |

---

## 17. Solución de problemas comunes

### Contenedor no inicia

```bash
# Ver los últimos logs
docker logs --tail 50 mi-contenedor

# Ver logs con timestamps
docker logs -t mi-contenedor

# Ver el estado de salida
docker inspect mi-contenedor --format='{{.State.ExitCode}}'
# 0 = éxito, 1 = error general, 137 = OOM killed

# Ver eventos del sistema Docker
docker events --filter container=mi-contenedor
```

### Problemas de red

```bash
# Verificar que el contenedor está en la red correcta
docker inspect mi-contenedor --format='{{json .NetworkSettings.Networks}}'

# Probar conectividad desde dentro del contenedor
docker exec mi-contenedor ping otro-contenedor
docker exec mi-contenedor curl http://otro-contenedor:puerto/health

# Ver las reglas de iptables (puede requerir sudo)
sudo iptables -L -n | grep DOCKER

# Reiniciar el daemon de Docker si hay problemas de red
sudo systemctl restart docker
```

### Problemas de volúmenes y permisos

```bash
# Ver el usuario actual dentro del contenedor
docker exec mi-contenedor id

# Ver permisos del directorio montado
docker exec mi-contenedor ls -la /ruta/volumen

# Solución: ajustar permisos en el Dockerfile
RUN chown -R appuser:appgroup /ruta/datos
```

### Imagen no se construye

```bash
# Build con salida verbosa
docker build --progress=plain --no-cache -t mi-app .

# Construir una etapa específica para debug
docker build --target nombre-etapa -t debug-imagen .
docker run -it debug-imagen sh

# Ver tamaño de cada capa
docker history mi-app
```

### Contenedor consume demasiados recursos

```bash
# Ver uso de recursos en tiempo real
docker stats

# Filtrar por contenedor
docker stats mi-contenedor

# Identificar contenedores con más consumo
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

### Limpiar el sistema

```bash
# Ver espacio usado por Docker
docker system df

# Eliminar todo lo no usado (contenedores, redes, imágenes colgantes)
docker system prune

# Incluir también volúmenes sin usar (¡cuidado con datos!)
docker system prune --volumes

# Eliminar todo (imágenes, contenedores, redes, volúmenes)
docker system prune -a --volumes

# Limpiar solo imágenes no usadas
docker image prune -a

# Limpiar solo contenedores detenidos
docker container prune

# Limpiar solo redes sin usar
docker network prune

# Limpiar solo volúmenes sin usar
docker volume prune
```

### Referencia rápida de comandos

```bash
# CONTENEDORES
docker run [opciones] imagen [comando]
docker ps [-a]                      # Listar contenedores
docker start/stop/restart nombre    # Gestionar estado
docker rm [-f] nombre               # Eliminar contenedor
docker exec -it nombre bash         # Terminal interactiva
docker logs [-f] nombre             # Ver logs
docker inspect nombre               # Detalles completos
docker stats [nombre]               # Uso de recursos
docker cp origen destino            # Copiar archivos

# IMÁGENES
docker images                       # Listar imágenes
docker pull imagen:tag              # Descargar imagen
docker build -t nombre:tag .        # Construir imagen
docker push nombre:tag              # Publicar imagen
docker rmi imagen                   # Eliminar imagen
docker history imagen               # Ver capas

# VOLÚMENES
docker volume create nombre         # Crear volumen
docker volume ls                    # Listar volúmenes
docker volume rm nombre             # Eliminar volumen
docker volume inspect nombre        # Detalles

# REDES
docker network create nombre        # Crear red
docker network ls                   # Listar redes
docker network connect red contenedor   # Conectar
docker network disconnect red contenedor # Desconectar

# DOCKER COMPOSE
docker compose up [-d] [--build]    # Iniciar servicios
docker compose down [-v]            # Detener y eliminar
docker compose ps                   # Ver estado
docker compose logs [-f] [servicio] # Ver logs
docker compose exec servicio bash   # Terminal interactiva
docker compose build [servicio]     # Construir imágenes

# SISTEMA
docker system df                    # Espacio usado
docker system prune [-a] [--volumes] # Limpiar
docker info                         # Info del sistema
docker version                      # Versión
```

---

## 📚 Recursos adicionales

- [Documentación oficial de Docker](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Play with Docker](https://labs.play-with-docker.com/) — Entorno Docker gratuito en el navegador
- [Docker Curriculum](https://docker-curriculum.com/) — Tutorial completo en inglés
- [Awesome Docker](https://github.com/veggiemonk/awesome-docker) — Lista de recursos curados
- [Docker Cheat Sheet](https://docs.docker.com/get-started/docker_cheatsheet.pdf) — PDF oficial de referencia rápida

---

*Tutorial creado para estudiantes de informática. Docker es una herramienta fundamental en el desarrollo moderno de software — ¡practica estos ejemplos y experimenta con tus propios proyectos!*
