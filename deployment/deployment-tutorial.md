# 🚀 Guía Completa de Deployment de Aplicaciones

## Índice

1. [¿Qué es el Deployment?](#1-qué-es-el-deployment)
2. [Conceptos fundamentales](#2-conceptos-fundamentales)
3. [Deployment de Backend](#3-deployment-de-backend)
   - [¿Cómo funciona?](#31-cómo-funciona-el-deployment-de-backend)
   - [Paso a paso](#32-paso-a-paso-deploy-de-un-backend)
   - [Herramientas y plataformas](#33-herramientas-para-backend)
4. [Deployment de Frontend](#4-deployment-de-frontend)
   - [¿Cómo funciona?](#41-cómo-funciona-el-deployment-de-frontend)
   - [Paso a paso](#42-paso-a-paso-deploy-de-un-frontend)
   - [Herramientas y plataformas](#43-herramientas-para-frontend)
5. [CI/CD: Automatizando el Deployment](#5-cicd-automatizando-el-deployment)
6. [Variables de entorno y configuración](#6-variables-de-entorno-y-configuración)
7. [Dominios y certificados SSL](#7-dominios-y-certificados-ssl)
8. [Monitoreo y logs](#8-monitoreo-y-logs)
9. [Proyecto práctico: Deploy completo Full-Stack](#9-proyecto-práctico-deploy-completo-full-stack)
10. [Buenas prácticas](#10-buenas-prácticas)
11. [Solución de problemas comunes](#11-solución-de-problemas-comunes)

---

## 1. ¿Qué es el Deployment?

El **deployment** (o despliegue) es el proceso de hacer que una aplicación esté **disponible para los usuarios finales** en un entorno de producción. Es el paso que convierte tu código local en una aplicación accesible desde internet.

### El ciclo de vida de una aplicación

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                       │
│   👨‍💻 DESARROLLO     🧪 TESTING       🚀 DEPLOYMENT     🌍 PRODUCCIÓN  │
│                                                                       │
│   Tu PC local   →  Entorno pruebas  →  Proceso deploy  →  Internet   │
│   localhost:3000   staging.app.com     CI/CD pipeline    app.com     │
│                                                                       │
└─────────────────────────────────────────────────────────────────────┘
```

### ¿Por qué es importante aprender deployment?

| Razón | Descripción |
|-------|-------------|
| 🌍 **Accesibilidad** | Tu app pasa de estar en tu PC a estar disponible para todo el mundo |
| 💼 **Profesionalismo** | Toda empresa necesita desplegar sus aplicaciones |
| ⚡ **Automatización** | Aprenderás CI/CD para desplegar con un simple `git push` |
| 🔒 **Seguridad** | Gestionarás secretos, HTTPS y accesos seguros |
| 💰 **Ahorro** | Conocer las herramientas adecuadas puede ahorrarte mucho dinero |

### Entornos comunes

```
Development  →  Staging  →  Production
(Local)          (Pre-prod)    (Real)

localhost:3000  staging.miapp.com  miapp.com
```

---

## 2. Conceptos Fundamentales

### Tipos de hosting

| Tipo | Descripción | Ejemplo | Ideal para |
|------|-------------|---------|------------|
| **Shared Hosting** | Varios sitios comparten un servidor | Hostinger, GoDaddy | Sitios web simples |
| **VPS** | Servidor virtual dedicado a ti | DigitalOcean, Linode | Apps con más control |
| **PaaS** | Plataforma gestionada | Heroku, Railway, Render | Developers que quieren simplicidad |
| **Serverless** | Funciones sin servidor | AWS Lambda, Vercel | APIs y funciones puntuales |
| **CDN/Static** | Archivos estáticos globales | Vercel, Netlify, Cloudflare | Frontends estáticos |

### Arquitectura de una aplicación web desplegada

```
┌─────────────────────────────────────────────────────────────────────┐
│                         INTERNET                                      │
└─────────────────────────────┬───────────────────────────────────────┘
                              │
                    ┌─────────▼──────────┐
                    │   DNS / Dominio     │
                    │   miapp.com         │
                    └─────────┬──────────┘
                              │
              ┌───────────────┴──────────────┐
              │                              │
    ┌─────────▼──────────┐      ┌────────────▼──────────┐
    │   FRONTEND CDN      │      │   BACKEND SERVER      │
    │   Vercel/Netlify    │      │   Railway/Render/VPS  │
    │   React/Vue/Angular │      │   Node/Python/Java    │
    │   miapp.com         │      │   api.miapp.com       │
    └────────────────────┘      └────────────┬──────────┘
                                             │
                                  ┌──────────▼─────────┐
                                  │    BASE DE DATOS    │
                                  │  PostgreSQL/MongoDB │
                                  │  (Managed DB)       │
                                  └────────────────────┘
```

---

## 3. Deployment de Backend

### 3.1 ¿Cómo funciona el deployment de backend?

El backend es el **servidor** que procesa la lógica de negocio, gestiona la base de datos y expone una API. Al hacer deploy de un backend:

1. **Tu código** se sube a un servidor remoto
2. El servidor **instala las dependencias** (npm install, pip install, etc.)
3. El servidor **ejecuta tu aplicación** en un puerto específico
4. Un **proxy inverso** (Nginx, Caddy) redirige el tráfico HTTP/HTTPS a tu app
5. La app queda **escuchando peticiones** 24/7

```
Usuario  →  HTTPS:443  →  Nginx/Proxy  →  Tu App:3000  →  BD
         ←  Respuesta  ←               ←               ←
```

### 3.2 Paso a paso: Deploy de un backend

#### Ejemplo con Node.js/Express

**1. Prepara tu aplicación**

```javascript
// app.js - Asegúrate de usar process.env.PORT
const express = require('express');
const app = express();

const PORT = process.env.PORT || 3000;

app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date() });
});

app.get('/api/usuarios', (req, res) => {
  res.json([{ id: 1, nombre: 'Ana' }, { id: 2, nombre: 'Juan' }]);
});

app.listen(PORT, () => {
  console.log(`Servidor corriendo en el puerto ${PORT}`);
});
```

```json
// package.json - Define el script de inicio
{
  "name": "mi-backend",
  "version": "1.0.0",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

**2. Crea un Dockerfile (recomendado)**

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

# Copia dependencias primero (mejor caché)
COPY package*.json ./
RUN npm ci --only=production

# Copia el resto del código
COPY . .

# Expone el puerto
EXPOSE 3000

# Comando de inicio
CMD ["node", "app.js"]
```

**3. Crea un .gitignore y .dockerignore**

```gitignore
# .gitignore
node_modules/
.env
.env.local
dist/
*.log
```

```dockerignore
# .dockerignore
node_modules/
.env
.git
*.log
README.md
```

**4. Configura las variables de entorno**

```bash
# .env (NUNCA subir a Git)
PORT=3000
DATABASE_URL=postgresql://usuario:password@host:5432/mibd
JWT_SECRET=mi_secreto_muy_seguro
NODE_ENV=production
```

**5. Verifica que funciona localmente**

```bash
# Instala dependencias
npm install

# Ejecuta en modo producción
NODE_ENV=production npm start

# Comprueba el endpoint de salud
curl http://localhost:3000/health
```

#### Ejemplo con Python/FastAPI

```python
# main.py
from fastapi import FastAPI
import os

app = FastAPI()

@app.get("/health")
def health_check():
    return {"status": "OK"}

@app.get("/api/usuarios")
def get_usuarios():
    return [{"id": 1, "nombre": "Ana"}, {"id": 2, "nombre": "Juan"}]

if __name__ == "__main__":
    import uvicorn
    port = int(os.environ.get("PORT", 8000))
    uvicorn.run(app, host="0.0.0.0", port=port)
```

```txt
# requirements.txt
fastapi==0.104.1
uvicorn==0.24.0
```

```dockerfile
# Dockerfile para Python
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

#### Ejemplo con Java/Spring Boot

```java
// src/main/java/com/ejemplo/Application.java
@SpringBootApplication
@RestController
public class Application {

    @GetMapping("/health")
    public Map<String, String> health() {
        return Map.of("status", "OK");
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

```yaml
# src/main/resources/application.properties o application.yml
server:
  port: ${PORT:8080}

spring:
  datasource:
    url: ${DATABASE_URL}
```

```dockerfile
# Dockerfile multi-stage para Spring Boot
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

### 3.3 Herramientas para Backend

#### 🚂 Railway

> 📖 **¿Quieres una guía completa y detallada de Railway?** Consulta la **[Guía Completa de Deploy con Railway](./railway-guia-completa.md)**, que cubre Node.js, Python, Java y Go; bases de datos integradas, CLI, CI/CD con GitHub Actions y mucho más.

**Railway** es una de las plataformas más sencillas y modernas para desplegar backends.

| Característica | Detalle |
|----------------|---------|
| **Precio** | Free tier generoso, ~$5/mes para uso moderado |
| **Lenguajes** | Node.js, Python, Java, Go, Ruby, PHP y más |
| **Base de datos** | PostgreSQL, MySQL, MongoDB, Redis integrados |
| **Deploy** | Automático desde GitHub |
| **URL** | railway.app |

```bash
# Instalar Railway CLI
npm install -g @railway/cli

# Login
railway login

# Inicializar proyecto
railway init

# Desplegar
railway up

# Ver logs
railway logs
```

**Deploy desde GitHub (sin CLI):**
1. Ve a [railway.app](https://railway.app) y crea cuenta
2. Haz clic en **"New Project"** → **"Deploy from GitHub repo"**
3. Selecciona tu repositorio
4. Railway detecta automáticamente el tipo de proyecto
5. Añade las variables de entorno en el panel
6. ¡Listo! Tu backend estará desplegado en minutos

#### 🎨 Render

**Render** ofrece un free tier permanente para backends y es muy popular.

| Característica | Detalle |
|----------------|---------|
| **Precio** | Free tier (con spin-down), desde $7/mes para always-on |
| **Lenguajes** | Node.js, Python, Ruby, Go, PHP, Rust |
| **Base de datos** | PostgreSQL integrado |
| **Deploy** | Automático desde GitHub/GitLab |
| **URL** | render.com |

```yaml
# render.yaml - Configuración como código
services:
  - type: web
    name: mi-backend
    env: node
    buildCommand: npm install
    startCommand: npm start
    envVars:
      - key: NODE_ENV
        value: production
      - key: DATABASE_URL
        fromDatabase:
          name: mi-base-datos
          property: connectionString

databases:
  - name: mi-base-datos
    plan: free
```

**Deploy desde el panel:**
1. Ve a [render.com](https://render.com) y crea cuenta
2. **"New"** → **"Web Service"**
3. Conecta tu repositorio de GitHub
4. Configura:
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
5. Añade variables de entorno
6. Haz clic en **"Create Web Service"**

#### ☁️ Heroku

**Heroku** fue el pionero del PaaS y sigue siendo muy usado.

| Característica | Detalle |
|----------------|---------|
| **Precio** | Desde $5/mes (eliminó el free tier en 2022) |
| **Lenguajes** | Node.js, Python, Java, Ruby, PHP, Go, Scala |
| **Add-ons** | Cientos de integraciones (BD, monitoring, etc.) |
| **Deploy** | Git push o GitHub Actions |
| **URL** | heroku.com |

```bash
# Instalar Heroku CLI
npm install -g heroku

# Login
heroku login

# Crear app
heroku create mi-app-nombre

# Desplegar (hace git push al remote de heroku)
git push heroku main

# Ver logs en tiempo real
heroku logs --tail

# Configurar variables de entorno
heroku config:set DATABASE_URL=postgresql://...
heroku config:set JWT_SECRET=mi_secreto

# Abrir la app
heroku open
```

```
# Procfile - Le dice a Heroku cómo iniciar tu app
web: node app.js
worker: node worker.js
```

#### 🌊 DigitalOcean App Platform

| Característica | Detalle |
|----------------|---------|
| **Precio** | Desde $5/mes para apps básicas |
| **Control** | Mayor control que Heroku/Railway |
| **Droplets** | También ofrece VPS (Droplets) |
| **Base de datos** | Managed databases integradas |
| **URL** | digitalocean.com |

#### 🖥️ VPS (Servidor Virtual Privado)

Para mayor control, puedes usar un **VPS** con DigitalOcean, Linode/Akamai, Vultr o Hetzner.

```bash
# 1. Conéctate por SSH a tu VPS
ssh root@IP_DE_TU_VPS

# 2. Actualiza el sistema
apt update && apt upgrade -y

# 3. Instala Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
apt install -y nodejs

# 4. Instala PM2 (gestor de procesos para Node.js)
npm install -g pm2

# 5. Clona tu proyecto
git clone https://github.com/tu-usuario/tu-backend.git
cd tu-backend
npm install --production

# 6. Configura variables de entorno
nano .env
# Añade tus variables...

# 7. Inicia con PM2
pm2 start app.js --name "mi-backend"
pm2 startup  # Para que reinicie con el servidor
pm2 save

# 8. Instala y configura Nginx como proxy inverso
apt install -y nginx

nano /etc/nginx/sites-available/mi-backend
```

```nginx
# /etc/nginx/sites-available/mi-backend
server {
    listen 80;
    server_name api.miapp.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
# Activa la configuración de Nginx
ln -s /etc/nginx/sites-available/mi-backend /etc/nginx/sites-enabled/
nginx -t  # Verifica la configuración
systemctl reload nginx

# 9. Configura SSL con Let's Encrypt (HTTPS)
apt install -y certbot python3-certbot-nginx
certbot --nginx -d api.miapp.com
```

#### Comparativa de plataformas de Backend

| Plataforma | Precio inicial | Facilidad | Control | Free tier | Ideal para |
|------------|---------------|-----------|---------|-----------|------------|
| **Railway** | ~$5/mes | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ✅ (limitado) | Proyectos rápidos |
| **Render** | $0 (con limits) | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ✅ (con spin-down) | Proyectos personales |
| **Heroku** | $5/mes | ⭐⭐⭐⭐ | ⭐⭐⭐ | ❌ | Proyectos de equipo |
| **Fly.io** | $0 (con limits) | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ✅ | Apps con Docker |
| **DigitalOcean** | $5/mes | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ❌ | Control avanzado |
| **VPS propio** | $4-10/mes | ⭐⭐ | ⭐⭐⭐⭐⭐ | ❌ | Máximo control |
| **AWS/GCP/Azure** | Variable | ⭐⭐ | ⭐⭐⭐⭐⭐ | ✅ (limitado) | Empresas grandes |

---

## 4. Deployment de Frontend

### 4.1 ¿Cómo funciona el deployment de frontend?

Los frontends modernos (React, Vue, Angular, etc.) se **compilan** en archivos estáticos (HTML, CSS y JavaScript). El proceso es:

1. **Build:** El framework compila el código fuente en archivos estáticos optimizados
2. **Upload:** Los archivos se suben a un servidor o CDN
3. **CDN:** Los archivos se distribuyen por servidores alrededor del mundo (Content Delivery Network)
4. **El usuario** recibe los archivos desde el servidor más cercano a él

```
                          ┌─────────────────┐
  npm run build           │   dist/          │
  ─────────────────►      │   ├── index.html │
  Compila tu código       │   ├── main.js    │
                          │   ├── styles.css │
                          │   └── assets/   │
                          └────────┬────────┘
                                   │ Upload
                                   ▼
                    ┌──────────────────────────┐
                    │         CDN Global        │
                    │  🌍 Europa  🌎 América    │
                    │  🌏 Asia    🌍 África     │
                    └──────────────────────────┘
                                   │
                    ┌──────────────┴─────────────┐
                    │  Usuario recibe archivos    │
                    │  desde el servidor más      │
                    │  cercano → muy rápido ⚡    │
                    └────────────────────────────┘
```

### 4.2 Paso a paso: Deploy de un frontend

#### Preparando un proyecto React

```bash
# Crea un proyecto nuevo
npx create-react-app mi-frontend
cd mi-frontend

# O con Vite (más moderno y rápido)
npm create vite@latest mi-frontend -- --template react
cd mi-frontend
npm install
```

```javascript
// src/App.jsx - Configura la URL del backend usando variables de entorno
import { useState, useEffect } from 'react';

function App() {
  const [usuarios, setUsuarios] = useState([]);
  
  // Usa la variable de entorno para la URL del backend
  const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000';

  useEffect(() => {
    fetch(`${API_URL}/api/usuarios`)
      .then(res => res.json())
      .then(data => setUsuarios(data));
  }, []);

  return (
    <div>
      <h1>Mi Aplicación</h1>
      <ul>
        {usuarios.map(u => <li key={u.id}>{u.nombre}</li>)}
      </ul>
    </div>
  );
}

export default App;
```

```bash
# .env (para desarrollo local)
VITE_API_URL=http://localhost:3000

# .env.production (para producción)
VITE_API_URL=https://api.miapp.com
```

```bash
# Comprueba que el build funciona correctamente
npm run build

# Verás algo así:
# dist/index.html                   0.46 kB
# dist/assets/index-DiwrgTda.css    1.39 kB
# dist/assets/index-C8E0ajhA.js    142.36 kB
```

#### Preparando un proyecto Vue.js

```bash
npm create vue@latest mi-frontend
cd mi-frontend
npm install
npm run build  # Genera la carpeta dist/
```

```javascript
// vite.config.js
export default {
  build: {
    outDir: 'dist'
  }
}
```

#### Preparando un proyecto Angular

```bash
ng new mi-frontend
cd mi-frontend
ng build --configuration production
# Genera la carpeta dist/mi-frontend/
```

```json
// angular.json - Configura el output
{
  "projects": {
    "mi-frontend": {
      "architect": {
        "build": {
          "options": {
            "outputPath": "dist/mi-frontend"
          }
        }
      }
    }
  }
}
```

### 4.3 Herramientas para Frontend

#### ▲ Vercel

**Vercel** es la plataforma líder para frontends modernos, creada por el equipo de Next.js.

| Característica | Detalle |
|----------------|---------|
| **Precio** | Free tier muy generoso, Pro desde $20/mes |
| **Frameworks** | Next.js, React, Vue, Angular, Svelte, Nuxt y más |
| **CDN** | Red global ultrarrápida |
| **Deploy** | Automático con cada push a GitHub |
| **Preview** | URLs de preview por cada PR |
| **URL** | vercel.com |

```bash
# Instalar Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy desde tu proyecto
cd mi-frontend
vercel

# El CLI te preguntará:
# ? Set up and deploy "mi-frontend"? [Y/n] Y
# ? Which scope? Tu cuenta
# ? Link to existing project? No
# ? What's your project's name? mi-frontend
# ? In which directory is your code located? ./

# Deploy a producción
vercel --prod
```

**Deploy desde GitHub (recomendado):**
1. Ve a [vercel.com](https://vercel.com) y crea cuenta con GitHub
2. Haz clic en **"New Project"**
3. Importa tu repositorio de GitHub
4. Vercel detecta el framework automáticamente
5. Configura las variables de entorno (ej: `VITE_API_URL`)
6. Haz clic en **"Deploy"**
7. Desde ahora, cada `git push` a `main` desplegará automáticamente ✨

```json
// vercel.json - Configuración avanzada (opcional)
{
  "framework": "vite",
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "installCommand": "npm install",
  "env": {
    "VITE_API_URL": "@api_url"
  },
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

#### 🌐 Netlify

**Netlify** fue pionero en el despliegue de sitios estáticos y sigue siendo excelente.

| Característica | Detalle |
|----------------|---------|
| **Precio** | Free tier generoso, Pro desde $19/mes |
| **Frameworks** | Todos los frameworks de frontend |
| **Funciones** | Netlify Functions (serverless) |
| **Forms** | Manejo de formularios sin backend |
| **Deploy** | Drag & drop, Git o CLI |
| **URL** | netlify.com |

```bash
# Instalar Netlify CLI
npm install -g netlify-cli

# Login
netlify login

# Deploy manual (subir carpeta dist)
netlify deploy --dir=dist

# Deploy a producción
netlify deploy --dir=dist --prod

# O inicializar y conectar con GitHub
netlify init
```

```toml
# netlify.toml - Configuración
[build]
  base = ""
  command = "npm run build"
  publish = "dist"

[build.environment]
  NODE_VERSION = "18"

# Redirige todas las rutas al index.html (necesario para SPAs)
[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

**Deploy con drag & drop:**
1. Ejecuta `npm run build` en tu proyecto
2. Ve a [netlify.com](https://netlify.com) y crea cuenta
3. Arrastra la carpeta `dist/` al área de deploy
4. ¡Tu sitio estará en línea en segundos!

#### 📄 GitHub Pages

**GitHub Pages** es gratuito e ideal para proyectos personales y de código abierto.

| Característica | Detalle |
|----------------|---------|
| **Precio** | Completamente **gratis** |
| **Limitaciones** | Solo sitios estáticos, 1GB de almacenamiento |
| **URL** | usuario.github.io/repositorio |
| **Deploy** | Desde una rama de Git o carpeta |
| **URL** | pages.github.com |

```bash
# Para React/Vite: instala gh-pages
npm install --save-dev gh-pages
```

```json
// package.json - Añade el homepage y scripts
{
  "homepage": "https://tu-usuario.github.io/mi-frontend",
  "scripts": {
    "predeploy": "npm run build",
    "deploy": "gh-pages -d dist"
  }
}
```

```bash
# Despliega en GitHub Pages
npm run deploy
```

**Con GitHub Actions (recomendado):**

```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install and Build
        run: |
          npm install
          npm run build
        env:
          VITE_API_URL: ${{ secrets.VITE_API_URL }}

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

#### ☁️ Cloudflare Pages

**Cloudflare Pages** ofrece una CDN increíblemente rápida y un free tier muy generoso.

| Característica | Detalle |
|----------------|---------|
| **Precio** | Free tier: proyectos ilimitados, 500 builds/mes |
| **CDN** | La más rápida del mercado (300+ ubicaciones) |
| **Deploy** | Automático desde GitHub/GitLab |
| **Preview** | URLs de preview por PR |
| **URL** | pages.cloudflare.com |

```bash
# Instalar Wrangler CLI (herramienta de Cloudflare)
npm install -g wrangler

# Login
wrangler login

# Deploy
wrangler pages deploy dist
```

#### Comparativa de plataformas de Frontend

| Plataforma | Precio | Velocidad CDN | Facilidad | Frameworks | Free tier |
|------------|--------|---------------|-----------|------------|-----------|
| **Vercel** | Free/$20+ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Todos | ✅ Muy generoso |
| **Netlify** | Free/$19+ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Todos | ✅ Generoso |
| **Cloudflare Pages** | Free/$20+ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Todos | ✅ Muy generoso |
| **GitHub Pages** | Gratis | ⭐⭐⭐ | ⭐⭐⭐⭐ | Estáticos | ✅ Siempre gratis |
| **Firebase Hosting** | Free/$25+ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Todos | ✅ Generoso |
| **AWS S3 + CloudFront** | ~$1-5/mes | ⭐⭐⭐⭐⭐ | ⭐⭐ | Todos | ✅ Limitado |

---

## 5. CI/CD: Automatizando el Deployment

**CI/CD** (Integración Continua / Entrega Continua) automatiza el proceso de testeo y despliegue.

```
git push  →  Tests automáticos  →  Build  →  Deploy automático
```

### Pipeline completo con GitHub Actions

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # ─── JOB 1: Tests ───────────────────────────────────────────
  test:
    name: 🧪 Tests
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

  # ─── JOB 2: Deploy Backend ──────────────────────────────────
  deploy-backend:
    name: 🚀 Deploy Backend
    needs: test  # Solo despliega si los tests pasan
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Railway
        uses: bervProject/railway-deploy@main
        with:
          railway_token: ${{ secrets.RAILWAY_TOKEN }}
          service: mi-backend

  # ─── JOB 3: Deploy Frontend ─────────────────────────────────
  deploy-frontend:
    name: 🌐 Deploy Frontend
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install and Build
        run: |
          npm ci
          npm run build
        env:
          VITE_API_URL: ${{ secrets.VITE_API_URL }}

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

### Pipeline simplificado: Railway + Vercel automático

La mayoría de veces **no necesitas escribir el pipeline manualmente**. Railway y Vercel se conectan directamente a GitHub:

```
GitHub repo
├── backend/    ← Railway monitoriza esta carpeta
└── frontend/   ← Vercel monitoriza esta carpeta

Cada git push a main → deploy automático en ambos 🎉
```

---

## 6. Variables de Entorno y Configuración

Las variables de entorno son **claves-valor** que configuran tu aplicación sin exponer secretos en el código.

### ⚠️ Regla de oro: NUNCA subas secretos a Git

```bash
# ❌ MAL - Nunca hagas esto
DATABASE_URL = "postgresql://admin:password123@host/db"  # En el código

# ✅ BIEN - Usa variables de entorno
DATABASE_URL = process.env.DATABASE_URL
```

### Gestión en cada plataforma

**Railway:**
```bash
# CLI
railway variables set DATABASE_URL=postgresql://...

# O desde el panel web: Settings → Variables
```

**Render:**
```bash
# Desde el panel: Environment → Environment Variables
# O con render.yaml (ver sección 3.3)
```

**Vercel:**
```bash
# CLI
vercel env add VITE_API_URL

# O desde el panel: Settings → Environment Variables
```

**Netlify:**
```bash
# CLI
netlify env:set VITE_API_URL https://api.miapp.com

# O desde el panel: Site settings → Environment variables
```

### En GitHub Actions

```yaml
# Añade secrets en: Repo → Settings → Secrets and variables → Actions
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
  JWT_SECRET: ${{ secrets.JWT_SECRET }}
  VITE_API_URL: ${{ secrets.VITE_API_URL }}
```

### Estructura recomendada de archivos .env

```bash
# .env.example (SÍ subir a Git - sirve como plantilla)
PORT=3000
DATABASE_URL=postgresql://usuario:password@host:5432/nombre_bd
JWT_SECRET=tu_secreto_aqui
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173

# .env (NO subir a Git - valores reales locales)
PORT=3000
DATABASE_URL=postgresql://postgres:mipassword@localhost:5432/miapp
JWT_SECRET=abc123xyz789secreto
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173

# .env.production (NO subir a Git)
DATABASE_URL=postgresql://usuario:pass@prod-host:5432/miapp_prod
JWT_SECRET=secreto_super_seguro_de_produccion
NODE_ENV=production
CORS_ORIGIN=https://miapp.com
```

---

## 7. Dominios y Certificados SSL

### Conectar tu dominio personalizado

Cuando despliegas en Railway, Vercel o Render, obtienes URLs automáticas como:
- `mi-backend.up.railway.app`
- `mi-frontend.vercel.app`

Para usar tu propio dominio (`miapp.com`):

**En Vercel:**
1. Ve a tu proyecto → **Domains**
2. Añade `miapp.com` y `www.miapp.com`
3. Vercel te dará registros DNS para configurar

**En tu proveedor de dominio (Namecheap, GoDaddy, Cloudflare, etc.):**
```
# Para apuntar el dominio al frontend (Vercel):
Tipo: CNAME
Nombre: www
Valor: cname.vercel-dns.com

# Para el apex domain (@):
Tipo: A
Nombre: @
Valor: 76.76.21.21  (IP de Vercel)

# Para el backend (Railway):
Tipo: CNAME
Nombre: api
Valor: mi-backend.up.railway.app
```

### SSL/HTTPS automático

Todas las plataformas modernas (Vercel, Netlify, Render, Railway) gestionan el **certificado SSL automáticamente** usando Let's Encrypt. Solo tienes que añadir el dominio y ellas hacen el resto.

```
http://miapp.com  →  Se redirige automáticamente a  →  https://miapp.com ✅
```

---

## 8. Monitoreo y Logs

### Ver logs en tiempo real

**Railway:**
```bash
railway logs                    # Logs del servicio activo
railway logs --tail             # Logs en tiempo real
```

**Render:**
```bash
# Dashboard → Tu servicio → Logs
# O con la API de Render
```

**Heroku:**
```bash
heroku logs --tail              # Logs en tiempo real
heroku logs -n 200              # Últimas 200 líneas
```

**VPS con PM2:**
```bash
pm2 logs                        # Logs de todos los servicios
pm2 logs mi-backend             # Logs de un servicio específico
pm2 logs --lines 100            # Últimas 100 líneas
pm2 monit                       # Monitor interactivo en tiempo real
```

### Herramientas de monitoreo

| Herramienta | Descripción | Precio |
|-------------|-------------|--------|
| **Sentry** | Captura errores automáticamente | Free tier disponible |
| **LogRocket** | Replay de sesiones + logs | Free tier disponible |
| **Datadog** | Monitoreo completo (APM, logs, métricas) | De pago |
| **New Relic** | APM y observabilidad | Free tier disponible |
| **UptimeRobot** | Alertas cuando tu app cae | Free tier disponible |
| **Grafana** | Dashboards personalizados | Open source |

### Añadir Sentry a tu backend (Node.js)

```bash
npm install @sentry/node
```

```javascript
// app.js
const Sentry = require("@sentry/node");

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
});

// Middleware para capturar errores
app.use(Sentry.Handlers.requestHandler());
// ... tus rutas ...
app.use(Sentry.Handlers.errorHandler());
```

---

## 9. Proyecto Práctico: Deploy Completo Full-Stack

Vamos a desplegar una aplicación completa con:
- **Backend:** Node.js + Express en Railway
- **Frontend:** React + Vite en Vercel
- **Base de datos:** PostgreSQL en Railway

### Estructura del proyecto

```
mi-app-fullstack/
├── backend/
│   ├── src/
│   │   ├── app.js
│   │   └── routes/
│   ├── package.json
│   ├── Dockerfile
│   └── .env.example
├── frontend/
│   ├── src/
│   │   └── App.jsx
│   ├── package.json
│   ├── vite.config.js
│   └── .env.example
└── .github/
    └── workflows/
        └── deploy.yml
```

### Paso 1: Prepara el backend

```javascript
// backend/src/app.js
const express = require('express');
const cors = require('cors');
require('dotenv').config();

const app = express();

// Configura CORS para permitir peticiones del frontend
app.use(cors({
  origin: process.env.CORS_ORIGIN || 'http://localhost:5173'
}));

app.use(express.json());

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK', env: process.env.NODE_ENV });
});

// API de ejemplo
app.get('/api/tareas', (req, res) => {
  res.json([
    { id: 1, titulo: 'Aprender deployment', completada: true },
    { id: 2, titulo: 'Desplegar mi app', completada: false },
  ]);
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Backend en puerto ${PORT}`));
```

```json
// backend/package.json
{
  "name": "mi-backend",
  "version": "1.0.0",
  "scripts": {
    "start": "node src/app.js",
    "dev": "nodemon src/app.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "dotenv": "^16.3.1",
    "express": "^4.18.2"
  }
}
```

```
# backend/.env.example
PORT=3000
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173
DATABASE_URL=postgresql://usuario:password@localhost:5432/miapp
```

### Paso 2: Prepara el frontend

```jsx
// frontend/src/App.jsx
import { useState, useEffect } from 'react';

const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000';

export default function App() {
  const [tareas, setTareas] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(`${API_URL}/api/tareas`)
      .then(res => res.json())
      .then(data => {
        setTareas(data);
        setLoading(false);
      })
      .catch(err => {
        console.error('Error conectando con el backend:', err);
        setLoading(false);
      });
  }, []);

  return (
    <div style={{ fontFamily: 'Arial', maxWidth: '600px', margin: '2rem auto' }}>
      <h1>🚀 Mi App Desplegada</h1>
      <p>Backend: <code>{API_URL}</code></p>
      {loading ? (
        <p>Cargando...</p>
      ) : (
        <ul>
          {tareas.map(t => (
            <li key={t.id} style={{ textDecoration: t.completada ? 'line-through' : 'none' }}>
              {t.titulo}
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

```
# frontend/.env.example
VITE_API_URL=http://localhost:3000
```

### Paso 3: Sube el código a GitHub

```bash
git init
git add .
git commit -m "feat: aplicación full-stack lista para deploy"
git branch -M main
git remote add origin https://github.com/tu-usuario/mi-app-fullstack.git
git push -u origin main
```

### Paso 4: Despliega el backend en Railway

1. Ve a [railway.app](https://railway.app) → **New Project**
2. **Deploy from GitHub repo** → Selecciona tu repositorio
3. En **Source Directory** pon: `backend`
4. Railway instala dependencias y ejecuta `npm start` automáticamente
5. Ve a **Settings → Networking** → **Generate Domain**
6. Añade variables de entorno en **Variables**:
   ```
   NODE_ENV=production
   CORS_ORIGIN=https://mi-frontend.vercel.app
   ```
7. Anota la URL del backend: `mi-backend.up.railway.app`

### Paso 5: Despliega el frontend en Vercel

1. Ve a [vercel.com](https://vercel.com) → **New Project**
2. Importa tu repositorio de GitHub
3. En **Root Directory** pon: `frontend`
4. Vercel detecta Vite automáticamente
5. Añade variables de entorno:
   ```
   VITE_API_URL=https://mi-backend.up.railway.app
   ```
6. Haz clic en **Deploy**
7. Anota la URL del frontend: `mi-frontend.vercel.app`

### Paso 6: Actualiza el CORS del backend

```bash
# En Railway, actualiza la variable:
CORS_ORIGIN=https://mi-frontend.vercel.app
```

### Resultado final

```
✅ Frontend:  https://mi-frontend.vercel.app
✅ Backend:   https://mi-backend.up.railway.app
✅ API:       https://mi-backend.up.railway.app/api/tareas
✅ Health:    https://mi-backend.up.railway.app/health
```

---

## 10. Buenas Prácticas

### Seguridad

```bash
# ✅ Usa HTTPS siempre (todas las plataformas lo hacen automáticamente)
# ✅ Nunca subas .env a Git
# ✅ Usa variables de entorno para todos los secretos
# ✅ Configura CORS correctamente (no uses * en producción)
# ✅ Usa Helmet.js para cabeceras de seguridad en Express

npm install helmet
```

```javascript
const helmet = require('helmet');
app.use(helmet());  // Añade cabeceras de seguridad automáticamente
```

### Performance

```javascript
// ✅ Activa compresión en Express
const compression = require('compression');
app.use(compression());

// ✅ Configura caché para assets estáticos
// Vercel y Netlify lo hacen automáticamente para el frontend
```

```bash
# ✅ Analiza el tamaño del bundle de tu frontend
npm run build -- --report  # Vite
npx source-map-explorer 'build/static/js/*.js'  # CRA
```

### Proceso de deploy

```
✅ Siempre haz deploy primero a staging y luego a producción
✅ Automatiza los tests antes del deploy con CI/CD
✅ Mantén el .env.example actualizado para el equipo
✅ Usa semantic versioning para tus releases (v1.0.0, v1.1.0, etc.)
✅ Implementa health checks en tu backend (/health endpoint)
✅ Configura alertas para cuando tu app caiga (UptimeRobot)
```

### Checklist de deploy

```
ANTES del deploy:
□ Los tests pasan localmente (npm test)
□ El build funciona localmente (npm run build)
□ Las variables de entorno están configuradas en la plataforma
□ El .env.example está actualizado
□ El CORS está configurado con la URL correcta del frontend

DESPUÉS del deploy:
□ El /health endpoint responde correctamente
□ La aplicación se conecta a la base de datos
□ El frontend puede comunicarse con el backend
□ Los logs no muestran errores críticos
□ El dominio personalizado funciona (si aplica)
```

---

## 11. Solución de Problemas Comunes

### ❌ "Application error" o la app no inicia

```bash
# Revisa los logs primero
railway logs
heroku logs --tail

# Causas comunes:
# 1. La app no escucha en process.env.PORT
app.listen(process.env.PORT || 3000)  # ✅ Correcto

# 2. Las dependencias no están en "dependencies" (sino en "devDependencies")
# En package.json, asegúrate que express, etc. estén en "dependencies"

# 3. El start script no está definido
# En package.json: "scripts": { "start": "node app.js" }
```

### ❌ Error de CORS en el frontend

```javascript
// En el backend, asegúrate de configurar CORS correctamente
app.use(cors({
  origin: process.env.CORS_ORIGIN,  // URL exacta del frontend
  credentials: true
}));

// ❌ Nunca uses esto en producción:
app.use(cors({ origin: '*' }))
```

### ❌ La app funciona local pero no en producción

```bash
# Diferencias comunes:
# 1. Variables de entorno no configuradas en la plataforma
# 2. NODE_ENV no establecido como "production"
# 3. Puerto hardcodeado en vez de process.env.PORT
# 4. Rutas de archivos case-sensitive (Linux vs Windows)
# 5. Dependencias en devDependencies que se necesitan en producción
```

### ❌ El frontend no conecta con el backend

```javascript
// Verifica que VITE_API_URL está configurado correctamente
console.log('API URL:', import.meta.env.VITE_API_URL);

// Las variables de Vite DEBEN empezar con VITE_ para estar disponibles en el cliente
// ✅ VITE_API_URL
// ❌ API_URL (no funcionará en el cliente)
```

### ❌ Error de base de datos en producción

```bash
# Asegúrate de que DATABASE_URL está configurada
railway variables set DATABASE_URL=postgresql://...

# Si usas Railway, añade primero el servicio de PostgreSQL:
# Railway Dashboard → New → Database → PostgreSQL
# Railway añade DATABASE_URL automáticamente

# Verifica la conexión con un endpoint de diagnóstico
app.get('/health/db', async (req, res) => {
  try {
    await pool.query('SELECT 1');
    res.json({ database: 'connected' });
  } catch (err) {
    res.status(500).json({ database: 'error', message: err.message });
  }
});
```

### ❌ El sitio es lento en producción

```bash
# Frontend:
# - Activa la compresión gzip (Vercel/Netlify lo hacen automáticamente)
# - Analiza el bundle: npx vite-bundle-visualizer
# - Usa lazy loading para componentes grandes
# - Optimiza imágenes

# Backend:
# - Añade caché con Redis para respuestas frecuentes
# - Usa índices en la base de datos
# - Activa la compresión: app.use(require('compression')())
# - Considera un CDN para assets estáticos
```

---

## Resumen: ¿Qué plataforma elegir?

```
¿Quieres empezar rápido y gratis?
  → Frontend: Vercel o Netlify
  → Backend: Render (free tier con spin-down)
  → BD: Railway o Supabase

¿Para proyectos serios/profesionales?
  → Frontend: Vercel o Cloudflare Pages
  → Backend: Railway o Render (plan de pago)
  → BD: Supabase, Railway o Neon (PostgreSQL gestionado)

¿Quieres máximo control?
  → VPS en DigitalOcean, Hetzner o Linode
  → Docker + Nginx + PM2 o Docker Compose

¿Para proyectos empresariales grandes?
  → AWS, Google Cloud o Azure
  → Kubernetes para orquestación de contenedores
```

---

## 📚 Recursos Adicionales

- [Railway Docs](https://docs.railway.app)
- [Render Docs](https://render.com/docs)
- [Vercel Docs](https://vercel.com/docs)
- [Netlify Docs](https://docs.netlify.com)
- [Cloudflare Pages Docs](https://developers.cloudflare.com/pages)
- [DigitalOcean Tutorials](https://www.digitalocean.com/community/tutorials)
- [The Twelve-Factor App](https://12factor.net/es/) - Metodología para apps en la nube
- [OWASP Deployment Security](https://owasp.org/www-project-web-security-testing-guide/)

---

*¿Tienes dudas o quieres añadir más plataformas? Abre un Issue o Pull Request en el repositorio.* 🚀
