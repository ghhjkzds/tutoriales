# 🚂 Guía Completa de Deployment con Railway

## Índice

1. [¿Qué es Railway?](#1-qué-es-railway)
2. [Crear cuenta y primeros pasos](#2-crear-cuenta-y-primeros-pasos)
3. [Conceptos clave de Railway](#3-conceptos-clave-de-railway)
4. [Deploy de un Backend Node.js](#4-deploy-de-un-backend-nodejs)
5. [Deploy de un Backend Python (FastAPI)](#5-deploy-de-un-backend-python-fastapi)
6. [Deploy de un Backend Java (Spring Boot)](#6-deploy-de-un-backend-java-spring-boot)
7. [Deploy de un Backend Go](#7-deploy-de-un-backend-go)
8. [Bases de datos en Railway](#8-bases-de-datos-en-railway)
9. [Variables de entorno y secretos](#9-variables-de-entorno-y-secretos)
10. [Dominios personalizados y HTTPS](#10-dominios-personalizados-y-https)
11. [Railway CLI: control total desde la terminal](#11-railway-cli-control-total-desde-la-terminal)
12. [CI/CD con Railway y GitHub Actions](#12-cicd-con-railway-y-github-actions)
13. [Monitoreo, logs y métricas](#13-monitoreo-logs-y-métricas)
14. [Proyecto práctico: API REST + PostgreSQL en Railway](#14-proyecto-práctico-api-rest--postgresql-en-railway)
15. [Escalado y configuración avanzada](#15-escalado-y-configuración-avanzada)
16. [Precios y plan gratuito](#16-precios-y-plan-gratuito)
17. [Solución de problemas comunes](#17-solución-de-problemas-comunes)
18. [Buenas prácticas](#18-buenas-prácticas)

---

## 1. ¿Qué es Railway?

**Railway** es una plataforma PaaS (Platform as a Service) moderna que permite desplegar aplicaciones, bases de datos y servicios de manera rápida y sencilla directamente desde GitHub u otros proveedores de Git, sin necesidad de gestionar servidores.

### ¿Por qué elegir Railway?

| Ventaja | Descripción |
|---------|-------------|
| 🚀 **Deploy instantáneo** | Conectas tu repo de GitHub y Railway despliega automáticamente en cada push |
| 🗄️ **Bases de datos integradas** | PostgreSQL, MySQL, MongoDB y Redis con un clic |
| 💰 **Precios transparentes** | Pay-per-use, sin sorpresas. Créditos gratuitos para empezar |
| 🔧 **Sin configuración de servidor** | Railway gestiona la infraestructura por ti |
| 🌐 **URLs automáticas** | Obtienes una URL pública con HTTPS inmediatamente |
| 📊 **Métricas y logs** | Monitoreo integrado sin herramientas adicionales |
| 🔄 **Múltiples lenguajes** | Node.js, Python, Java, Go, Ruby, PHP, Rust y más |

### Railway vs otras plataformas

```
┌──────────────┬───────────┬────────────┬────────────┬──────────────┐
│ Plataforma   │ Free tier │ Facilidad  │ Lenguajes  │ BD integrada │
├──────────────┼───────────┼────────────┼────────────┼──────────────┤
│ Railway      │ $5 crédito│ ⭐⭐⭐⭐⭐ │ Todos      │ ✅ Nativo   │
│ Render       │ Gratis    │ ⭐⭐⭐⭐   │ Mayoría    │ ✅ Nativo   │
│ Heroku       │ $5/mes    │ ⭐⭐⭐⭐   │ Todos      │ ✅ Add-ons  │
│ Fly.io       │ Limitado  │ ⭐⭐⭐     │ Docker     │ ✅ Nativo   │
│ DigitalOcean │ No        │ ⭐⭐⭐     │ Todos      │ ✅ Managed  │
└──────────────┴───────────┴────────────┴────────────┴──────────────┘
```

---

## 2. Crear Cuenta y Primeros Pasos

### Paso 1: Registro

1. Ve a [railway.app](https://railway.app)
2. Haz clic en **"Start a New Project"** o **"Login"**
3. Elige autenticarte con **GitHub** (recomendado) o con email
4. Autoriza a Railway a acceder a tus repositorios cuando te lo solicite

> 💡 **Recomendación:** Usa tu cuenta de GitHub para registrarte. Esto facilita enormemente conectar tus repositorios y la integración con CI/CD.

### Paso 2: Dashboard principal

Una vez dentro verás el **dashboard de Railway** con:

```
┌─────────────────────────────────────────────────┐
│              Railway Dashboard                   │
│                                                 │
│  + New Project                                  │
│                                                 │
│  📁 mis-proyectos                               │
│  ├── 🚀 mi-api-backend                         │
│  ├── 🗄️ postgresql-db                          │
│  └── 🔴 redis-cache                            │
│                                                 │
│  💳 $5.00 créditos restantes                   │
└─────────────────────────────────────────────────┘
```

### Paso 3: Crear tu primer proyecto

1. Haz clic en **"+ New Project"**
2. Elige una de las opciones:
   - **Deploy from GitHub repo** → conecta un repositorio existente
   - **Deploy a template** → usa una plantilla predefinida (Express, Django, Spring Boot...)
   - **Empty project** → proyecto en blanco para configurar manualmente

---

## 3. Conceptos Clave de Railway

### Proyectos, Servicios y Entornos

```
Proyecto (Project)
├── Entorno: Production
│   ├── Servicio: mi-backend (Node.js)
│   ├── Servicio: postgresql (Base de datos)
│   └── Servicio: redis (Caché)
└── Entorno: Staging
    ├── Servicio: mi-backend (Node.js) [rama: develop]
    └── Servicio: postgresql (copia de producción)
```

| Concepto | Descripción |
|----------|-------------|
| **Project** | Contenedor principal que agrupa todos tus servicios |
| **Service** | Unidad de despliegue individual (un backend, una BD, un worker) |
| **Environment** | Variante del proyecto (production, staging, development) |
| **Deployment** | Una instancia concreta de un servicio en ejecución |
| **Variables** | Configuración en forma de clave-valor para cada servicio |

### Cómo funciona el deploy automático

```
Git push → GitHub webhook → Railway detecta cambio
    → Build (instala dependencias, compila)
    → Deploy (reemplaza la instancia anterior)
    → Health check (verifica que la app responde)
    → Tráfico redirigido a la nueva versión ✅
```

Railway usa **rolling deployments** por defecto: la nueva versión se despliega en paralelo a la anterior, y sólo cuando está lista se redirige el tráfico. Esto garantiza **zero downtime**.

---

## 4. Deploy de un Backend Node.js

### Preparar el proyecto

Asegúrate de que tu proyecto tiene la estructura mínima:

```
mi-backend/
├── package.json      ← Railway lo detecta para saber que es Node.js
├── package-lock.json
├── .gitignore        ← Incluir node_modules y .env
├── .env.example      ← Variables de entorno de ejemplo (sin valores reales)
└── src/
    └── index.js      ← Punto de entrada
```

#### `package.json` mínimo

```json
{
  "name": "mi-backend",
  "version": "1.0.0",
  "scripts": {
    "start": "node src/index.js",
    "build": "echo 'No build step needed'"
  },
  "engines": {
    "node": ">=18.0.0"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

> ⚠️ **Importante:** Railway ejecuta `npm start` por defecto. Asegúrate de que ese script existe y arranca tu servidor.

#### `src/index.js` básico

```javascript
const express = require('express');
const app = express();

app.use(express.json());

// Railway requiere escuchar en process.env.PORT
const PORT = process.env.PORT || 3000;

app.get('/health', (req, res) => {
  res.json({ status: 'ok', uptime: process.uptime() });
});

app.get('/api/hola', (req, res) => {
  res.json({ mensaje: '¡Hola desde Railway!' });
});

app.listen(PORT, () => {
  console.log(`Servidor corriendo en puerto ${PORT}`);
});
```

> 🔑 **Clave:** Usa siempre `process.env.PORT` para el puerto. Railway asigna el puerto dinámicamente mediante esta variable.

#### `.gitignore`

```gitignore
node_modules/
.env
dist/
*.log
```

### Desplegar desde GitHub (método recomendado)

1. **Sube tu código a GitHub:**
   ```bash
   git init
   git add .
   git commit -m "feat: backend inicial"
   git branch -M main
   git remote add origin https://github.com/tu-usuario/mi-backend.git
   git push -u origin main
   ```

2. **En Railway:**
   - Haz clic en **"+ New Project"**
   - Selecciona **"Deploy from GitHub repo"**
   - Autoriza el acceso a tus repos si no lo has hecho
   - Busca y selecciona `mi-backend`

3. **Railway hace el resto:**
   - Detecta que es Node.js por el `package.json`
   - Instala dependencias con `npm install`
   - Ejecuta `npm start`
   - Genera una URL pública como `mi-backend-production.up.railway.app`

4. **Verifica que funciona:**
   ```
   https://mi-backend-production.up.railway.app/health
   → {"status":"ok","uptime":123.45}
   ```

### Activar el dominio público

Por defecto, Railway no expone tu servicio a internet. Debes generarle una URL:

1. Abre tu servicio en el dashboard
2. Ve a la pestaña **"Settings"**
3. Busca la sección **"Networking"**
4. Haz clic en **"Generate Domain"**
5. Obtendrás una URL del tipo: `nombre-produccion.up.railway.app`

```
⚠️ Sin este paso, tu app está desplegada pero no es accesible desde internet.
```

---

## 5. Deploy de un Backend Python (FastAPI)

### Estructura del proyecto

```
mi-api-python/
├── main.py             ← Punto de entrada
├── requirements.txt    ← Dependencias (Railway las instala automáticamente)
├── Procfile            ← (Opcional) Comando de inicio
└── .gitignore
```

#### `requirements.txt`

```txt
fastapi==0.111.0
uvicorn[standard]==0.30.1
pydantic==2.7.0
psycopg2-binary==2.9.9
python-dotenv==1.0.1
```

#### `main.py`

```python
from fastapi import FastAPI
import os

app = FastAPI(title="Mi API en Railway")

@app.get("/health")
def health_check():
    return {"status": "ok"}

@app.get("/api/hola")
def hola():
    return {"mensaje": "¡Hola desde Railway con FastAPI!"}

if __name__ == "__main__":
    import uvicorn
    # Railway inyecta PORT como variable de entorno
    port = int(os.environ.get("PORT", 8000))
    uvicorn.run("main:app", host="0.0.0.0", port=port, reload=False)
```

#### `Procfile` (opcional pero recomendado)

```
web: uvicorn main:app --host 0.0.0.0 --port $PORT
```

> 💡 **Procfile vs `railway.json`:** Puedes usar cualquiera de los dos para indicarle a Railway cómo iniciar tu app. El `Procfile` es más portable; el `railway.json` es más potente.

#### `railway.json` (alternativa al Procfile)

```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "pip install -r requirements.txt"
  },
  "deploy": {
    "startCommand": "uvicorn main:app --host 0.0.0.0 --port $PORT",
    "healthcheckPath": "/health",
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 3
  }
}
```

### `nixpacks.toml` para control fino del build

Railway usa [Nixpacks](https://nixpacks.com) para detectar el lenguaje y construir el proyecto. Puedes sobreescribir la configuración:

```toml
# nixpacks.toml
[phases.setup]
nixPkgs = ["python311", "gcc"]

[phases.install]
cmds = ["pip install -r requirements.txt"]

[phases.build]
cmds = []

[start]
cmd = "uvicorn main:app --host 0.0.0.0 --port $PORT"
```

---

## 6. Deploy de un Backend Java (Spring Boot)

### Estructura del proyecto

```
mi-api-spring/
├── pom.xml             ← Railway detecta Maven automáticamente
├── src/
│   └── main/
│       ├── java/
│       │   └── com/ejemplo/
│       │       └── MiApiApplication.java
│       └── resources/
│           └── application.properties
└── .gitignore
```

#### `application.properties`

```properties
# Railway inyecta PORT automáticamente
server.port=${PORT:8080}

# Variables de entorno para BD (Railway las inyecta desde el servicio de PostgreSQL)
spring.datasource.url=${DATABASE_URL:jdbc:h2:mem:testdb}
spring.datasource.username=${PGUSER:sa}
spring.datasource.password=${PGPASSWORD:}

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
```

#### `MiApiApplication.java`

```java
package com.ejemplo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class MiApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(MiApiApplication.class, args);
    }

    @GetMapping("/health")
    public String health() {
        return "{\"status\":\"ok\"}";
    }

    @GetMapping("/api/hola")
    public String hola() {
        return "{\"mensaje\":\"¡Hola desde Railway con Spring Boot!\"}";
    }
}
```

#### `pom.xml` (fragmento clave)

```xml
<properties>
    <java.version>17</java.version>
</properties>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

> 🔑 Railway ejecuta `./mvnw package -DskipTests` y luego `java -jar target/*.jar` automáticamente para proyectos Maven.

#### `railway.json` para Spring Boot

```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "./mvnw package -DskipTests"
  },
  "deploy": {
    "startCommand": "java -jar target/*.jar",
    "healthcheckPath": "/health",
    "healthcheckTimeout": 60
  }
}
```

---

## 7. Deploy de un Backend Go

### Estructura del proyecto

```
mi-api-go/
├── go.mod              ← Railway detecta Go automáticamente
├── go.sum
├── main.go
└── .gitignore
```

#### `main.go`

```go
package main

import (
    "encoding/json"
    "log"
    "net/http"
    "os"
)

func main() {
    http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(map[string]string{"status": "ok"})
    })

    http.HandleFunc("/api/hola", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(map[string]string{
            "mensaje": "¡Hola desde Railway con Go!",
        })
    })

    // Railway asigna el puerto mediante la variable PORT
    port := os.Getenv("PORT")
    if port == "" {
        port = "8080"
    }

    log.Printf("Servidor Go escuchando en :%s", port)
    log.Fatal(http.ListenAndServe(":"+port, nil))
}
```

#### `railway.json` para Go

```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "go build -o server ."
  },
  "deploy": {
    "startCommand": "./server",
    "healthcheckPath": "/health"
  }
}
```

---

## 8. Bases de Datos en Railway

Una de las funcionalidades más potentes de Railway es la **gestión integrada de bases de datos**. No necesitas contratar un servicio externo.

### Bases de datos disponibles

| Base de datos | Versión | Uso ideal |
|---------------|---------|-----------|
| **PostgreSQL** | 15, 16 | Aplicaciones relacionales (la más usada) |
| **MySQL** | 8.0 | Proyectos legacy o que requieren MySQL |
| **MongoDB** | 6.0, 7.0 | Datos no estructurados, documentos JSON |
| **Redis** | 7.x | Caché, sesiones, colas de mensajes |

### Añadir PostgreSQL a tu proyecto

**Desde el dashboard:**

1. Abre tu proyecto en Railway
2. Haz clic en **"+ Add Service"** (o el botón **"+"** en el canvas)
3. Selecciona **"Database"** → **"PostgreSQL"**
4. Railway crea el servicio en segundos y genera las variables de conexión automáticamente

**Variables que Railway inyecta automáticamente en tu servicio:**

```
DATABASE_URL      = postgresql://postgres:PASSWORD@HOST:PORT/railway
PGHOST            = HOST.railway.internal
PGPORT            = 5432
PGUSER            = postgres
PGPASSWORD        = PASSWORD_GENERADO
PGDATABASE        = railway
```

> 💡 Railway inyecta estas variables **automáticamente** en todos los servicios del mismo proyecto. No necesitas copiar y pegar manualmente.

### Usar la variable DATABASE_URL

#### Node.js con pg

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: process.env.NODE_ENV === 'production'
    ? { rejectUnauthorized: false }
    : false
});

async function getUsers() {
  const result = await pool.query('SELECT * FROM usuarios LIMIT 10');
  return result.rows;
}
```

#### Python con SQLAlchemy

```python
from sqlalchemy import create_engine
import os

DATABASE_URL = os.environ.get("DATABASE_URL")

# SQLAlchemy necesita "postgresql://" en vez de "postgres://"
if DATABASE_URL and DATABASE_URL.startswith("postgres://"):
    DATABASE_URL = DATABASE_URL.replace("postgres://", "postgresql://", 1)

engine = create_engine(DATABASE_URL)
```

#### Java con Spring Boot (application.properties)

```properties
spring.datasource.url=${DATABASE_URL}
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
```

#### Go con pgx

```go
import (
    "context"
    "os"
    "github.com/jackc/pgx/v5/pgxpool"
)

func NewDB() (*pgxpool.Pool, error) {
    return pgxpool.New(context.Background(), os.Getenv("DATABASE_URL"))
}
```

### Conectarse a la BD de Railway desde tu máquina local

Railway permite abrir un túnel seguro a la base de datos para desarrollo local:

```bash
# Instalar Railway CLI (si no lo tienes)
npm install -g @railway/cli

# Login
railway login

# Abrir el proyecto
railway link

# Abrir un túnel al servicio de PostgreSQL
railway connect postgresql

# También puedes usar el proxy directamente
railway run -- psql $DATABASE_URL
```

Esto abrirá una conexión local en `localhost:5432` que se redirige a la BD de Railway. Muy útil para:
- Ejecutar migraciones
- Inspeccionar datos
- Debuggear problemas de producción

### Backups y restauración

**Crear un backup manual:**
1. Dashboard → tu servicio PostgreSQL → pestaña **"Backups"**
2. Haz clic en **"Create Backup"**
3. Descarga el archivo `.sql` generado

**Restaurar un backup:**
```bash
# Con Railway CLI
railway run -- psql $DATABASE_URL < backup.sql

# Con psql directo (necesitas la URL de conexión)
psql "postgresql://postgres:PASSWORD@HOST:PORT/railway" < backup.sql
```

---

## 9. Variables de Entorno y Secretos

### Regla de oro: nunca subas secretos a Git

```bash
# ❌ NUNCA hagas esto
const SECRET = "mi_clave_secreta_123";  # Hardcodeado en el código

# ✅ Siempre así
const SECRET = process.env.JWT_SECRET;
```

### Gestionar variables en Railway

**Desde el dashboard:**

1. Abre tu servicio
2. Ve a la pestaña **"Variables"**
3. Haz clic en **"+ Add Variable"**
4. Escribe la clave y el valor

**Con Railway CLI:**

```bash
# Establecer una variable
railway variables set JWT_SECRET=mi_clave_super_secreta

# Ver todas las variables
railway variables

# Eliminar una variable
railway variables delete JWT_SECRET

# Importar desde un archivo .env
railway variables import .env.production
```

### Tipos de variables en Railway

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Manual** | La defines tú desde el panel o CLI | `JWT_SECRET=abc123` |
| **Referencia de servicio** | Railway las inyecta desde otro servicio del mismo proyecto | `DATABASE_URL` desde PostgreSQL |
| **Railway variables** | Variables del sistema que Railway expone | `RAILWAY_ENVIRONMENT`, `RAILWAY_PROJECT_ID` |

### Variables especiales de Railway

```bash
RAILWAY_ENVIRONMENT       # Nombre del entorno (production, staging...)
RAILWAY_PROJECT_ID        # ID único del proyecto
RAILWAY_SERVICE_ID        # ID único del servicio
RAILWAY_PUBLIC_DOMAIN     # Dominio público asignado
PORT                      # Puerto en el que debe escuchar tu app
```

### Compartir variables entre servicios (Variable References)

Railway permite que un servicio lea variables de otro servicio del mismo proyecto:

1. En las variables de tu backend, haz clic en **"+ Add Variable"**
2. En el valor, escribe `${{Nombre-Servicio.VARIABLE}}`
3. Por ejemplo: `${{PostgreSQL.DATABASE_URL}}`

```
# Ejemplo de referencia de variable
DATABASE_URL = ${{PostgreSQL.DATABASE_URL}}
REDIS_URL    = ${{Redis.REDIS_URL}}
```

### Usar `.env.example` para el equipo

```bash
# .env.example (SUBE ESTO a Git, sin valores reales)
PORT=3000
NODE_ENV=development
DATABASE_URL=postgresql://usuario:password@localhost:5432/mi_bd
JWT_SECRET=cambia_esto_en_produccion
CORS_ORIGIN=http://localhost:5173
```

```bash
# .env (NO subas esto a Git - inclúyelo en .gitignore)
PORT=3000
NODE_ENV=development
DATABASE_URL=postgresql://postgres:miPassword@localhost:5432/dev_db
JWT_SECRET=secreto_local_seguro_1234
CORS_ORIGIN=http://localhost:5173
```

---

## 10. Dominios Personalizados y HTTPS

### URL pública automática

Al hacer deploy, Railway te da una URL automática con HTTPS:

```
https://mi-backend-production.up.railway.app
```

Esta URL es permanente y no cambia entre deploys.

### Añadir un dominio personalizado

1. Compra un dominio en algún registrador (Namecheap, Google Domains, Cloudflare, etc.)
2. En Railway: tu servicio → **"Settings"** → **"Networking"** → **"Custom Domain"**
3. Escribe tu dominio: `api.miapp.com`
4. Railway te mostrará un registro CNAME para añadir en tu DNS:
   ```
   Tipo:  CNAME
   Host:  api
   Valor: tu-backend.up.railway.app
   TTL:   Auto
   ```
5. Añade ese registro CNAME en tu proveedor de DNS
6. Espera entre 5 minutos y 24 horas a que se propague
7. Railway gestiona el certificado SSL (Let's Encrypt) automáticamente

### Verificación del dominio

```bash
# Comprobar que el DNS apunta correctamente
nslookup api.miapp.com
dig api.miapp.com CNAME

# Comprobar el certificado SSL
curl -I https://api.miapp.com/health
```

### Redirigir HTTP a HTTPS

Railway gestiona esto automáticamente. Todas las peticiones HTTP son redirigidas a HTTPS con un código 301. No necesitas configurar nada adicional.

---

## 11. Railway CLI: Control Total desde la Terminal

La CLI de Railway te permite gestionar tus proyectos y servicios directamente desde la terminal.

### Instalación

```bash
# Con npm (Node.js)
npm install -g @railway/cli

# Con Homebrew (macOS/Linux)
brew install railway

# Verificar la instalación
railway --version
```

### Autenticación

```bash
# Login con el navegador
railway login

# Login con token (para CI/CD)
railway login --browserless
# Ingresa el token generado en railway.app/account/tokens
```

### Comandos esenciales

```bash
# ── Proyectos ──────────────────────────────────────────────────
railway list                          # Listar todos tus proyectos
railway link                          # Vincular la carpeta al proyecto
railway link [PROJECT_ID]             # Vincular a un proyecto específico
railway open                          # Abrir el proyecto en el navegador

# ── Despliegue ─────────────────────────────────────────────────
railway up                            # Desplegar el directorio actual
railway up --service mi-backend       # Desplegar en un servicio específico
railway up --detach                   # Deploy sin esperar a que termine

# ── Variables de entorno ───────────────────────────────────────
railway variables                     # Ver todas las variables del servicio activo
railway variables set CLAVE=VALOR     # Añadir/actualizar variable
railway variables delete CLAVE        # Eliminar variable
railway variables import .env         # Importar desde archivo .env

# ── Logs ───────────────────────────────────────────────────────
railway logs                          # Ver logs recientes
railway logs --tail                   # Logs en tiempo real (streaming)
railway logs --filter "ERROR"         # Filtrar logs por texto

# ── Base de datos ──────────────────────────────────────────────
railway connect postgresql            # Conectar psql a la BD de producción
railway run -- psql $DATABASE_URL     # Ejecutar psql con las variables del proyecto

# ── Ejecutar comandos en el entorno del proyecto ───────────────
railway run -- node scripts/seed.js   # Ejecutar script con variables de Railway
railway run -- npm run migrate        # Ejecutar migraciones con la BD real
railway run -- python manage.py migrate

# ── Shell ──────────────────────────────────────────────────────
railway shell                         # Abrir una shell interactiva en el servicio
```

### Flujo de trabajo diario con la CLI

```bash
# 1. Iniciar en un proyecto nuevo
cd mi-proyecto
railway login
railway link

# 2. Durante el desarrollo, probar con las variables de Railway
railway run -- npm run dev

# 3. Antes de hacer commit, ver los logs del deploy actual
railway logs --tail

# 4. Actualizar una variable
railway variables set NODE_ENV=production

# 5. Deploy manual (si no tienes auto-deploy configurado)
railway up
```

---

## 12. CI/CD con Railway y GitHub Actions

### Método 1: Auto-deploy (sin configuración adicional)

Railway puede desplegar automáticamente en cada push a una rama. Configuración en el dashboard:

1. Ve a tu servicio → **"Settings"** → **"Source"**
2. En **"Branch"**, selecciona la rama a monitorizar (ej: `main`)
3. Activa **"Auto Deploy"**

```
Desde ese momento:
git push origin main → Railway detecta el push → Build → Deploy automático ✅
```

### Método 2: GitHub Actions con Railway CLI

Este método te da más control: puedes ejecutar tests antes del deploy.

```yaml
# .github/workflows/deploy.yml
name: 🚀 CI/CD con Railway

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # ─── JOB 1: Tests ─────────────────────────────────────────────
  test:
    name: 🧪 Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Configurar Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Instalar dependencias
        run: npm ci

      - name: Ejecutar tests
        run: npm test

      - name: Lint
        run: npm run lint

  # ─── JOB 2: Deploy a Railway ──────────────────────────────────
  deploy:
    name: 🚂 Deploy a Railway
    needs: test               # Solo despliega si los tests pasan
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'  # Solo en la rama main

    steps:
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Instalar Railway CLI
        run: npm install -g @railway/cli

      - name: Deploy a Railway
        run: railway up --service ${{ vars.RAILWAY_SERVICE_NAME }}
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
```

### Obtener el RAILWAY_TOKEN para GitHub Actions

1. Ve a [railway.app/account/tokens](https://railway.app/account/tokens)
2. Haz clic en **"+ Create Token"**
3. Ponle un nombre descriptivo: `github-actions-deploy`
4. Copia el token generado
5. En tu repo de GitHub: **Settings** → **Secrets and variables** → **Actions** → **"New repository secret"**
6. Nombre: `RAILWAY_TOKEN`, Valor: el token copiado

### Método 3: Acción oficial de Railway

```yaml
# .github/workflows/deploy.yml
name: Deploy to Railway

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Railway
        uses: bervProject/railway-deploy@main
        with:
          railway_token: ${{ secrets.RAILWAY_TOKEN }}
          service: mi-servicio-nombre
```

### Pipeline completo con entornos (staging + production)

```yaml
# .github/workflows/deploy.yml
name: 🚀 Pipeline Completo

on:
  push:
    branches: [main, develop]

jobs:
  test:
    name: 🧪 Tests y Lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm test
      - run: npm run lint

  deploy-staging:
    name: 🎭 Deploy a Staging
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/develop'
    environment: staging

    steps:
      - uses: actions/checkout@v4
      - run: npm install -g @railway/cli
      - name: Deploy a entorno Staging
        run: railway up --service backend --environment staging
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

  deploy-production:
    name: 🚀 Deploy a Producción
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production

    steps:
      - uses: actions/checkout@v4
      - run: npm install -g @railway/cli
      - name: Deploy a entorno Production
        run: railway up --service backend --environment production
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
```

---

## 13. Monitoreo, Logs y Métricas

### Ver logs desde el dashboard

1. Abre tu proyecto → selecciona el servicio
2. Ve a la pestaña **"Logs"**
3. Verás los logs en tiempo real con:
   - Timestamp
   - Nivel de log (INFO, WARN, ERROR)
   - Mensaje

### Ver logs desde la CLI

```bash
# Logs recientes (últimas 100 líneas)
railway logs

# Logs en tiempo real (streaming)
railway logs --tail

# Filtrar por texto
railway logs --tail | grep "ERROR"

# Especificar el servicio
railway logs --service mi-backend
```

### Hacer que tus logs sean informativos

#### Node.js con formato JSON (recomendado para producción)

```javascript
// logger.js
const log = (level, message, meta = {}) => {
  console.log(JSON.stringify({
    timestamp: new Date().toISOString(),
    level,
    message,
    ...meta
  }));
};

module.exports = {
  info: (msg, meta) => log('INFO', msg, meta),
  warn: (msg, meta) => log('WARN', msg, meta),
  error: (msg, meta) => log('ERROR', msg, meta),
};

// Uso
const logger = require('./logger');
logger.info('Servidor iniciado', { port: 3000 });
logger.error('Error en BD', { error: err.message, userId: 123 });
```

#### Python con logging estructurado

```python
import logging
import json
import os

class JSONFormatter(logging.Formatter):
    def format(self, record):
        return json.dumps({
            "timestamp": self.formatTime(record),
            "level": record.levelname,
            "message": record.getMessage(),
        })

handler = logging.StreamHandler()
handler.setFormatter(JSONFormatter())
logging.basicConfig(handlers=[handler], level=logging.INFO)
logger = logging.getLogger(__name__)

logger.info("Servidor iniciado", extra={"port": os.environ.get("PORT")})
```

### Métricas de uso

Railway muestra en el dashboard métricas de:

```
📊 CPU Usage       → porcentaje de CPU consumido
💾 Memory Usage    → RAM utilizada vs límite del plan
🌐 Network I/O     → tráfico entrante y saliente
💿 Disk I/O        → operaciones de lectura/escritura
```

Para acceder: **Tu servicio** → pestaña **"Metrics"**

### Health checks

Configura un endpoint de salud para que Railway sepa si tu app está funcionando:

```javascript
// Node.js - health check completo
app.get('/health', async (req, res) => {
  try {
    // Verificar conexión a BD
    await pool.query('SELECT 1');
    res.json({
      status: 'ok',
      timestamp: new Date().toISOString(),
      uptime: process.uptime(),
      database: 'connected'
    });
  } catch (error) {
    res.status(503).json({
      status: 'error',
      database: 'disconnected',
      error: error.message
    });
  }
});
```

```python
# FastAPI - health check completo
from fastapi import FastAPI
from sqlalchemy import text
import time

start_time = time.time()

@app.get("/health")
async def health_check():
    try:
        with engine.connect() as conn:
            conn.execute(text("SELECT 1"))
        return {
            "status": "ok",
            "uptime": time.time() - start_time,
            "database": "connected"
        }
    except Exception as e:
        return JSONResponse(
            status_code=503,
            content={"status": "error", "database": str(e)}
        )
```

Configura el health check en `railway.json`:

```json
{
  "deploy": {
    "healthcheckPath": "/health",
    "healthcheckTimeout": 30
  }
}
```

---

## 14. Proyecto Práctico: API REST + PostgreSQL en Railway

Vamos a desplegar una API de gestión de tareas completa con Node.js y PostgreSQL.

### Estructura del proyecto

```
api-tareas/
├── package.json
├── railway.json
├── .gitignore
├── .env.example
└── src/
    ├── index.js        ← Entrada principal
    ├── database.js     ← Conexión a PostgreSQL
    ├── migrate.js      ← Creación de tablas
    └── routes/
        └── tareas.js   ← Endpoints de la API
```

### `package.json`

```json
{
  "name": "api-tareas",
  "version": "1.0.0",
  "scripts": {
    "start": "node src/index.js",
    "migrate": "node src/migrate.js"
  },
  "engines": {
    "node": ">=18.0.0"
  },
  "dependencies": {
    "express": "^4.18.2",
    "pg": "^8.11.3",
    "cors": "^2.8.5",
    "dotenv": "^16.4.5"
  }
}
```

### `src/database.js`

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: process.env.NODE_ENV === 'production'
    ? { rejectUnauthorized: false }
    : false,
  max: 10,                   // Máximo de conexiones en el pool
  idleTimeoutMillis: 30000,  // Tiempo antes de cerrar conexiones inactivas
  connectionTimeoutMillis: 2000,
});

// Probar conexión al iniciar
pool.on('connect', () => {
  console.log('✅ Conectado a PostgreSQL');
});

pool.on('error', (err) => {
  console.error('❌ Error en pool de PostgreSQL:', err.message);
});

module.exports = pool;
```

### `src/migrate.js`

```javascript
require('dotenv').config();
const pool = require('./database');

async function migrate() {
  console.log('🔄 Ejecutando migraciones...');

  await pool.query(`
    CREATE TABLE IF NOT EXISTS tareas (
      id          SERIAL PRIMARY KEY,
      titulo      VARCHAR(255) NOT NULL,
      descripcion TEXT,
      completada  BOOLEAN DEFAULT false,
      prioridad   VARCHAR(10) DEFAULT 'media'
                  CHECK (prioridad IN ('baja', 'media', 'alta')),
      creada_en   TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
      actualizada TIMESTAMP WITH TIME ZONE DEFAULT NOW()
    )
  `);

  console.log('✅ Migraciones completadas');
  await pool.end();
}

migrate().catch(err => {
  console.error('❌ Error en migración:', err);
  process.exit(1);
});
```

### `src/routes/tareas.js`

```javascript
const express = require('express');
const router = express.Router();
const pool = require('../database');

// GET /api/tareas
router.get('/', async (req, res) => {
  try {
    const { rows } = await pool.query(
      'SELECT * FROM tareas ORDER BY creada_en DESC'
    );
    res.json({ success: true, data: rows, total: rows.length });
  } catch (err) {
    res.status(500).json({ success: false, error: err.message });
  }
});

// GET /api/tareas/:id
router.get('/:id', async (req, res) => {
  try {
    const { rows } = await pool.query(
      'SELECT * FROM tareas WHERE id = $1',
      [req.params.id]
    );
    if (!rows[0]) {
      return res.status(404).json({ success: false, error: 'Tarea no encontrada' });
    }
    res.json({ success: true, data: rows[0] });
  } catch (err) {
    res.status(500).json({ success: false, error: err.message });
  }
});

// POST /api/tareas
router.post('/', async (req, res) => {
  const { titulo, descripcion, prioridad = 'media' } = req.body;
  if (!titulo) {
    return res.status(400).json({ success: false, error: 'El título es requerido' });
  }
  try {
    const { rows } = await pool.query(
      `INSERT INTO tareas (titulo, descripcion, prioridad)
       VALUES ($1, $2, $3) RETURNING *`,
      [titulo, descripcion, prioridad]
    );
    res.status(201).json({ success: true, data: rows[0] });
  } catch (err) {
    res.status(500).json({ success: false, error: err.message });
  }
});

// PATCH /api/tareas/:id
router.patch('/:id', async (req, res) => {
  const { titulo, descripcion, completada, prioridad } = req.body;
  try {
    const { rows } = await pool.query(
      `UPDATE tareas
       SET titulo       = COALESCE($1, titulo),
           descripcion  = COALESCE($2, descripcion),
           completada   = COALESCE($3, completada),
           prioridad    = COALESCE($4, prioridad),
           actualizada  = NOW()
       WHERE id = $5
       RETURNING *`,
      [titulo, descripcion, completada, prioridad, req.params.id]
    );
    if (!rows[0]) {
      return res.status(404).json({ success: false, error: 'Tarea no encontrada' });
    }
    res.json({ success: true, data: rows[0] });
  } catch (err) {
    res.status(500).json({ success: false, error: err.message });
  }
});

// DELETE /api/tareas/:id
router.delete('/:id', async (req, res) => {
  try {
    const { rows } = await pool.query(
      'DELETE FROM tareas WHERE id = $1 RETURNING id',
      [req.params.id]
    );
    if (!rows[0]) {
      return res.status(404).json({ success: false, error: 'Tarea no encontrada' });
    }
    res.json({ success: true, message: 'Tarea eliminada' });
  } catch (err) {
    res.status(500).json({ success: false, error: err.message });
  }
});

module.exports = router;
```

### `src/index.js`

```javascript
require('dotenv').config();
const express = require('express');
const cors = require('cors');
const tareasRouter = require('./routes/tareas');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(cors({
  origin: process.env.CORS_ORIGIN || '*',
}));
app.use(express.json());

// Health check
app.get('/health', async (req, res) => {
  const pool = require('./database');
  try {
    await pool.query('SELECT 1');
    res.json({ status: 'ok', uptime: process.uptime(), database: 'connected' });
  } catch {
    res.status(503).json({ status: 'error', database: 'disconnected' });
  }
});

app.use('/api/tareas', tareasRouter);

// 404
app.use((req, res) => {
  res.status(404).json({ error: 'Ruta no encontrada' });
});

app.listen(PORT, () => {
  console.log(`🚀 API de Tareas corriendo en puerto ${PORT}`);
  console.log(`📋 Entorno: ${process.env.NODE_ENV || 'development'}`);
});
```

### `railway.json`

```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "npm install"
  },
  "deploy": {
    "startCommand": "npm start",
    "healthcheckPath": "/health",
    "healthcheckTimeout": 30,
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 3
  }
}
```

### `.env.example`

```bash
# Puerto (Railway lo sobreescribe automáticamente)
PORT=3000

# Entorno
NODE_ENV=development

# Base de datos (Railway inyecta DATABASE_URL automáticamente)
DATABASE_URL=postgresql://postgres:password@localhost:5432/tareas_dev

# CORS
CORS_ORIGIN=http://localhost:5173
```

### `.gitignore`

```gitignore
node_modules/
.env
*.log
dist/
.DS_Store
```

### Pasos para desplegar

```bash
# 1. Sube el código a GitHub
git init
git add .
git commit -m "feat: API de tareas lista para Railway"
git branch -M main
git remote add origin https://github.com/tu-usuario/api-tareas.git
git push -u origin main
```

**En Railway:**

1. **New Project** → **Deploy from GitHub repo** → selecciona `api-tareas`
2. Railway detecta Node.js y despliega automáticamente
3. **Añade PostgreSQL**: en el canvas de tu proyecto, clic en **"+"** → **Database** → **PostgreSQL**
4. Railway inyecta `DATABASE_URL` automáticamente en tu servicio Node.js
5. **Ejecuta las migraciones:**
   ```bash
   railway run -- npm run migrate
   ```
6. **Genera el dominio público**: servicio → Settings → Networking → Generate Domain
7. **Añade las variables de entorno:**
   ```
   NODE_ENV=production
   CORS_ORIGIN=*
   ```

### Resultado final

```
✅ API:       https://api-tareas-production.up.railway.app
✅ Health:    https://api-tareas-production.up.railway.app/health
✅ Tareas:    https://api-tareas-production.up.railway.app/api/tareas

# Probar con curl
curl https://api-tareas-production.up.railway.app/api/tareas

curl -X POST https://api-tareas-production.up.railway.app/api/tareas \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Aprender Railway","prioridad":"alta"}'
```

---

## 15. Escalado y Configuración Avanzada

### Réplicas y escalado horizontal

Railway permite escalar tu servicio horizontalmente añadiendo réplicas:

1. Tu servicio → **"Settings"** → **"Deploy"** → **"Replicas"**
2. Aumenta el número de réplicas (1, 2, 3...)
3. Railway balancea el tráfico automáticamente entre las réplicas

> ⚠️ Si usas sesiones en memoria, necesitarás Redis para compartirlas entre réplicas.

### Escalado de recursos (CPU y RAM)

En Railway puedes configurar límites de recursos por servicio:

1. Tu servicio → **"Settings"** → **"Deploy"** → **"Resources"**
2. Ajusta el límite de CPU (en vCPU) y memoria (en MB)

```
Plan Hobby:     Hasta 8 GB RAM, 8 vCPU por servicio
Plan Pro:       Hasta 32 GB RAM, 32 vCPU por servicio
Plan Enterprise: Sin límites
```

### Configurar reintentos y política de reinicio

```json
{
  "deploy": {
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 5
  }
}
```

| Política | Descripción |
|----------|-------------|
| `NEVER` | No reiniciar nunca si falla |
| `ON_FAILURE` | Reiniciar solo si hay un error |
| `ALWAYS` | Reiniciar siempre que pare (incluso si fue intencional) |

### Variables de entorno por entorno (staging vs production)

Railway permite tener el mismo proyecto con múltiples entornos, cada uno con sus propias variables:

1. Dashboard del proyecto → botón de entorno en la esquina superior izquierda
2. Haz clic en **"+ Create Environment"** → nombra como `staging`
3. Cada entorno tiene su propio conjunto de variables y configuración

```
Proyecto: mi-app
├── Entorno: production
│   ├── NODE_ENV=production
│   ├── DATABASE_URL=postgresql://prod-host/prod-db
│   └── JWT_SECRET=secreto_produccion_muy_seguro
└── Entorno: staging
    ├── NODE_ENV=staging
    ├── DATABASE_URL=postgresql://staging-host/staging-db
    └── JWT_SECRET=secreto_staging_menos_critico
```

### Dockerfile personalizado

Si los buildpacks de Nixpacks no cubren tu caso, puedes usar un Dockerfile propio:

```dockerfile
# Dockerfile para Node.js
FROM node:20-alpine

WORKDIR /app

# Instalar dependencias
COPY package*.json ./
RUN npm ci --only=production

# Copiar código
COPY src/ ./src/

# Usuario no-root por seguridad
USER node

# Puerto configurable (Railway inyecta $PORT)
ENV PORT=3000
EXPOSE $PORT

CMD ["node", "src/index.js"]
```

Railway detecta automáticamente el `Dockerfile` si existe en la raíz del proyecto.

### Cron Jobs con Railway

Para tareas programadas, puedes crear un servicio tipo **Cron**:

1. **"+ Add Service"** → **"Cron Job"**
2. Escoge el repositorio y la rama
3. Define el comando a ejecutar
4. Configura el schedule en formato cron: `0 2 * * *` (cada día a las 2:00 AM)

```javascript
// scripts/limpiar-tareas-viejas.js
require('dotenv').config();
const pool = require('../src/database');

async function limpiar() {
  const result = await pool.query(`
    DELETE FROM tareas
    WHERE completada = true
      AND actualizada < NOW() - INTERVAL '30 days'
  `);
  console.log(`Eliminadas ${result.rowCount} tareas antiguas`);
  await pool.end();
}

limpiar().catch(console.error);
```

```
# En Railway Cron Job
Schedule: 0 3 * * *      # Cada día a las 3:00 AM
Command:  node scripts/limpiar-tareas-viejas.js
```

---

## 16. Precios y Plan Gratuito

### Estructura de precios de Railway

| Plan | Precio | Créditos | Límites |
|------|--------|----------|---------|
| **Trial** | Gratis | $5 USD | Sin tarjeta de crédito. Se consumen con el uso |
| **Hobby** | $5/mes | $5 incluidos + pay-per-use | Hasta 8 GB RAM, 8 vCPU, SSL, dominios |
| **Pro** | $20/mes | $20 incluidos + pay-per-use | Hasta 32 GB RAM, soporte prioritario |
| **Enterprise** | Custom | Personalizado | SLA, soporte dedicado, SAML |

### ¿Cuánto consume un proyecto típico?

```
Backend Node.js (Express, tráfico moderado):
  CPU:     ~0.1-0.3 vCPU   → ~$0.02-0.06/hora
  RAM:     ~128-256 MB     → ~$0.002-0.004/hora
  Costo/mes aprox:         → $2-5 USD

PostgreSQL (BD de desarrollo/producción baja):
  CPU:     ~0.05 vCPU      → ~$0.01/hora
  RAM:     ~256 MB         → ~$0.004/hora
  Storage: ~1 GB           → ~$0.25/GB/mes
  Costo/mes aprox:         → $3-7 USD

Total proyecto simple:     → $5-12 USD/mes
```

### Optimizar costos

```bash
# ✅ Usa el sleep mode (Railway lo activa automáticamente en Hobby para
#    servicios sin tráfico)

# ✅ Ajusta los límites de recursos según necesites
# No pongas 8 GB de RAM si tu app usa 256 MB

# ✅ Borra proyectos y servicios que no uses

# ✅ Usa el plan Trial para aprendizaje y demos

# ✅ Monitoriza el consumo desde:
# Dashboard → Usage → Current month
```

### Ver el uso en tiempo real

1. Dashboard de Railway → **"Usage"** (esquina inferior izquierda)
2. Verás un desglose por proyecto y servicio
3. Puedes configurar alertas de gasto

---

## 17. Solución de Problemas Comunes

### ❌ El deploy falla: "Build failed"

**Causas y soluciones:**

```bash
# Causa 1: Falta package.json o requirements.txt
# Solución: Asegúrate de que el archivo de dependencias está en la raíz

# Causa 2: El script de build falla
# Ver los logs de build:
railway logs

# Causa 3: Versión de Node.js incompatible
# Solución: Especifica la versión en package.json:
{
  "engines": { "node": ">=18.0.0" }
}
```

### ❌ La app se despliega pero devuelve 502/503

**Causas más comunes:**

```bash
# Causa 1: No escucha en process.env.PORT
# ❌ Mal:
app.listen(3000);
# ✅ Bien:
app.listen(process.env.PORT || 3000);

# Causa 2: No generaste el dominio público
# Solución: Settings → Networking → Generate Domain

# Causa 3: La app tarda mucho en arrancar (healthcheck timeout)
# Solución: Aumenta el timeout en railway.json:
{
  "deploy": { "healthcheckTimeout": 60 }
}
```

### ❌ No se puede conectar a la base de datos

```bash
# Causa 1: DATABASE_URL no está configurada
railway variables  # Verificar que existe DATABASE_URL

# Causa 2: SSL requerido en producción
# Node.js - añade la opción ssl:
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: false }  // Railway requiere SSL
});

# Causa 3: La migración no se ha ejecutado
railway run -- npm run migrate

# Verificar conexión:
railway connect postgresql
```

### ❌ Las variables de entorno no se aplican

```bash
# Las variables se aplican en el PRÓXIMO deploy
# Si cambias una variable, debes hacer redeploy:

# Opción 1: Hacer un nuevo push a GitHub
git commit --allow-empty -m "chore: redeploy para aplicar variables"
git push

# Opción 2: Desde el dashboard
# Dashboard → tu servicio → Deployments → "Redeploy"

# Opción 3: Con CLI
railway up
```

### ❌ El dominio personalizado no funciona

```bash
# Verificar DNS:
nslookup tu-dominio.com
# Debe apuntar a la URL de Railway

# Verificar que el dominio está añadido en Railway:
# Settings → Networking → Custom Domain

# Esperar la propagación DNS:
# Puede tardar hasta 24-48h (normalmente menos de 1h)

# Verificar SSL:
curl -I https://tu-dominio.com
```

### ❌ El servicio se reinicia constantemente

```bash
# Ver los logs para identificar el error:
railway logs --tail

# Causas comunes:
# 1. Error de sintaxis en el código (revisa los logs)
# 2. Variable de entorno faltante (app crashea al arrancar)
# 3. Puerto incorrecto (no usa process.env.PORT)
# 4. La BD no es accesible al arrancar

# Limitar los reinicios en railway.json:
{
  "deploy": {
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 3
  }
}
```

### ❌ El deploy de Node.js es lento

```bash
# Causa: npm install en cada deploy
# Solución: Usar npm ci y cachear node_modules

# railway.json
{
  "build": {
    "buildCommand": "npm ci --production"
  }
}

# O con nixpacks.toml:
[phases.install]
cmds = ["npm ci --production"]
```

---

## 18. Buenas Prácticas

### 🔐 Seguridad

```bash
# ✅ Nunca subas .env a Git
echo ".env" >> .gitignore
echo ".env.*" >> .gitignore

# ✅ Usa .env.example con valores ficticios para el equipo
# ✅ Usa secrets de GitHub Actions para el token de Railway
# ✅ Configura CORS con el dominio exacto, no con *
app.use(cors({ origin: process.env.CORS_ORIGIN }));

# ✅ Usa Helmet en Express para cabeceras de seguridad
const helmet = require('helmet');
app.use(helmet());

# ✅ Nunca loguees contraseñas o tokens
```

### 🚀 Performance

```javascript
// ✅ Activa compresión gzip en Express
const compression = require('compression');
app.use(compression());

// ✅ Configura el pool de conexiones correctamente
const pool = new Pool({
  max: 10,                    // Ajusta según el plan de Railway
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

// ✅ Usa índices en las columnas de búsqueda frecuente
// En la migración:
await pool.query('CREATE INDEX IF NOT EXISTS idx_tareas_completada ON tareas(completada)');
```

### 📋 Estructura del proyecto

```bash
# ✅ Usa railway.json para declarar explícitamente cómo construir y arrancar
# ✅ Define engines en package.json para controlar la versión de Node
# ✅ Usa el mismo archivo .env.example en todos los entornos
# ✅ Separa la lógica de negocio de la configuración (usa variables de entorno)
# ✅ Implementa un endpoint /health robusto que verifique la BD
```

### 🔄 CI/CD

```bash
# ✅ Usa ramas: main (producción) y develop (staging)
# ✅ Nunca hagas deploy directamente a producción sin pasar por staging
# ✅ Ejecuta los tests ANTES de hacer deploy
# ✅ Usa GitHub Actions para el pipeline CI/CD
# ✅ Configura notificaciones de fallos (email o Slack desde GitHub Actions)
```

### 📊 Monitoreo

```bash
# ✅ Implementa logging estructurado (JSON) para facilitar el análisis
# ✅ Usa niveles de log (INFO, WARN, ERROR) correctamente
# ✅ Configura alertas de gasto en Railway para evitar sorpresas
# ✅ Revisa las métricas de CPU y RAM periódicamente
# ✅ Haz backups regulares de la base de datos
```

### 🗄️ Base de datos

```bash
# ✅ Usa migraciones (nunca modifiques la BD a mano en producción)
# ✅ Prueba siempre las migraciones en staging antes que en producción
# ✅ Haz backup antes de ejecutar migraciones destructivas
# ✅ Usa transacciones para operaciones críticas
# ✅ Configura el pool de conexiones correctamente (no abras más conexiones de las necesarias)
```

---

## Resumen: Checklist de Deploy en Railway

```
PRE-DEPLOY
□ El código sube sin errores (npm test / pytest / go test)
□ Existe .gitignore con node_modules/ y .env
□ Existe .env.example con todas las variables necesarias
□ El servidor escucha en process.env.PORT (no en un puerto fijo)
□ Existe un endpoint /health funcional
□ El package.json tiene el script "start" correcto
□ railway.json está configurado (opcional pero recomendado)

DEPLOY EN RAILWAY
□ Proyecto creado en Railway
□ Repo de GitHub conectado
□ Base de datos añadida (si es necesaria)
□ Variables de entorno configuradas
□ Dominio público generado (Settings → Networking → Generate Domain)
□ Migraciones ejecutadas (railway run -- npm run migrate)

POST-DEPLOY
□ /health responde con 200 OK
□ Los endpoints principales funcionan (prueba con curl o Postman)
□ Los logs no muestran errores
□ Las métricas de CPU/RAM están en niveles normales
□ El CORS está configurado correctamente si hay un frontend
```

---

> 📖 **Recursos adicionales:**
> - [Documentación oficial de Railway](https://docs.railway.app)
> - [Railway CLI en GitHub](https://github.com/railwayapp/cli)
> - [Templates de Railway](https://railway.app/templates)
> - [Comunidad de Railway en Discord](https://discord.gg/railway)
> - [Guía Completa de Deployment](./deployment-tutorial.md) — cubre también Render, Heroku, Vercel, Netlify y VPS
