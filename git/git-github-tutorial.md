# 🐙 Tutorial Completo de Git y GitHub

## Índice

1. [¿Qué es Git y GitHub?](#1-qué-es-git-y-github)
2. [Instalación y configuración inicial](#2-instalación-y-configuración-inicial)
3. [Conceptos fundamentales](#3-conceptos-fundamentales)
4. [Creando tu primer repositorio](#4-creando-tu-primer-repositorio)
5. [Comandos básicos de Git](#5-comandos-básicos-de-git)
6. [Trabajando con ramas (branches)](#6-trabajando-con-ramas-branches)
7. [Repositorios remotos y GitHub](#7-repositorios-remotos-y-github)
8. [Flujos de trabajo colaborativos](#8-flujos-de-trabajo-colaborativos)
9. [Pull Requests y revisión de código](#9-pull-requests-y-revisión-de-código)
10. [Resolución de conflictos](#10-resolución-de-conflictos)
11. [Git avanzado](#11-git-avanzado)
12. [GitHub Actions (CI/CD básico)](#12-github-actions-cicd-básico)
13. [GitHub Pages](#13-github-pages)
14. [Buenas prácticas](#14-buenas-prácticas)
15. [Comandos de referencia rápida](#15-comandos-de-referencia-rápida)
16. [Proyecto práctico completo](#16-proyecto-práctico-completo)

---

## 1. ¿Qué es Git y GitHub?

### Git

**Git** es un sistema de **control de versiones distribuido** creado por Linus Torvalds en 2005. Permite registrar todos los cambios realizados en los archivos de un proyecto a lo largo del tiempo, facilitando volver a versiones anteriores, comparar cambios y trabajar en equipo sin pisarse el trabajo.

```
Sin Git:                          Con Git:
proyecto_v1.zip                   proyecto/
proyecto_v2.zip                   ├── .git/          ← historial completo
proyecto_v2_final.zip             ├── index.html
proyecto_v2_final_OK.zip          ├── style.css
proyecto_ESTE_SI.zip              └── app.js
😩                                😊
```

### GitHub

**GitHub** es una plataforma web que aloja repositorios Git en la nube. Añade funcionalidades de colaboración como:
- **Pull Requests** (revisión de código)
- **Issues** (gestión de tareas y bugs)
- **GitHub Actions** (automatización y CI/CD)
- **GitHub Pages** (publicación de sitios web)
- **Projects** (tableros Kanban)

### Git vs GitHub

| | Git | GitHub |
|---|---|---|
| **Tipo** | Software (herramienta CLI) | Plataforma web |
| **Uso** | Control de versiones local | Repositorios remotos y colaboración |
| **Requiere internet** | No | Sí |
| **Alternativas** | Mercurial, SVN | GitLab, Bitbucket |

---

## 2. Instalación y configuración inicial

### Instalar Git

**Windows:**
```bash
# Descarga desde https://git-scm.com/download/win
# O usa winget:
winget install Git.Git
```

**macOS:**
```bash
# Con Homebrew:
brew install git

# O instala Xcode Command Line Tools:
xcode-select --install
```

**Linux (Debian/Ubuntu):**
```bash
sudo apt update
sudo apt install git
```

**Verificar instalación:**
```bash
git --version
# git version 2.43.0
```

### Configuración inicial obligatoria

Antes de hacer cualquier commit, debes configurar tu nombre y correo:

```bash
# Configuración global (para todos los proyectos)
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"

# Editor de texto predeterminado (opcional)
git config --global core.editor "code --wait"   # VS Code
git config --global core.editor "nano"           # Nano
git config --global core.editor "vim"            # Vim

# Rama principal por defecto (recomendado)
git config --global init.defaultBranch main

# Colores en la terminal
git config --global color.ui auto
```

**Ver toda tu configuración:**
```bash
git config --list
# user.name=Tu Nombre
# user.email=tu@email.com
# ...
```

### Configurar SSH con GitHub (recomendado)

Usar SSH evita tener que ingresar usuario y contraseña cada vez:

```bash
# 1. Generar clave SSH
ssh-keygen -t ed25519 -C "tu@email.com"
# Presiona Enter para aceptar ubicación por defecto
# Opcionalmente, ingresa una contraseña

# 2. Iniciar el agente SSH
eval "$(ssh-agent -s)"

# 3. Añadir tu clave al agente
ssh-add ~/.ssh/id_ed25519

# 4. Copiar tu clave pública
cat ~/.ssh/id_ed25519.pub
# Copia el resultado completo

# 5. En GitHub: Settings → SSH and GPG keys → New SSH key
#    Pega la clave y guarda

# 6. Verificar conexión
ssh -T git@github.com
# Hi TuUsuario! You've successfully authenticated...
```

---

## 3. Conceptos fundamentales

### El repositorio (repo)

Un **repositorio** es la carpeta de tu proyecto con todo su historial de cambios. Contiene una carpeta oculta `.git/` donde Git guarda toda la información.

```
mi-proyecto/
├── .git/           ← Base de datos de Git (no tocar manualmente)
│   ├── HEAD
│   ├── objects/
│   ├── refs/
│   └── config
├── index.html
├── style.css
└── app.js
```

### Las tres zonas de Git

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   DIRECTORIO DE    →   ÁREA DE STAGE    →   REPOSITORIO    │
│      TRABAJO           (Index/Stage)         (Commits)      │
│   (Working Dir)                                             │
│                                                             │
│   Archivos que         Cambios listos        Historial      │
│   estás editando       para commit           permanente     │
│                                                             │
│   git add ──────────────→                                  │
│                          git commit ──────────→            │
└─────────────────────────────────────────────────────────────┘
```

| Zona | Descripción | Comando relacionado |
|------|-------------|---------------------|
| **Working Directory** | Archivos en tu disco, con cambios no registrados | `git status` |
| **Staging Area** | Cambios marcados para el próximo commit | `git add` |
| **Repository** | Historial de commits guardado | `git commit` |

### El commit

Un **commit** es una instantánea (snapshot) del proyecto en un momento dado. Cada commit tiene:
- Un **SHA** único (identificador de 40 caracteres)
- **Autor** y **fecha**
- **Mensaje** descriptivo
- Referencia al commit anterior (padre)

```
commit a3f8c9d (HEAD -> main)
Author: María García <maria@example.com>
Date:   Wed Mar 5 10:30:00 2025

    Añade formulario de contacto

    - Campos nombre, email y mensaje
    - Validación básica con JavaScript
    - Estilos responsive
```

### Ramas (branches)

Una **rama** es una línea de desarrollo independiente. La rama principal suele llamarse `main` (o `master` en proyectos antiguos).

```
main:    A ── B ── C ── D ── E
                    \
feature:             F ── G ── H
```

---

## 4. Creando tu primer repositorio

### Opción A: Iniciar un repositorio local nuevo

```bash
# Crear carpeta y entrar en ella
mkdir mi-primer-repo
cd mi-primer-repo

# Inicializar Git
git init
# Initialized empty Git repository in .../mi-primer-repo/.git/

# Verificar estado
git status
# On branch main
# No commits yet
# nothing to commit
```

### Opción B: Clonar un repositorio existente de GitHub

```bash
# Clonar con HTTPS
git clone https://github.com/usuario/repositorio.git

# Clonar con SSH (recomendado si configuraste SSH)
git clone git@github.com:usuario/repositorio.git

# Clonar en una carpeta con nombre específico
git clone https://github.com/usuario/repositorio.git mi-carpeta

# Entrar en la carpeta clonada
cd repositorio
```

### Tu primer commit paso a paso

```bash
# 1. Crea un archivo
echo "# Mi Proyecto" > README.md

# 2. Verifica el estado
git status
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         README.md

# 3. Añade al staging
git add README.md

# 4. Verifica de nuevo
git status
# Changes to be committed:
#   (use "git restore --staged <file>..." to unstage)
#         new file:   README.md

# 5. Haz el commit
git commit -m "Primer commit: añade README"
# [main (root-commit) a1b2c3d] Primer commit: añade README
#  1 file changed, 1 insertion(+)
#  create mode 100644 README.md

# 6. Ver el historial
git log
# commit a1b2c3d4e5f6...
# Author: Tu Nombre <tu@email.com>
# Date:   ...
#
#     Primer commit: añade README
```

---

## 5. Comandos básicos de Git

### `git status` — Ver el estado actual

```bash
git status

# Ejemplo de salida:
# On branch main
# Changes to be committed:        ← en staging
#   modified:   index.html
#
# Changes not staged for commit:  ← modificado pero no en staging
#   modified:   style.css
#
# Untracked files:                ← nuevos, Git no los conoce
#   app.js
```

### `git add` — Añadir al staging

```bash
# Añadir un archivo específico
git add index.html

# Añadir varios archivos
git add index.html style.css app.js

# Añadir todos los archivos del directorio actual
git add .

# Añadir todos los archivos del proyecto (incluyendo subdirectorios)
git add -A

# Añadir partes de un archivo (interactivo)
git add -p index.html
```

### `git commit` — Guardar los cambios

```bash
# Commit con mensaje corto
git commit -m "Añade estilos de navegación"

# Commit con mensaje largo (abre el editor)
git commit

# Añadir al staging y hacer commit en un solo paso (solo archivos rastreados)
git commit -am "Corrige bug en formulario"

# Modificar el último commit (solo si no se ha publicado)
git commit --amend -m "Mensaje corregido"
```

### `git log` — Ver el historial

```bash
# Historial completo
git log

# Historial resumido (una línea por commit)
git log --oneline

# Historial con grafo de ramas
git log --oneline --graph --all

# Historial de un archivo específico
git log --oneline archivo.txt

# Buscar commits por mensaje
git log --grep="formulario"

# Commits de un autor específico
git log --author="María"

# Últimos N commits
git log -5

# Ejemplo de salida de git log --oneline --graph --all:
# * f3a1b2c (HEAD -> main) Añade sección de contacto
# * d4e5f6a Mejora estilos del header
# | * 8b9c0d1 (feature/login) Implementa formulario de login
# |/
# * 2a3b4c5 Commit inicial
```

### `git diff` — Ver diferencias

```bash
# Diferencias entre working directory y staging
git diff

# Diferencias entre staging y último commit
git diff --staged

# Diferencias entre dos commits
git diff a1b2c3d f3e4d5c

# Diferencias entre dos ramas
git diff main feature/login

# Diferencias de un archivo específico
git diff index.html
```

### `git restore` — Deshacer cambios

```bash
# Descartar cambios en working directory (no recuperable)
git restore index.html

# Sacar un archivo del staging (mantiene los cambios)
git restore --staged index.html

# Restaurar un archivo a una versión anterior
git restore --source=HEAD~2 index.html
```

### `git rm` — Eliminar archivos

```bash
# Eliminar archivo del repo y del disco
git rm archivo.txt

# Eliminar del repo pero mantener en disco (útil para añadir a .gitignore)
git rm --cached archivo.txt

# Eliminar carpeta recursivamente
git rm -r carpeta/
```

### `git mv` — Mover o renombrar archivos

```bash
# Renombrar
git mv viejo-nombre.txt nuevo-nombre.txt

# Mover
git mv archivo.txt nueva-carpeta/archivo.txt
```

### `.gitignore` — Ignorar archivos

Crea un archivo `.gitignore` en la raíz del proyecto:

```bash
# .gitignore

# Dependencias de Node.js
node_modules/

# Archivos de entorno (¡nunca subir contraseñas!)
.env
.env.local
.env.production

# Archivos compilados
dist/
build/
*.class
*.pyc
__pycache__/

# Logs
*.log
logs/

# Archivos del sistema operativo
.DS_Store          # macOS
Thumbs.db          # Windows
desktop.ini        # Windows

# Archivos del IDE
.vscode/
.idea/
*.swp              # Vim

# Archivos de prueba/cobertura
coverage/
.nyc_output/
```

```bash
# Ver qué archivos están ignorados
git status --ignored

# Forzar añadir un archivo ignorado (¡usar con cuidado!)
git add -f archivo-ignorado.txt
```

---

## 6. Trabajando con ramas (branches)

### Crear y cambiar de rama

```bash
# Listar todas las ramas
git branch

# Listar ramas remotas también
git branch -a

# Crear una nueva rama
git branch feature/nueva-funcionalidad

# Cambiar a una rama existente
git checkout feature/nueva-funcionalidad

# Crear y cambiar en un solo paso (forma moderna - Git 2.23+)
git switch -c feature/nueva-funcionalidad

# Cambiar a una rama existente (forma moderna)
git switch main
```

### Flujo básico de trabajo con ramas

```bash
# 1. Asegúrate de estar en main actualizado
git switch main
git pull

# 2. Crea una rama para la nueva funcionalidad
git switch -c feature/login

# 3. Trabaja en tu funcionalidad
echo "<form>...</form>" > login.html
git add login.html
git commit -m "Añade estructura del formulario de login"

# 4. Más cambios...
echo "/* login styles */" >> style.css
git add style.css
git commit -m "Añade estilos del formulario de login"

# 5. Ver el historial de la rama
git log --oneline

# 6. Fusionar de vuelta en main
git switch main
git merge feature/login

# 7. Eliminar la rama (opcional, ya fusionada)
git branch -d feature/login
```

### Tipos de merge (fusión)

**Fast-forward merge** (sin commits adicionales, historial lineal):
```bash
# Si main no tiene commits nuevos desde que se creó la rama:
git merge feature/simple
# Fast-forward
# index.html | 10 ++++++++++
# 1 file changed, 10 insertions(+)
```

**Merge commit** (crea un commit de fusión):
```bash
# Forzar siempre un commit de merge
git merge --no-ff feature/compleja
# Se abre el editor para el mensaje del commit de merge
```

**Squash merge** (aplana todos los commits de la rama en uno):
```bash
git merge --squash feature/experimento
git commit -m "Añade funcionalidad de experimento"
```

### `git stash` — Guardar cambios temporalmente

Útil cuando necesitas cambiar de rama con cambios sin terminar:

```bash
# Guardar cambios actuales en el stash
git stash

# Ver lista de stashes guardados
git stash list
# stash@{0}: WIP on feature/login: a1b2c3 Último commit
# stash@{1}: WIP on main: d4e5f6 Otro commit

# Recuperar el último stash (y eliminarlo del stash)
git stash pop

# Recuperar sin eliminar del stash
git stash apply stash@{0}

# Eliminar un stash
git stash drop stash@{0}

# Limpiar todos los stashes
git stash clear

# Guardar con mensaje descriptivo
git stash push -m "Trabajo en progreso: formulario de login"
```

---

## 7. Repositorios remotos y GitHub

### Crear un repositorio en GitHub

1. Ve a [github.com](https://github.com) y haz login
2. Clic en **"+"** → **"New repository"**
3. Completa:
   - **Repository name**: `mi-proyecto`
   - **Description**: descripción opcional
   - **Visibility**: Public o Private
   - **NO** inicialices con README si ya tienes un repo local
4. Clic en **"Create repository"**

### Conectar repo local con GitHub

```bash
# Añadir el repositorio remoto
git remote add origin git@github.com:tu-usuario/mi-proyecto.git

# Verificar remotos configurados
git remote -v
# origin  git@github.com:tu-usuario/mi-proyecto.git (fetch)
# origin  git@github.com:tu-usuario/mi-proyecto.git (push)

# Primer push: enviar rama main y configurar upstream
git push -u origin main

# Pushes posteriores (ya configurado el upstream)
git push
```

### `git push` — Enviar cambios al remoto

```bash
# Push de la rama actual (si ya configuró upstream)
git push

# Push de una rama específica
git push origin feature/login

# Push forzado (¡peligroso! reescribe historial remoto)
git push --force-with-lease origin feature/login

# Subir todas las ramas locales
git push --all origin

# Subir tags
git push --tags
```

### `git pull` — Obtener y fusionar cambios del remoto

```bash
# Pull de la rama actual
git pull

# Pull de una rama específica
git pull origin main

# Pull con rebase en vez de merge (historial más limpio)
git pull --rebase origin main
```

### `git fetch` — Obtener cambios sin fusionar

```bash
# Descargar cambios del remoto (sin modificar tu código)
git fetch origin

# Fetch de todos los remotos
git fetch --all

# Ver qué cambios llegaron
git log HEAD..origin/main --oneline

# Ahora puedes decidir cuándo fusionar
git merge origin/main
```

### Diferencia entre `fetch` y `pull`

```
git fetch   = descarga cambios remotos → tu rama local NO cambia
git pull    = git fetch + git merge    → tu rama local SÍ cambia
```

### Gestionar remotos

```bash
# Listar remotos
git remote -v

# Añadir un remoto adicional (ej: el fork original)
git remote add upstream git@github.com:proyecto-original/repo.git

# Cambiar URL de un remoto
git remote set-url origin git@github.com:tu-usuario/nuevo-nombre.git

# Eliminar un remoto
git remote remove origin

# Renombrar un remoto
git remote rename origin nuevo-nombre
```

---

## 8. Flujos de trabajo colaborativos

### Flujo GitHub Flow (recomendado para la mayoría de proyectos)

```
1. main siempre está listo para producción
2. Crear rama desde main para cada feature/fix
3. Hacer commits en la rama
4. Abrir Pull Request
5. Revisión de código
6. Merge a main
7. Desplegar
```

```bash
# Flujo completo con GitHub Flow:

# 1. Actualizar main
git switch main
git pull origin main

# 2. Crear rama de feature
git switch -c feature/carrito-compras

# 3. Desarrollar con commits frecuentes
git add src/cart.js
git commit -m "feat: añade clase Cart con métodos básicos"

git add src/cart.js tests/cart.test.js
git commit -m "test: añade tests unitarios para Cart"

git add styles/cart.css
git commit -m "style: añade estilos del carrito"

# 4. Subir la rama a GitHub
git push -u origin feature/carrito-compras

# 5. Abrir Pull Request en GitHub (desde la web)

# 6. Tras la aprobación, merge desde GitHub

# 7. Actualizar local y limpiar
git switch main
git pull origin main
git branch -d feature/carrito-compras
git remote prune origin  # Limpia referencias a ramas remotas eliminadas
```

### Flujo Git Flow (para proyectos con releases formales)

```
main         ──●──────────────────────●──── (producción)
                \                    /
develop     ────●──●──●──●──●──●──●──●──── (integración)
                  \          /   \
feature          ──●──●──●──     feature2
```

Ramas en Git Flow:
- **main**: código en producción
- **develop**: integración de features
- **feature/\***: nuevas funcionalidades
- **release/\***: preparación de releases
- **hotfix/\***: correcciones urgentes en producción

### Trabajar con el repositorio de otro (Fork)

```bash
# 1. En GitHub: clic en "Fork" en el repositorio original

# 2. Clonar TU fork
git clone git@github.com:tu-usuario/proyecto-original.git
cd proyecto-original

# 3. Añadir el repositorio original como "upstream"
git remote add upstream git@github.com:autor-original/proyecto-original.git

# 4. Verifica los remotos
git remote -v
# origin    git@github.com:tu-usuario/proyecto-original.git (push/fetch)
# upstream  git@github.com:autor-original/proyecto-original.git (push/fetch)

# 5. Mantener tu fork actualizado
git fetch upstream
git switch main
git merge upstream/main
git push origin main

# 6. Crear rama para tu contribución
git switch -c fix/corrige-typo-readme

# 7. Hacer cambios, commit y push a tu fork
git commit -am "fix: corrige typo en README"
git push origin fix/corrige-typo-readme

# 8. En GitHub: abrir Pull Request desde tu fork al repositorio original
```

---

## 9. Pull Requests y revisión de código

### ¿Qué es un Pull Request (PR)?

Un Pull Request es una solicitud para incorporar los cambios de una rama a otra. Permite:
- Revisar el código antes de fusionarlo
- Discutir cambios con comentarios
- Ejecutar pruebas automáticas (CI)
- Mantener un historial de decisiones

### Crear un Pull Request en GitHub

1. Ve a tu repositorio en GitHub
2. Aparecerá un banner amarillo: **"Compare & pull request"**, o ve a **Pull requests → New pull request**
3. Selecciona:
   - **base**: la rama destino (generalmente `main`)
   - **compare**: tu rama con los cambios
4. Rellena:
   - **Título**: descriptivo y conciso
   - **Descripción**: qué cambios incluye, por qué, cómo probar
5. Asigna **Reviewers**, **Labels**, **Milestone** si procede
6. Clic en **"Create pull request"**

### Plantilla de descripción de PR

```markdown
## 📋 Descripción
Breve descripción de qué hace este PR.

## 🔗 Issue relacionado
Closes #123

## 🔄 Tipo de cambio
- [ ] Bug fix
- [x] Nueva funcionalidad
- [ ] Refactor
- [ ] Documentación

## 🧪 Cómo probar
1. Ejecuta `npm install`
2. Ve a `/carrito`
3. Añade un producto
4. Verifica que el total se actualiza

## ✅ Checklist
- [x] El código sigue las convenciones del proyecto
- [x] He añadido tests
- [x] Los tests pasan
- [x] He actualizado la documentación
```

### Revisar un Pull Request

```bash
# Descargar la rama del PR para probarla localmente
git fetch origin pull/42/head:pr-42
git switch pr-42

# Revisar los cambios
git diff main...pr-42

# Volver a tu rama
git switch main
```

En GitHub puedes:
- **Comentar** líneas específicas de código
- **Solicitar cambios** (Request changes)
- **Aprobar** (Approve)
- Ver el historial de revisiones

---

## 10. Resolución de conflictos

Los conflictos ocurren cuando dos ramas modifican las mismas líneas de un archivo.

### Ejemplo de conflicto

```bash
# En main, alguien cambió la línea 5 de index.html a:
# <title>Mi Aplicación</title>

# En tu rama, tú cambiaste la misma línea a:
# <title>Mi App Increíble</title>

# Al intentar hacer merge:
git merge main
# Auto-merging index.html
# CONFLICT (content): Merge conflict in index.html
# Automatic merge failed; fix conflicts and then commit the result.
```

### Cómo se ve un conflicto en el archivo

```html
<!DOCTYPE html>
<html>
<head>
<<<<<<< HEAD
    <title>Mi App Increíble</title>
=======
    <title>Mi Aplicación</title>
>>>>>>> main
</head>
<body>
```

**Leyendo el conflicto:**
- `<<<<<<< HEAD`: inicio de TUS cambios
- `=======`: separador
- `>>>>>>> main`: inicio de los cambios de la otra rama

### Resolver el conflicto

```bash
# 1. Ver todos los archivos con conflictos
git status
# both modified: index.html

# 2. Abrir el archivo y editar manualmente
# Elimina los marcadores y decide qué conservar:
```

```html
<!-- Resultado final (elegimos combinar ambos): -->
<!DOCTYPE html>
<html>
<head>
    <title>Mi App Increíble</title>
</head>
<body>
```

```bash
# 3. Marcar como resuelto
git add index.html

# 4. Completar el merge
git commit
# Se abre el editor con un mensaje predeterminado de merge

# O si quieres abortar el merge
git merge --abort
```

### Herramientas para resolver conflictos

```bash
# Usar la herramienta configurada
git mergetool

# Configurar VS Code como herramienta de merge
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

### Estrategias para evitar conflictos

```bash
# 1. Hacer pull frecuentemente
git pull --rebase origin main

# 2. Trabajar en archivos diferentes cuando sea posible

# 3. Hacer commits pequeños y frecuentes

# 4. Comunicarse con el equipo sobre qué archivos están modificando
```

---

## 11. Git avanzado

### `git rebase` — Reescribir el historial

Rebase mueve o "replanta" commits sobre otra base:

```bash
# Situación inicial:
# main:    A ── B ── C
# feature:      └── D ── E

# Con merge:
# main:    A ── B ── C ── M  (M = commit de merge)
#                    └──/
# feature:      D ── E

# Con rebase (historial más limpio):
# main:    A ── B ── C ── D' ── E'
# (los commits D y E se "replantaron" encima de C)

# Hacer rebase de tu rama sobre main
git switch feature/mi-rama
git rebase main

# Rebase interactivo (reescribir últimos 3 commits)
git rebase -i HEAD~3
```

**Rebase interactivo** (muy útil para limpiar historial):

```bash
git rebase -i HEAD~3
# Se abre el editor con algo así:
# pick a1b2c3d Añade estructura
# pick d4e5f6a Añade estilos
# pick g7h8i9j Corrige typo

# Puedes cambiar "pick" por:
# r (reword): cambiar el mensaje del commit
# e (edit): pausar para editar el commit
# s (squash): unir con el commit anterior
# f (fixup): unir con el anterior (descarta el mensaje)
# d (drop): eliminar el commit
```

> ⚠️ **Nunca hagas rebase de commits ya publicados** en una rama compartida

### `git cherry-pick` — Aplicar commits específicos

```bash
# Copiar un commit específico a la rama actual
git cherry-pick a1b2c3d

# Cherry-pick de un rango de commits
git cherry-pick a1b2c3d..f3e4d5c

# Cherry-pick sin hacer commit automáticamente
git cherry-pick --no-commit a1b2c3d
```

### `git tag` — Etiquetar versiones

```bash
# Crear tag ligero (solo un puntero)
git tag v1.0.0

# Crear tag anotado (con metadatos, recomendado)
git tag -a v1.0.0 -m "Versión 1.0.0 - Primera release estable"

# Listar tags
git tag
git tag -l "v1.*"

# Ver detalles de un tag
git show v1.0.0

# Subir tags a GitHub
git push origin v1.0.0
git push --tags  # Subir todos los tags

# Eliminar tag local
git tag -d v1.0.0-beta

# Eliminar tag remoto
git push origin --delete v1.0.0-beta
```

### `git reset` — Mover HEAD y deshacer commits

```bash
# --soft: deshace el commit, mantiene cambios en staging
git reset --soft HEAD~1

# --mixed (por defecto): deshace el commit, mantiene cambios en working dir
git reset HEAD~1
git reset --mixed HEAD~1

# --hard: deshace el commit y DESCARTA todos los cambios (¡irreversible!)
git reset --hard HEAD~1

# Volver a un commit específico
git reset --hard a1b2c3d
```

### `git revert` — Deshacer commits de forma segura

Crea un **nuevo commit** que deshace los cambios (no reescribe historial):

```bash
# Revertir el último commit
git revert HEAD

# Revertir un commit específico
git revert a1b2c3d

# Revertir sin abrir el editor
git revert --no-edit HEAD

# Revertir un rango de commits
git revert HEAD~3..HEAD
```

> 💡 Usa `git revert` en ramas públicas y `git reset` solo en ramas locales

### `git bisect` — Encontrar el commit que introdujo un bug

```bash
# Iniciar bisect
git bisect start

# Marcar el commit actual como "malo" (tiene el bug)
git bisect bad

# Marcar un commit anterior como "bueno" (no tenía el bug)
git bisect good v1.0.0

# Git te llevará a commits intermedios
# Prueba si el bug existe y marca:
git bisect good  # o
git bisect bad

# Cuando Git encuentre el commit problemático:
# a1b2c3d is the first bad commit

# Terminar bisect
git bisect reset
```

### `git reflog` — El historial de todos los movimientos

```bash
# Ver todos los movimientos de HEAD (¡salvavidas!)
git reflog

# Ejemplo de salida:
# a1b2c3d HEAD@{0}: commit: Añade feature X
# d4e5f6a HEAD@{1}: checkout: moving from feature to main
# g7h8i9j HEAD@{2}: reset: moving to HEAD~1

# Recuperar un commit "perdido" después de un reset
git checkout g7h8i9j  # o
git reset --hard g7h8i9j
```

---

## 12. GitHub Actions (CI/CD básico)

GitHub Actions permite automatizar flujos de trabajo directamente en GitHub.

### Estructura de un workflow

```
.github/
└── workflows/
    ├── ci.yml          ← Tests automáticos en cada push
    ├── deploy.yml      ← Despliegue automático
    └── release.yml     ← Publicar nueva versión
```

### Ejemplo: Tests automáticos en Node.js

```yaml
# .github/workflows/ci.yml
name: CI - Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x, 20.x]

    steps:
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Usar Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - name: Instalar dependencias
        run: npm ci

      - name: Ejecutar linter
        run: npm run lint

      - name: Ejecutar tests
        run: npm test

      - name: Construir proyecto
        run: npm run build
```

### Ejemplo: Despliegue a GitHub Pages

```yaml
# .github/workflows/deploy.yml
name: Deploy a GitHub Pages

on:
  push:
    branches: [ main ]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Instalar dependencias
        run: npm ci

      - name: Construir
        run: npm run build

      - name: Subir artefacto
        uses: actions/upload-pages-artifact@v3
        with:
          path: dist/

      - name: Desplegar a GitHub Pages
        uses: actions/deploy-pages@v4
```

### Variables y secretos en GitHub Actions

```yaml
# Usar un secreto (configurado en Settings → Secrets)
steps:
  - name: Desplegar
    env:
      API_KEY: ${{ secrets.API_KEY }}
      DB_URL: ${{ secrets.DATABASE_URL }}
    run: ./deploy.sh
```

---

## 13. GitHub Pages

GitHub Pages permite publicar sitios web estáticos gratis directamente desde tu repositorio.

### Publicar desde la rama main

1. Ve a tu repositorio en GitHub
2. **Settings → Pages**
3. En **Source**, selecciona **Deploy from a branch**
4. Selecciona **main** y la carpeta **/(root)** o **/docs**
5. Clic en **Save**
6. Tu sitio estará en: `https://tu-usuario.github.io/tu-repositorio/`

### Ejemplo: Crear un sitio personal

```bash
# Crear repositorio con nombre especial para el sitio principal
# El repositorio DEBE llamarse: tu-usuario.github.io

git clone git@github.com:tu-usuario/tu-usuario.github.io.git
cd tu-usuario.github.io

# Crear página principal
cat > index.html << 'EOF'
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Portfolio</title>
    <style>
        body { font-family: sans-serif; max-width: 800px; margin: 0 auto; padding: 2rem; }
        h1 { color: #333; }
    </style>
</head>
<body>
    <h1>¡Hola! Soy Tu Nombre</h1>
    <p>Desarrollador/a web apasionado/a por el código.</p>
</body>
</html>
EOF

git add index.html
git commit -m "Añade página principal del portfolio"
git push origin main
# Sitio disponible en: https://tu-usuario.github.io
```

---

## 14. Buenas prácticas

### Mensajes de commit: Conventional Commits

El estándar **Conventional Commits** hace los mensajes descriptivos y permite automatizar changelogs:

```
<tipo>(<ámbito opcional>): <descripción>

[cuerpo opcional]

[pie de página opcional]
```

**Tipos:**
| Tipo | Uso |
|------|-----|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de bug |
| `docs` | Cambios en documentación |
| `style` | Formato, espacios (sin cambios de lógica) |
| `refactor` | Refactorización (sin nuevas features ni bugs) |
| `test` | Añadir o corregir tests |
| `chore` | Tareas de mantenimiento (dependencias, config) |
| `perf` | Mejoras de rendimiento |
| `ci` | Cambios en CI/CD |

**Ejemplos:**
```
feat(auth): añade login con Google OAuth

fix(cart): corrige cálculo incorrecto del total con descuentos

docs: actualiza README con instrucciones de instalación

feat!: cambia API de autenticación (BREAKING CHANGE)
```

### Estrategia de ramas

```bash
# Nombres descriptivos con prefijos:
git switch -c feature/sistema-de-pagos
git switch -c fix/error-login-gmail
git switch -c docs/actualiza-readme
git switch -c refactor/simplifica-autenticacion
git switch -c hotfix/seguridad-xss
git switch -c release/v2.1.0

# Ramas cortas: una tarea, un PR, eliminar tras merge
# Nunca trabajar directamente en main o develop
```

### Commits pequeños y frecuentes

```bash
# ❌ Un solo commit enorme
git commit -m "Implementa todo el sistema de usuarios"
# (contiene 50 archivos cambiados)

# ✅ Commits pequeños y descriptivos
git commit -m "feat(users): añade modelo User con validaciones"
git commit -m "feat(users): añade endpoint POST /users"
git commit -m "feat(users): añade endpoint GET /users/:id"
git commit -m "test(users): añade tests para CRUD de usuarios"
git commit -m "docs(users): documenta API de usuarios en README"
```

### Configuración del repositorio en GitHub

**Proteger la rama main:**
1. **Settings → Branches → Add rule**
2. Branch name pattern: `main`
3. Activa:
   - ✅ Require a pull request before merging
   - ✅ Require approvals (mínimo 1)
   - ✅ Require status checks to pass (CI)
   - ✅ Include administrators

**Añadir un README completo:**

```markdown
# Nombre del Proyecto

Descripción breve del proyecto.

## 🚀 Instalación

```bash
git clone https://github.com/usuario/proyecto.git
cd proyecto
npm install
```

## 💻 Uso

```bash
npm start
```

## 🧪 Tests

```bash
npm test
```

## 🤝 Contribuir

Ver [CONTRIBUTING.md](CONTRIBUTING.md).

## 📄 Licencia

MIT - ver [LICENSE](LICENSE).
```

---

## 15. Comandos de referencia rápida

### Tabla de comandos esenciales

| Comando | Descripción |
|---------|-------------|
| `git init` | Inicializar repositorio |
| `git clone <url>` | Clonar repositorio |
| `git status` | Ver estado del repo |
| `git add <archivo>` | Añadir al staging |
| `git add .` | Añadir todo al staging |
| `git commit -m "msg"` | Hacer commit |
| `git log --oneline` | Ver historial resumido |
| `git diff` | Ver cambios no en staging |
| `git diff --staged` | Ver cambios en staging |
| `git branch` | Listar ramas |
| `git switch -c <rama>` | Crear y cambiar de rama |
| `git switch <rama>` | Cambiar de rama |
| `git merge <rama>` | Fusionar rama |
| `git push` | Enviar cambios al remoto |
| `git pull` | Obtener y fusionar del remoto |
| `git fetch` | Obtener sin fusionar |
| `git stash` | Guardar cambios temporalmente |
| `git stash pop` | Recuperar cambios del stash |
| `git tag -a v1.0 -m "..."` | Crear tag anotado |
| `git revert HEAD` | Revertir último commit (seguro) |
| `git reset --soft HEAD~1` | Deshacer commit (mantener cambios) |
| `git cherry-pick <sha>` | Copiar commit a rama actual |
| `git rebase -i HEAD~3` | Rebase interactivo |
| `git bisect start` | Iniciar búsqueda de bug |
| `git reflog` | Ver historial de movimientos |

### Alias útiles

```bash
# Configurar alias para comandos frecuentes
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.sw switch
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.unstage "restore --staged"

# Uso:
git st        # = git status
git lg        # = git log --oneline --graph --all --decorate
git undo      # = git reset --soft HEAD~1
```

---

## 16. Proyecto práctico completo

Vamos a construir un pequeño proyecto de blog estático y gestionarlo completamente con Git y GitHub.

### Paso 1: Crear el repositorio en GitHub y clonar

```bash
# En GitHub: crea repositorio "mi-blog-git"
# Luego:
git clone git@github.com:tu-usuario/mi-blog-git.git
cd mi-blog-git
```

### Paso 2: Estructura inicial del proyecto

```bash
# Crear estructura de archivos
mkdir -p css js posts

cat > index.html << 'EOF'
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Mi Blog</title>
    <link rel="stylesheet" href="css/styles.css">
</head>
<body>
    <header>
        <h1>Mi Blog Técnico</h1>
        <nav>
            <a href="index.html">Inicio</a>
        </nav>
    </header>
    <main>
        <article>
            <h2>Bienvenido a mi blog</h2>
            <p>Aquí compartiré tutoriales de programación.</p>
        </article>
    </main>
</body>
</html>
EOF

cat > css/styles.css << 'EOF'
* { box-sizing: border-box; margin: 0; padding: 0; }
body { font-family: sans-serif; max-width: 800px; margin: 0 auto; padding: 2rem; }
header { background: #333; color: white; padding: 1rem; margin-bottom: 2rem; }
header a { color: white; text-decoration: none; margin-right: 1rem; }
article { padding: 1rem; border: 1px solid #ddd; border-radius: 4px; }
EOF

# Crear .gitignore
cat > .gitignore << 'EOF'
.DS_Store
*.log
node_modules/
EOF

# Primer commit
git add .
git commit -m "feat: estructura inicial del blog"
git push origin main
```

### Paso 3: Trabajar con ramas - Añadir una nueva entrada

```bash
# Crear rama para el nuevo post
git switch -c feature/post-git-tutorial

# Crear el post
cat > posts/intro-git.html << 'EOF'
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Introducción a Git - Mi Blog</title>
    <link rel="stylesheet" href="../css/styles.css">
</head>
<body>
    <header>
        <h1>Mi Blog Técnico</h1>
        <nav><a href="../index.html">Inicio</a></nav>
    </header>
    <main>
        <article>
            <h2>Introducción a Git</h2>
            <p>Git es el sistema de control de versiones más usado del mundo...</p>
        </article>
    </main>
</body>
</html>
EOF

git add posts/intro-git.html
git commit -m "feat(posts): añade post sobre introducción a Git"

# Actualizar el índice con el enlace
# (editar index.html para añadir el enlace al post)
git add index.html
git commit -m "feat: añade enlace al post de Git en el índice"

# Subir la rama
git push -u origin feature/post-git-tutorial
```

### Paso 4: Simular colaboración - Arreglar un bug en main

```bash
# Mientras trabajas en feature, alguien reporta un bug en main
# Guarda tu trabajo actual
git stash push -m "WIP: añadiendo post de Git"

# Vuelve a main y crea rama para el fix
git switch main
git pull origin main
git switch -c fix/typo-bienvenida

# Corrige el error
sed -i 's/compartiré/compartiré aquí/' index.html
git add index.html
git commit -m "fix: corrige texto de bienvenida"
git push -u origin fix/typo-bienvenida

# Merge rápido del fix a main (en un equipo real sería via PR)
git switch main
git merge --no-ff fix/typo-bienvenida -m "fix: merge typo-bienvenida"
git push origin main
git branch -d fix/typo-bienvenida

# Vuelve a tu feature y recupera el stash
git switch feature/post-git-tutorial
git stash pop
```

### Paso 5: Actualizar la rama feature con los cambios de main

```bash
# Rebasar sobre main para tener los últimos cambios
git rebase main

# Si hay conflictos, resuélvelos y continúa:
# git add archivo-conflictivo
# git rebase --continue

# Forzar push (porque rebase reescribió el historial)
git push --force-with-lease origin feature/post-git-tutorial
```

### Paso 6: Crear el Pull Request y hacer merge

```bash
# En GitHub: crea el Pull Request de feature/post-git-tutorial → main
# Revisa los cambios, comenta si es necesario

# Tras aprobación y merge en GitHub:
git switch main
git pull origin main
git branch -d feature/post-git-tutorial
git remote prune origin
```

### Paso 7: Etiquetar la primera versión

```bash
# Ver el historial
git log --oneline --graph --all

# Crear tag de versión
git tag -a v1.0.0 -m "Primera versión del blog con post inicial"
git push origin v1.0.0

# En GitHub: crea una Release a partir del tag
# Releases → Create a new release → Selecciona v1.0.0
```

### Paso 8: Configurar GitHub Actions para validar HTML

```bash
mkdir -p .github/workflows

cat > .github/workflows/validate.yml << 'EOF'
name: Validar HTML

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Verificar archivos HTML existen
        run: |
          echo "Archivos HTML encontrados:"
          find . -name "*.html" -not -path "./.git/*"
          echo "Validación completada ✅"
EOF

git add .github/workflows/validate.yml
git commit -m "ci: añade workflow de validación básica"
git push origin main
```

### Resultado final: historial del proyecto

```bash
git log --oneline --graph --all
# * f8a9b0c (HEAD -> main, tag: v1.0.0, origin/main) ci: añade workflow de validación
# *   d3e4f5a fix: merge typo-bienvenida
# |\
# | * b6c7d8e fix: corrige texto de bienvenida
# |/
# * a1b2c3d feat: añade enlace al post de Git en el índice
# * 9e0f1a2 feat(posts): añade post sobre introducción a Git
# * 7c8d9e0 feat: estructura inicial del blog
```

---

## 🔗 Recursos adicionales

### Documentación oficial
- [Documentación oficial de Git](https://git-scm.com/doc)
- [Pro Git (libro gratuito)](https://git-scm.com/book/es/v2)
- [GitHub Docs](https://docs.github.com)

### Práctica interactiva
- [Learn Git Branching](https://learngitbranching.js.org/?locale=es_ES) — Simulador visual de ramas
- [GitHub Skills](https://skills.github.com/) — Cursos interactivos oficiales de GitHub
- [Oh My Git!](https://ohmygit.org/) — Juego para aprender Git

### Herramientas recomendadas
- **[GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)** — Extensión VS Code
- **[GitHub Desktop](https://desktop.github.com/)** — Cliente gráfico oficial
- **[SourceTree](https://www.sourcetreeapp.com/)** — Cliente gráfico gratuito
- **[git-flow](https://github.com/nvie/gitflow)** — Herramienta para Git Flow

---

⭐ **¡Si este tutorial te fue útil, dale una estrella al repositorio!**
