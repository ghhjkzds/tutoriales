# 🎨 Guía Completa de Render

## Índice

1. [¿Qué es Render?](#1-qué-es-render)
2. [Crear cuenta y primeros pasos](#2-crear-cuenta-y-primeros-pasos)
3. [Conceptos clave de Render](#3-conceptos-clave-de-render)
4. [Deploy de un Backend Node.js](#4-deploy-de-un-backend-nodejs)
5. [Deploy de un Backend Python (FastAPI / Django)](#5-deploy-de-un-backend-python-fastapi--django)
6. [Deploy de un Backend Java (Spring Boot)](#6-deploy-de-un-backend-java-spring-boot)
7. [Deploy de un Backend Go](#7-deploy-de-un-backend-go)
8. [Deploy de un Frontend (Static Site)](#8-deploy-de-un-frontend-static-site)
9. [Bases de datos en Render](#9-bases-de-datos-en-render)
10. [Variables de entorno y secretos](#10-variables-de-entorno-y-secretos)
11. [Dominios personalizados y HTTPS](#11-dominios-personalizados-y-https)
12. [Render Blueprints: Infraestructura como Código](#12-render-blueprints-infraestructura-como-código)
13. [CI/CD con Render y GitHub Actions](#13-cicd-con-render-y-github-actions)
14. [Monitoreo, logs y métricas](#14-monitoreo-logs-y-métricas)
15. [Proyecto práctico: API REST + PostgreSQL en Render](#15-proyecto-práctico-api-rest--postgresql-en-render)
16. [Escalado y configuración avanzada](#16-escalado-y-configuración-avanzada)
17. [Precios y plan gratuito](#17-precios-y-plan-gratuito)
18. [Solución de problemas comunes](#18-solución-de-problemas-comunes)
19. [Buenas prácticas](#19-buenas-prácticas)

---

## 1. ¿Qué es Render?

**Render** es una plataforma PaaS (Platform as a Service) moderna que permite desplegar aplicaciones web, APIs, bases de datos, cron jobs y sitios estáticos de forma rápida, directamente desde GitHub o GitLab, sin gestionar servidores.

### ¿Por qué elegir Render?

| Ventaja | Descripción |
|---------|-------------|
| 🆓 **Free tier real** | Servicios web y bases de datos gratuitas sin tarjeta de crédito |
| 🚀 **Deploy automático** | Conectas tu repo y Render despliega en cada `git push` |
| 🗄️ **Bases de datos gestionadas** | PostgreSQL y Redis nativos, con backups automáticos |
| 🔒 **HTTPS gratis** | Certificados SSL/TLS automáticos para todos los servicios |
| 🌐 **Dominios personalizados** | Conecta tu propio dominio con facilidad |
| 📄 **Blueprints** | Define toda tu infraestructura en un fichero `render.yaml` |
| 🔄 **Zero-downtime deploys** | Actualizaciones sin interrupciones para los usuarios |
| 📊 **Logs y métricas** | Monitoreo integrado sin herramientas adicionales |

### Render vs otras plataformas

```
┌──────────────┬────────────┬────────────┬────────────┬──────────────┐
│ Plataforma   │ Free tier  │ Facilidad  │ Lenguajes  │ BD integrada │
├──────────────┼────────────┼────────────┼────────────┼──────────────┤
│ Render       │ ✅ Gratis  │ ⭐⭐⭐⭐   │ Mayoría    │ ✅ Nativo   │
│ Railway      │ $5 crédito │ ⭐⭐⭐⭐⭐ │ Todos      │ ✅ Nativo   │
│ Heroku       │ $5/mes     │ ⭐⭐⭐⭐   │ Todos      │ ✅ Add-ons  │
│ Fly.io       │ Limitado   │ ⭐⭐⭐     │ Docker     │ ✅ Nativo   │
│ DigitalOcean │ No         │ ⭐⭐⭐     │ Todos      │ ✅ Managed  │
│ Vercel       │ ✅ Gratis  │ ⭐⭐⭐⭐⭐ │ Frontend   │ ❌ No       │
└──────────────┴────────────┴────────────┴────────────┴──────────────┘
```

### Tipos de servicios que ofrece Render

```
┌─────────────────────────────────────────────────┐
│                 Render Services                  │
│                                                 │
│  🌐 Web Services      → APIs, backends          │
│  📄 Static Sites      → React, Angular, Vue     │
│  ⚙️  Background Workers → Procesamiento async    │
│  ⏰ Cron Jobs          → Tareas programadas      │
│  🗄️ PostgreSQL         → Base de datos SQL       │
│  🔴 Redis              → Cache y colas           │
│  🐳 Private Services   → Servicios internos      │
└─────────────────────────────────────────────────┘
```

---

## 2. Crear Cuenta y Primeros Pasos

### Paso 1: Registro

1. Ve a [render.com](https://render.com)
2. Haz clic en **"Get Started for Free"**
3. Elige autenticarte con **GitHub** o **GitLab** (recomendado) o con email
4. Autoriza a Render a acceder a tus repositorios cuando te lo solicite

> 💡 **Recomendación:** Usa tu cuenta de GitHub para registrarte. Esto facilita enormemente conectar tus repositorios directamente sin configuración adicional.

### Paso 2: Dashboard principal

Una vez dentro verás el **dashboard de Render** con:

```
┌─────────────────────────────────────────────────┐
│              Render Dashboard                    │
│                                                 │
│  + New  ▼                                       │
│                                                 │
│  📁 Services                                    │
│  ├── 🌐 mi-api-backend    (Web Service)         │
│  ├── 📄 mi-frontend        (Static Site)        │
│  ├── 🗄️ mi-postgres        (PostgreSQL)          │
│  └── ⏰ mi-cron-job        (Cron Job)            │
│                                                 │
│  💳 Free Plan activo                            │
└─────────────────────────────────────────────────┘
```

### Paso 3: Crear tu primer servicio

1. Haz clic en **"+ New"**
2. Elige el tipo de servicio:
   - **Web Service** → backend/API
   - **Static Site** → frontend React/Angular/Vue
   - **Background Worker** → tareas en segundo plano
   - **Cron Job** → tareas programadas
   - **PostgreSQL** → base de datos
   - **Redis** → caché

---

## 3. Conceptos Clave de Render

### Servicios

Un **servicio** en Render es una unidad de despliegue. Cada servicio puede ser un backend, un sitio estático, una base de datos, etc.

```
┌──────────────────────────────────────────────────────┐
│                    Render Project                     │
│                                                      │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────┐   │
│  │ Web Service │  │ Static Site │  │ PostgreSQL │   │
│  │  (API REST) │  │  (React)    │  │   (DB)     │   │
│  │             │  │             │  │            │   │
│  │ :10000      │  │ CDN global  │  │ :5432      │   │
│  └─────────────┘  └─────────────┘  └────────────┘   │
│         │                │                │          │
│         └────────────────┴────────────────┘          │
│                    Se comunican por                   │
│                    Internal Network                   │
└──────────────────────────────────────────────────────┘
```

### Environments

Render no tiene entornos (staging/production) nativos como Railway, pero puedes replicar el patrón creando **servicios separados** o usando **Preview Environments** en el plan Team.

### Regions

Render ofrece las siguientes regiones:

| Región | Código | Ubicación |
|--------|--------|-----------|
| Oregon | `oregon` | EE.UU. Oeste |
| Ohio | `ohio` | EE.UU. Este |
| Frankfurt | `frankfurt` | Europa |
| Singapore | `singapore` | Asia-Pacífico |

> 💡 Para menor latencia, elige la región más cercana a tus usuarios.

### Auto-Deploy

Render detecta cambios en tu rama principal y despliega automáticamente:

```
git push origin main
       │
       ▼
   GitHub/GitLab
       │
       ▼ webhook automático
   Render detecta cambio
       │
       ▼
   Build del proyecto
       │
       ▼
   Deploy sin downtime
       │
       ▼
   Nueva versión en producción ✅
```

---

## 4. Deploy de un Backend Node.js

### Paso 1: Preparar el proyecto

Asegúrate de tener un `package.json` con el script `start`:

```json
{
  "name": "mi-api",
  "version": "1.0.0",
  "scripts": {
    "start": "node index.js",
    "build": "echo 'No build needed'"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

Crea un servidor Express básico en `index.js`:

```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.get('/', (req, res) => {
  res.json({ message: 'API funcionando en Render 🎨' });
});

app.get('/health', (req, res) => {
  res.json({ status: 'ok' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Servidor corriendo en puerto ${PORT}`);
});
```

> ⚠️ **Importante:** Render asigna el puerto mediante la variable `PORT`. Siempre usa `process.env.PORT` en tu app.

### Paso 2: Subir a GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/tu-usuario/mi-api.git
git push -u origin main
```

### Paso 3: Crear el Web Service en Render

1. En el dashboard, haz clic en **"+ New" → "Web Service"**
2. Conecta tu repositorio de GitHub
3. Selecciona el repositorio `mi-api`
4. Configura el servicio:

```
┌─────────────────────────────────────────────────┐
│            Configuración Web Service             │
│                                                 │
│  Name:         mi-api-backend                   │
│  Region:       Frankfurt (EU)                   │
│  Branch:       main                             │
│  Runtime:      Node                             │
│  Build Command: npm install                     │
│  Start Command: npm start                       │
│  Plan:         Free                             │
│                                                 │
└─────────────────────────────────────────────────┘
```

5. Haz clic en **"Create Web Service"**

### Paso 4: Verificar el deploy

Render muestra logs en tiempo real durante el build:

```
==> Cloning from https://github.com/tu-usuario/mi-api
==> Checking out commit abc1234
==> Using Node version 18.17.0
==> Running build command: npm install
    added 57 packages in 2.3s
==> Build successful 🎉
==> Starting service
    Servidor corriendo en puerto 10000
==> Deploy successful!
    Your service is live at: https://mi-api-backend.onrender.com
```

### Paso 5: Probar la API

```bash
curl https://mi-api-backend.onrender.com/
# {"message":"API funcionando en Render 🎨"}

curl https://mi-api-backend.onrender.com/health
# {"status":"ok"}
```

---

## 5. Deploy de un Backend Python (FastAPI / Django)

### FastAPI

#### Estructura del proyecto

```
mi-fastapi/
├── main.py
├── requirements.txt
└── .gitignore
```

#### `main.py`

```python
from fastapi import FastAPI
import os

app = FastAPI(title="Mi API con FastAPI en Render")

@app.get("/")
def root():
    return {"message": "API FastAPI funcionando en Render 🎨"}

@app.get("/health")
def health():
    return {"status": "ok"}
```

#### `requirements.txt`

```
fastapi==0.104.1
uvicorn[standard]==0.24.0
```

#### Configuración en Render

| Campo | Valor |
|-------|-------|
| Runtime | Python 3 |
| Build Command | `pip install -r requirements.txt` |
| Start Command | `uvicorn main:app --host 0.0.0.0 --port $PORT` |

> ⚠️ Usa `--host 0.0.0.0` para que Render pueda enrutar el tráfico correctamente.

---

### Django

#### Estructura del proyecto

```
mi-django/
├── manage.py
├── requirements.txt
├── Procfile
└── mi_proyecto/
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

#### `requirements.txt`

```
django==4.2.7
gunicorn==21.2.0
whitenoise==6.6.0
psycopg2-binary==2.9.9
python-dotenv==1.0.0
```

#### `settings.py` (ajustes para producción)

```python
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = os.environ.get('SECRET_KEY', 'cambia-esto-en-produccion')

DEBUG = os.environ.get('DEBUG', 'False') == 'True'

ALLOWED_HOSTS = ['*']  # En producción, especifica tu dominio

# Base de datos desde variable de entorno
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST'),
        'PORT': os.environ.get('DB_PORT', '5432'),
    }
}

# WhiteNoise para archivos estáticos
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    # ... resto de middleware
]

STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

#### Configuración en Render

| Campo | Valor |
|-------|-------|
| Runtime | Python 3 |
| Build Command | `pip install -r requirements.txt && python manage.py collectstatic --no-input && python manage.py migrate` |
| Start Command | `gunicorn mi_proyecto.wsgi:application` |

---

## 6. Deploy de un Backend Java (Spring Boot)

### Preparar el proyecto

Usa Maven o Gradle. Asegúrate de configurar el puerto:

#### `src/main/resources/application.properties`

```properties
server.port=${PORT:8080}
```

#### Para Maven (`pom.xml`)

Incluye el plugin de Spring Boot:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

### Configuración en Render

| Campo | Valor |
|-------|-------|
| Runtime | Java |
| Build Command | `mvn clean package -DskipTests` |
| Start Command | `java -jar target/*.jar` |

> 💡 Render detecta proyectos Java automáticamente y usa Java 17 por defecto. Puedes cambiar la versión con la variable de entorno `JAVA_VERSION=21`.

### Controlador de ejemplo

```java
@RestController
public class HealthController {

    @GetMapping("/")
    public Map<String, String> root() {
        return Map.of("message", "Spring Boot en Render 🎨");
    }

    @GetMapping("/health")
    public Map<String, String> health() {
        return Map.of("status", "ok");
    }
}
```

---

## 7. Deploy de un Backend Go

### Estructura del proyecto

```
mi-go-api/
├── main.go
├── go.mod
└── go.sum
```

### `main.go`

```go
package main

import (
    "encoding/json"
    "fmt"
    "net/http"
    "os"
)

func main() {
    port := os.Getenv("PORT")
    if port == "" {
        port = "8080"
    }

    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(map[string]string{
            "message": "API Go funcionando en Render 🎨",
        })
    })

    http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        json.NewEncoder(w).Encode(map[string]string{"status": "ok"})
    })

    fmt.Printf("Servidor corriendo en puerto %s\n", port)
    http.ListenAndServe(":"+port, nil)
}
```

### `go.mod`

```
module mi-go-api

go 1.21
```

### Configuración en Render

| Campo | Valor |
|-------|-------|
| Runtime | Go |
| Build Command | `go build -o main .` |
| Start Command | `./main` |

---

## 8. Deploy de un Frontend (Static Site)

Render ofrece hosting gratuito e ilimitado para sitios estáticos, con CDN global.

### React (Create React App)

#### Configuración en Render

1. En el dashboard: **"+ New" → "Static Site"**
2. Conecta tu repositorio de React
3. Configura:

| Campo | Valor |
|-------|-------|
| Build Command | `npm run build` |
| Publish Directory | `build` |

#### Redireccionamiento para SPA

Crea el archivo `public/_redirects`:

```
/*    /index.html    200
```

Esto asegura que las rutas de React Router funcionen correctamente.

---

### Vite (React / Vue / Svelte)

| Campo | Valor |
|-------|-------|
| Build Command | `npm run build` |
| Publish Directory | `dist` |

Archivo `public/_redirects`:

```
/*    /index.html    200
```

---

### Angular

| Campo | Valor |
|-------|-------|
| Build Command | `npm run build -- --configuration production` |
| Publish Directory | `dist/nombre-de-tu-app/browser` |

> 💡 En Angular 17+, el directorio de salida es `dist/nombre-app/browser`.

Archivo `public/_redirects`:

```
/*    /index.html    200
```

---

### Next.js (export estático)

```bash
# next.config.js
const nextConfig = {
  output: 'export',
};
```

| Campo | Valor |
|-------|-------|
| Build Command | `npm run build` |
| Publish Directory | `out` |

> ⚠️ Para Next.js con SSR (Server-Side Rendering), usa un **Web Service** en lugar de Static Site.

---

## 9. Bases de Datos en Render

### PostgreSQL

#### Crear una base de datos PostgreSQL

1. En el dashboard: **"+ New" → "PostgreSQL"**
2. Configura:

```
┌─────────────────────────────────────────────────┐
│          Configuración PostgreSQL                │
│                                                 │
│  Name:     mi-postgres-db                       │
│  Database: mi_database                          │
│  User:     mi_usuario                           │
│  Region:   Frankfurt (EU)                       │
│  Plan:     Free (90 días de prueba)             │
│                                                 │
└─────────────────────────────────────────────────┘
```

3. Haz clic en **"Create Database"**

#### Información de conexión

Render proporciona varias cadenas de conexión:

```
Internal Database URL (desde servicios Render):
postgresql://user:pass@dpg-xxxx-a.frankfurt-postgres.render.com/mi_database

External Database URL (desde tu PC u otros):
postgresql://user:pass@dpg-xxxx-a.frankfurt-postgres.render.com/mi_database

PSQL Command:
psql postgresql://user:pass@dpg-xxxx-a.frankfurt-postgres.render.com/mi_database
```

> 💡 Usa siempre la **Internal Database URL** entre servicios de Render para comunicación interna más rápida y sin costo adicional.

#### Conectar PostgreSQL desde Node.js

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false // requerido por Render
  }
});

async function query(text, params) {
  const client = await pool.connect();
  try {
    return await client.query(text, params);
  } finally {
    client.release();
  }
}

module.exports = { query };
```

#### Conectar PostgreSQL desde Python

```python
import os
import psycopg2
from psycopg2.extras import RealDictCursor

def get_connection():
    return psycopg2.connect(
        os.environ.get("DATABASE_URL"),
        sslmode="require"
    )
```

#### Backups automáticos

Los planes de pago incluyen backups automáticos diarios. En el plan gratuito, debes hacer backups manuales:

```bash
# Exportar base de datos
pg_dump "$DATABASE_URL" > backup.sql

# Restaurar base de datos
psql "$DATABASE_URL" < backup.sql
```

---

### Redis

#### Crear una instancia Redis

1. En el dashboard: **"+ New" → "Redis"**
2. Configura nombre, región y plan
3. Obtén la URL de conexión

#### Usar Redis desde Node.js

```javascript
const { createClient } = require('redis');

const client = createClient({
  url: process.env.REDIS_URL
});

client.on('error', (err) => console.error('Redis error:', err));

await client.connect();

// Guardar valor
await client.set('clave', 'valor', { EX: 3600 }); // expira en 1 hora

// Obtener valor
const valor = await client.get('clave');
```

#### Usar Redis desde Python

```python
import os
import redis

r = redis.from_url(os.environ.get("REDIS_URL"))

# Guardar valor
r.set("clave", "valor", ex=3600)

# Obtener valor
valor = r.get("clave").decode("utf-8")
```

---

## 10. Variables de Entorno y Secretos

### Agregar variables de entorno

1. Ve a tu servicio en Render
2. Pestaña **"Environment"**
3. Haz clic en **"Add Environment Variable"**

```
┌─────────────────────────────────────────────────┐
│            Environment Variables                 │
│                                                 │
│  KEY                  VALUE                     │
│  ─────────────────────────────────────          │
│  DATABASE_URL         postgresql://...          │
│  SECRET_KEY           mi-secreto-seguro         │
│  NODE_ENV             production                │
│  JWT_SECRET           jwt-secreto-largo         │
│  API_KEY              key-de-tercero            │
│                                                 │
│  [+ Add Environment Variable]                   │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Secret Files

Para archivos de configuración completos (como `.env` o `credentials.json`):

1. Pestaña **"Environment"** → **"Secret Files"**
2. Agrega el path y el contenido del archivo

```
Path:    /etc/secrets/.env
Content: DATABASE_URL=postgresql://...
         SECRET_KEY=mi-secreto
```

### Grupos de variables de entorno

Render permite crear **Environment Groups** para compartir variables entre múltiples servicios:

1. En el dashboard: **"Environment Groups"**
2. Crea un grupo con las variables comunes
3. Enlaza el grupo a tus servicios

```
┌─────────────────────────┐
│  Environment Group:     │
│  "shared-production"    │
│                         │
│  DATABASE_URL = ...     │
│  REDIS_URL = ...        │
│  API_KEY = ...          │
└────────┬────────────────┘
         │  enlazado a
    ┌────┴──────┐    ┌──────────────┐
    │ Backend   │    │ Worker       │
    │ Service   │    │ Service      │
    └───────────┘    └──────────────┘
```

### Acceder a variables en el código

```javascript
// Node.js
const dbUrl = process.env.DATABASE_URL;
const secretKey = process.env.SECRET_KEY;
```

```python
# Python
import os
db_url = os.environ.get("DATABASE_URL")
secret_key = os.environ.get("SECRET_KEY")
```

```java
// Java / Spring Boot
@Value("${DATABASE_URL}")
private String databaseUrl;
```

```go
// Go
dbUrl := os.Getenv("DATABASE_URL")
secretKey := os.Getenv("SECRET_KEY")
```

---

## 11. Dominios Personalizados y HTTPS

### Conectar un dominio propio

1. Ve a tu servicio → pestaña **"Settings"**
2. Sección **"Custom Domains"**
3. Haz clic en **"Add Custom Domain"**
4. Introduce tu dominio: `api.miempresa.com`

### Configurar DNS

Render te proporcionará un registro CNAME que debes agregar en tu proveedor DNS:

```
Tipo:   CNAME
Nombre: api
Valor:  mi-api-backend.onrender.com
TTL:    3600
```

Para dominios raíz (`miempresa.com` sin subdominio), usa un registro ALIAS o ANAME:

```
Tipo:   ALIAS (o ANAME)
Nombre: @
Valor:  mi-api-backend.onrender.com
```

### SSL/TLS automático

Render genera y renueva automáticamente certificados SSL/TLS gratuitos usando Let's Encrypt. No necesitas configurar nada adicional.

```
https://api.miempresa.com  ←  certificado SSL automático ✅
```

### Verificar el dominio

```bash
# Verificar propagación DNS
nslookup api.miempresa.com
dig api.miempresa.com CNAME

# Verificar HTTPS
curl -I https://api.miempresa.com
```

---

## 12. Render Blueprints: Infraestructura como Código

Los **Blueprints** permiten definir todos tus servicios de Render en un fichero YAML llamado `render.yaml`. Es la forma recomendada de gestionar infraestructuras complejas.

### Estructura básica

```yaml
# render.yaml
services:
  - type: web
    name: mi-api-backend
    runtime: node
    buildCommand: npm install
    startCommand: npm start
    envVars:
      - key: NODE_ENV
        value: production
      - key: DATABASE_URL
        fromDatabase:
          name: mi-postgres-db
          property: connectionString

databases:
  - name: mi-postgres-db
    plan: free
    databaseName: mi_database
    user: mi_usuario
```

### Blueprint completo: Full-Stack (API + Frontend + DB + Redis)

```yaml
# render.yaml
services:
  # Backend API (Node.js)
  - type: web
    name: backend-api
    runtime: node
    region: frankfurt
    plan: free
    buildCommand: npm install
    startCommand: npm start
    envVars:
      - key: NODE_ENV
        value: production
      - key: PORT
        value: 10000
      - key: DATABASE_URL
        fromDatabase:
          name: app-postgres
          property: connectionString
      - key: REDIS_URL
        fromService:
          name: app-redis
          type: redis
          property: connectionString
      - key: JWT_SECRET
        generateValue: true
    healthCheckPath: /health

  # Frontend (React)
  - type: web
    name: frontend-app
    runtime: static
    buildCommand: npm run build
    staticPublishPath: ./build
    envVars:
      - key: REACT_APP_API_URL
        fromService:
          name: backend-api
          type: web
          property: host
    routes:
      - type: rewrite
        source: /*
        destination: /index.html

  # Background Worker
  - type: worker
    name: email-worker
    runtime: node
    buildCommand: npm install
    startCommand: node workers/emailWorker.js
    envVars:
      - key: REDIS_URL
        fromService:
          name: app-redis
          type: redis
          property: connectionString

  # Cron Job (limpieza diaria)
  - type: cron
    name: daily-cleanup
    runtime: node
    buildCommand: npm install
    startCommand: node scripts/cleanup.js
    schedule: "0 2 * * *"  # Cada día a las 2:00 AM UTC
    envVars:
      - key: DATABASE_URL
        fromDatabase:
          name: app-postgres
          property: connectionString

  # Redis
  - type: redis
    name: app-redis
    plan: free
    region: frankfurt
    maxmemoryPolicy: allkeys-lru

databases:
  - name: app-postgres
    plan: free
    region: frankfurt
    databaseName: app_production
    user: app_user
    postgresMajorVersion: 15
```

### Desplegar un Blueprint

1. Añade `render.yaml` a la raíz de tu repositorio
2. En el dashboard: **"+ New" → "Blueprint"**
3. Conecta el repositorio con el `render.yaml`
4. Render creará todos los servicios automáticamente

```bash
# Añadir render.yaml al repo
git add render.yaml
git commit -m "Add Render Blueprint"
git push origin main
```

---

## 13. CI/CD con Render y GitHub Actions

### Auto-Deploy nativo

Render incluye auto-deploy nativo. Al hacer push a tu rama principal, el deploy se lanza automáticamente. No necesitas GitHub Actions para esto.

### GitHub Actions para workflows avanzados

Sin embargo, puedes usar GitHub Actions para ejecutar tests antes de que Render despliegue:

#### `.github/workflows/ci.yml`

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    name: Tests
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: test_db
          POSTGRES_USER: test_user
          POSTGRES_PASSWORD: test_password
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

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
        env:
          DATABASE_URL: postgresql://test_user:test_password@localhost:5432/test_db
          NODE_ENV: test

      - name: Ejecutar linter
        run: npm run lint

  deploy:
    name: Deploy a Render
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'

    steps:
      - name: Deploy a Render
        env:
          RENDER_API_KEY: ${{ secrets.RENDER_API_KEY }}
          RENDER_SERVICE_ID: ${{ secrets.RENDER_SERVICE_ID }}
        run: |
          curl -X POST \
            "https://api.render.com/v1/services/$RENDER_SERVICE_ID/deploys" \
            -H "Authorization: Bearer $RENDER_API_KEY" \
            -H "Content-Type: application/json" \
            -d '{"clearCache": false}'
```

### Configurar secrets en GitHub

1. En GitHub: **Settings → Secrets and variables → Actions**
2. Añade los siguientes secrets:

| Secret | Cómo obtenerlo |
|--------|----------------|
| `RENDER_API_KEY` | Render Dashboard → Account Settings → API Keys |
| `RENDER_SERVICE_ID` | URL de tu servicio: `https://dashboard.render.com/web/srv-XXXXXXX` |

### Obtener la API Key de Render

1. Ve a [dashboard.render.com](https://dashboard.render.com)
2. Haz clic en tu avatar → **"Account Settings"**
3. Sección **"API Keys"**
4. Haz clic en **"Create API Key"**

---

## 14. Monitoreo, Logs y Métricas

### Ver logs en tiempo real

#### Desde el dashboard

1. Ve a tu servicio
2. Pestaña **"Logs"**
3. Puedes filtrar por nivel: `info`, `warn`, `error`

```
┌─────────────────────────────────────────────────────────┐
│                    Logs del Servicio                     │
│                                                         │
│  [2024-01-15 10:30:00] INFO  Servidor en puerto 10000   │
│  [2024-01-15 10:30:05] INFO  GET /api/users 200 45ms    │
│  [2024-01-15 10:30:10] INFO  POST /api/login 200 120ms  │
│  [2024-01-15 10:30:15] ERROR GET /api/data 500 Error    │
│  [2024-01-15 10:30:20] INFO  GET /health 200 2ms        │
│                                                         │
│  🔍 Filtrar:  [info] [warn] [error]                     │
└─────────────────────────────────────────────────────────┘
```

#### Desde la API de Render

```bash
curl -H "Authorization: Bearer $RENDER_API_KEY" \
  "https://api.render.com/v1/services/$SERVICE_ID/logs?limit=100"
```

### Health Checks

Render verifica la salud de tu servicio con health checks periódicos. Configura el endpoint:

1. Ve a tu servicio → **"Settings"**
2. Sección **"Health & Alerts"**
3. Configura el path: `/health`

Implementa el endpoint en tu app:

```javascript
// Node.js
app.get('/health', async (req, res) => {
  try {
    // Verificar conexión a BD
    await pool.query('SELECT 1');
    res.json({
      status: 'ok',
      timestamp: new Date().toISOString(),
      database: 'connected'
    });
  } catch (error) {
    res.status(503).json({
      status: 'error',
      error: error.message
    });
  }
});
```

```python
# FastAPI
@app.get("/health")
async def health():
    return {
        "status": "ok",
        "timestamp": datetime.utcnow().isoformat()
    }
```

### Métricas y alertas

Render muestra métricas básicas en la pestaña **"Metrics"**:

```
┌─────────────────────────────────────────────────┐
│                   Métricas                       │
│                                                 │
│  CPU Usage:      ▓▓▓░░░░░░░  25%               │
│  Memory:         ▓▓▓▓▓░░░░░  52%               │
│  Requests/min:   ▓▓░░░░░░░░  234               │
│  Error rate:     ▓░░░░░░░░░  0.2%              │
│  Response time:  ▓▓░░░░░░░░  85ms              │
│                                                 │
│  [Last 1h] [Last 24h] [Last 7d]                │
└─────────────────────────────────────────────────┘
```

### Alertas por email

1. Ve a tu servicio → **"Settings"** → **"Notifications"**
2. Configura alertas para:
   - ❌ Deploy fallido
   - 🔴 Servicio caído
   - ⚠️ Uso elevado de CPU/memoria

---

## 15. Proyecto Práctico: API REST + PostgreSQL en Render

Construiremos una **API de gestión de tareas** completa y la desplegaremos en Render.

### Estructura del proyecto

```
task-manager-api/
├── src/
│   ├── routes/
│   │   └── tasks.js
│   ├── db/
│   │   └── pool.js
│   └── middleware/
│       └── auth.js
├── index.js
├── package.json
├── render.yaml
└── .gitignore
```

### `package.json`

```json
{
  "name": "task-manager-api",
  "version": "1.0.0",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "pg": "^8.11.3",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.2",
    "cors": "^2.8.5",
    "helmet": "^7.1.0"
  }
}
```

### `src/db/pool.js`

```javascript
const { Pool } = require('pg');

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: process.env.NODE_ENV === 'production'
    ? { rejectUnauthorized: false }
    : false,
});

// Crear tablas al iniciar
async function initDb() {
  const client = await pool.connect();
  try {
    await client.query(`
      CREATE TABLE IF NOT EXISTS users (
        id SERIAL PRIMARY KEY,
        email VARCHAR(255) UNIQUE NOT NULL,
        password_hash VARCHAR(255) NOT NULL,
        created_at TIMESTAMP DEFAULT NOW()
      );

      CREATE TABLE IF NOT EXISTS tasks (
        id SERIAL PRIMARY KEY,
        user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
        title VARCHAR(255) NOT NULL,
        description TEXT,
        status VARCHAR(50) DEFAULT 'pending',
        created_at TIMESTAMP DEFAULT NOW(),
        updated_at TIMESTAMP DEFAULT NOW()
      );
    `);
    console.log('Base de datos inicializada ✅');
  } finally {
    client.release();
  }
}

module.exports = { pool, initDb };
```

### `src/routes/tasks.js`

```javascript
const express = require('express');
const router = express.Router();
const { pool } = require('../db/pool');

// GET todas las tareas del usuario
router.get('/', async (req, res) => {
  try {
    const result = await pool.query(
      'SELECT * FROM tasks WHERE user_id = $1 ORDER BY created_at DESC',
      [req.userId]
    );
    res.json(result.rows);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// POST crear tarea
router.post('/', async (req, res) => {
  const { title, description } = req.body;
  try {
    const result = await pool.query(
      `INSERT INTO tasks (user_id, title, description)
       VALUES ($1, $2, $3) RETURNING *`,
      [req.userId, title, description]
    );
    res.status(201).json(result.rows[0]);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// PUT actualizar tarea
router.put('/:id', async (req, res) => {
  const { title, description, status } = req.body;
  try {
    const result = await pool.query(
      `UPDATE tasks
       SET title = COALESCE($1, title),
           description = COALESCE($2, description),
           status = COALESCE($3, status),
           updated_at = NOW()
       WHERE id = $4 AND user_id = $5
       RETURNING *`,
      [title, description, status, req.params.id, req.userId]
    );
    if (result.rows.length === 0) {
      return res.status(404).json({ error: 'Tarea no encontrada' });
    }
    res.json(result.rows[0]);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// DELETE eliminar tarea
router.delete('/:id', async (req, res) => {
  try {
    await pool.query(
      'DELETE FROM tasks WHERE id = $1 AND user_id = $2',
      [req.params.id, req.userId]
    );
    res.json({ message: 'Tarea eliminada' });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

module.exports = router;
```

### `index.js`

```javascript
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');
const { pool, initDb } = require('./src/db/pool');
const tasksRouter = require('./src/routes/tasks');

const app = express();

// Middlewares
app.use(helmet());
app.use(cors());
app.use(express.json());

// Middleware de autenticación
function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'Token requerido' });
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.userId = decoded.userId;
    next();
  } catch {
    res.status(401).json({ error: 'Token inválido' });
  }
}

// Rutas públicas
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

app.post('/register', async (req, res) => {
  const { email, password } = req.body;
  try {
    const hash = await bcrypt.hash(password, 10);
    const result = await pool.query(
      'INSERT INTO users (email, password_hash) VALUES ($1, $2) RETURNING id, email',
      [email, hash]
    );
    const token = jwt.sign({ userId: result.rows[0].id }, process.env.JWT_SECRET, { expiresIn: '7d' });
    res.status(201).json({ user: result.rows[0], token });
  } catch (error) {
    res.status(400).json({ error: 'Email ya registrado' });
  }
});

app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  try {
    const result = await pool.query('SELECT * FROM users WHERE email = $1', [email]);
    const user = result.rows[0];
    if (!user || !await bcrypt.compare(password, user.password_hash)) {
      return res.status(401).json({ error: 'Credenciales inválidas' });
    }
    const token = jwt.sign({ userId: user.id }, process.env.JWT_SECRET, { expiresIn: '7d' });
    res.json({ user: { id: user.id, email: user.email }, token });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Rutas protegidas
app.use('/tasks', authenticate, tasksRouter);

// Iniciar servidor
const PORT = process.env.PORT || 3000;
initDb().then(() => {
  app.listen(PORT, () => {
    console.log(`🚀 API corriendo en puerto ${PORT}`);
  });
});
```

### `render.yaml`

```yaml
services:
  - type: web
    name: task-manager-api
    runtime: node
    region: frankfurt
    plan: free
    buildCommand: npm install
    startCommand: npm start
    healthCheckPath: /health
    envVars:
      - key: NODE_ENV
        value: production
      - key: JWT_SECRET
        generateValue: true
      - key: DATABASE_URL
        fromDatabase:
          name: task-manager-db
          property: connectionString

databases:
  - name: task-manager-db
    plan: free
    region: frankfurt
    databaseName: taskmanager
    user: taskmanager_user
```

### Deploy y pruebas

```bash
# 1. Inicializar repositorio
git init
git add .
git commit -m "Task Manager API"
git remote add origin https://github.com/tu-usuario/task-manager-api.git
git push -u origin main

# 2. En Render: New → Blueprint → conectar repositorio

# 3. Probar la API
BASE_URL="https://task-manager-api.onrender.com"

# Registrar usuario
curl -X POST "$BASE_URL/register" \
  -H "Content-Type: application/json" \
  -d '{"email": "usuario@ejemplo.com", "password": "contraseña123"}'

# Login
TOKEN=$(curl -s -X POST "$BASE_URL/login" \
  -H "Content-Type: application/json" \
  -d '{"email": "usuario@ejemplo.com", "password": "contraseña123"}' \
  | jq -r '.token')

# Crear tarea
curl -X POST "$BASE_URL/tasks" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Mi primera tarea", "description": "Descripción de la tarea"}'

# Listar tareas
curl "$BASE_URL/tasks" \
  -H "Authorization: Bearer $TOKEN"
```

---

## 16. Escalado y Configuración Avanzada

### Planes de instancias

| Plan | CPU | RAM | Precio/mes | Uso recomendado |
|------|-----|-----|------------|-----------------|
| Free | Compartida | 512 MB | $0 | Desarrollo, demos |
| Starter | Compartida | 512 MB | $7 | Apps pequeñas |
| Standard | 1 CPU | 2 GB | $25 | Producción básica |
| Pro | 2 CPU | 4 GB | $85 | Producción media |
| Pro Plus | 4 CPU | 8 GB | $175 | Alta disponibilidad |

> ⚠️ **Importante sobre el Free plan:** Los servicios gratuitos se "duermen" tras 15 minutos sin actividad. El primer request tras el sueño puede tardar hasta 30 segundos. Usa el plan Starter para producción.

### Scaling horizontal

En planes Standard y superiores puedes configurar escalado:

```
┌─────────────────────────────────────────────────┐
│              Scaling Configuration               │
│                                                 │
│  Min Instances:  1                              │
│  Max Instances:  5                              │
│                                                 │
│  Scale Up when:   CPU > 80% por 5 min          │
│  Scale Down when: CPU < 20% por 15 min         │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Disk persistente

Para servicios que necesitan almacenamiento persistente (SQLite, archivos subidos, etc.):

```yaml
# render.yaml
services:
  - type: web
    name: mi-app
    runtime: node
    disk:
      name: datos-persistentes
      mountPath: /var/data
      sizeGB: 10
```

### Zero-Downtime Deploys

Render realiza deploys sin downtime por defecto:

```
Deploy iniciado
    │
    ▼
Nueva instancia arranca (mientras la antigua sigue activa)
    │
    ▼
Health check pasa (/health devuelve 200)
    │
    ▼
Tráfico se transfiere a la nueva instancia
    │
    ▼
Instancia antigua se detiene ✅
```

### Preview Environments

En el plan Team, Render puede crear entornos de preview para cada Pull Request:

```yaml
# render.yaml
previewsEnabled: true
previewsExpireAfterDays: 7
```

---

## 17. Precios y Plan Gratuito

### Qué incluye el plan gratuito

| Servicio | Plan Gratuito |
|----------|---------------|
| Web Services | ✅ Gratis (con sleep) |
| Static Sites | ✅ Gratis ilimitado |
| PostgreSQL | ✅ 90 días gratis, luego $7/mes |
| Redis | ✅ 30 días gratis, luego $10/mes |
| Cron Jobs | ✅ Gratis |
| Background Workers | ✅ Gratis (con sleep) |
| Bandwidth | 100 GB/mes gratis |
| Build minutes | 500 min/mes gratis |

### Limitaciones del plan gratuito

```
⚠️ Web Services gratuitos:
   - Se duermen tras 15 min sin tráfico
   - Cold start: hasta 30 segundos
   - 512 MB RAM

⚠️ PostgreSQL gratuito:
   - Solo 90 días
   - 256 MB de almacenamiento
   - Sin backups automáticos

⚠️ Sin dominios personalizados en Static Sites gratuitos
   (usa subdomain.onrender.com)
```

### Estrategia para mantener el servicio despierto (plan gratuito)

```javascript
// Opción 1: Cron job externo (UptimeRobot - gratuito)
// Configura UptimeRobot para hacer ping cada 5 minutos a:
// https://tu-app.onrender.com/health

// Opción 2: Cron job en el propio Render
// Crea un Cron Job que haga ping cada 10 minutos
const https = require('https');
https.get(process.env.SERVICE_URL + '/health', () => {
  console.log('Ping enviado para mantener el servicio activo');
});
```

### Calculadora de costos

Para una app de producción básica:

```
Web Service (Starter):        $7/mes
PostgreSQL (Basic):           $7/mes
Redis (Free → Starter):      $10/mes
                              ──────
Total estimado:              $24/mes
```

---

## 18. Solución de Problemas Comunes

### ❌ Error: "Service failed to start"

**Causa:** El comando de inicio falla o el servicio no escucha en el puerto correcto.

**Solución:**

```bash
# Verifica que tu app usa process.env.PORT
# Node.js
const PORT = process.env.PORT || 3000;
app.listen(PORT);

# Python (uvicorn)
uvicorn main:app --host 0.0.0.0 --port $PORT

# Java
server.port=${PORT:8080}
```

---

### ❌ Error: "Build failed"

**Causa:** Dependencias faltantes o error en el comando de build.

**Diagnóstico:**

```bash
# Verifica tu build command localmente
npm install && npm run build

# Verifica que package.json tiene los scripts correctos
cat package.json | grep scripts
```

**Solución:**

```
# En Render → Settings → Build & Deploy
# Cambia Build Command a:
npm ci  # más confiable que npm install
```

---

### ❌ Error: "Application error: SSL/TLS required"

**Causa:** La conexión a PostgreSQL requiere SSL en producción.

**Solución:**

```javascript
// Node.js con pg
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: {
    rejectUnauthorized: false
  }
});
```

```python
# Python con psycopg2
conn = psycopg2.connect(
    os.environ.get("DATABASE_URL"),
    sslmode="require"
)
```

---

### ❌ Cold Start lento (plan gratuito)

**Causa:** Los servicios gratuitos se duermen tras 15 min de inactividad.

**Solución 1:** Usa UptimeRobot (gratuito) para hacer ping cada 5 minutos.

**Solución 2:** Muestra un mensaje amigable mientras carga:

```javascript
// En el frontend, maneja el cold start
async function fetchWithRetry(url, options = {}, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await fetch(url, { ...options, signal: AbortSignal.timeout(30000) });
      return response;
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await new Promise(resolve => setTimeout(resolve, 5000));
    }
  }
}
```

**Solución 3:** Migra al plan Starter ($7/mes) para eliminar el cold start.

---

### ❌ Variables de entorno no disponibles

**Causa:** Las variables no están configuradas correctamente en Render.

**Diagnóstico:**

```javascript
// Añade temporalmente un endpoint de diagnóstico
app.get('/debug-env', (req, res) => {
  res.json({
    nodeEnv: process.env.NODE_ENV,
    hasDbUrl: !!process.env.DATABASE_URL,
    hasJwtSecret: !!process.env.JWT_SECRET,
  });
});
```

> ⚠️ Elimina este endpoint en producción antes de publicar código sensible.

---

### ❌ Deploy infinito o stuck

**Causa:** El health check falla o la app no responde en tiempo.

**Solución:**

```
1. Verifica los logs del deploy en Render
2. Asegúrate de que /health responde en < 5 segundos
3. Verifica que la app no crashea al iniciar
4. Revisa que todas las variables de entorno estén configuradas
```

---

### ❌ "Out of memory"

**Causa:** La app consume más de 512 MB (límite del plan gratuito).

**Solución:**

```bash
# Node.js: limita el heap
node --max-old-space-size=400 index.js

# O en el start command de Render:
node --max-old-space-size=400 index.js
```

---

## 19. Buenas Prácticas

### 1. Siempre usa variables de entorno para secretos

```javascript
// ❌ Mal
const secret = "mi-secreto-hardcodeado";
const dbUrl = "postgresql://user:pass@host/db";

// ✅ Bien
const secret = process.env.JWT_SECRET;
const dbUrl = process.env.DATABASE_URL;
```

### 2. Implementa un health check robusto

```javascript
app.get('/health', async (req, res) => {
  const checks = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    services: {}
  };

  // Verificar base de datos
  try {
    await pool.query('SELECT 1');
    checks.services.database = 'ok';
  } catch (error) {
    checks.services.database = 'error';
    checks.status = 'degraded';
  }

  const httpStatus = checks.status === 'ok' ? 200 : 503;
  res.status(httpStatus).json(checks);
});
```

### 3. Configura el archivo `.gitignore` correctamente

```
# .gitignore
node_modules/
.env
.env.local
.env.production
*.log
dist/
build/
```

### 4. Usa `render.yaml` para reproducibilidad

Siempre define tu infraestructura en `render.yaml` en lugar de configurar todo desde la UI. Esto te permite recrear toda la infraestructura fácilmente.

### 5. Configura correctamente los CORS

```javascript
const corsOptions = {
  origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
  credentials: true,
  optionsSuccessStatus: 200
};

app.use(cors(corsOptions));
```

### 6. Maneja el graceful shutdown

```javascript
process.on('SIGTERM', async () => {
  console.log('Recibida señal SIGTERM. Cerrando servidor...');
  server.close(async () => {
    await pool.end();
    console.log('Servidor cerrado correctamente.');
    process.exit(0);
  });
});
```

### 7. Usa logging estructurado

```javascript
function log(level, message, meta = {}) {
  console.log(JSON.stringify({
    level,
    message,
    timestamp: new Date().toISOString(),
    ...meta
  }));
}

// Uso
log('info', 'Request recibido', { method: 'GET', path: '/users' });
log('error', 'Error en DB', { error: error.message });
```

### 8. Limita el tamaño de los requests

```javascript
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ limit: '10mb', extended: true }));
```

### 9. Actualiza dependencias regularmente

```bash
# Ver dependencias desactualizadas
npm outdated

# Actualizar dependencias
npm update

# Para actualizaciones mayores
npx npm-check-updates -u && npm install
```

### 10. Monitoriza el estado de tu servicio

Usa servicios externos gratuitos para monitoreo adicional:

| Servicio | Función | Plan gratuito |
|----------|---------|---------------|
| [UptimeRobot](https://uptimerobot.com) | Uptime monitoring | 50 monitores |
| [BetterStack](https://betterstack.com) | Logs + alertas | 1 GB/mes |
| [Sentry](https://sentry.io) | Error tracking | 5k eventos/mes |

---

## Resumen

Render es una plataforma excelente para desplegar aplicaciones desde el primer día, con un generoso plan gratuito para empezar sin coste. Su integración directa con GitHub y los Blueprints como código hacen que el deployment sea reproducible y fácil de mantener.

```
🎨 Render en un vistazo:

✅ Free tier real (sin tarjeta de crédito)
✅ Auto-deploy desde GitHub/GitLab
✅ HTTPS automático para todos los servicios
✅ Bases de datos PostgreSQL y Redis gestionadas
✅ Static Sites con CDN global (gratuito e ilimitado)
✅ Blueprints para infraestructura como código
✅ Zero-downtime deploys en planes de pago
✅ Logs y métricas integradas

⚠️ A tener en cuenta:
⚠️ Servicios gratuitos se duermen tras 15 min
⚠️ PostgreSQL gratuita solo 90 días
⚠️ Sin escalado automático en plan gratuito
```

**Recursos adicionales:**
- 📚 [Documentación oficial de Render](https://render.com/docs)
- 💬 [Comunidad de Render](https://community.render.com)
- 🐙 [Render en GitHub](https://github.com/render-examples)
- 📊 [Estado de los servicios de Render](https://status.render.com)
