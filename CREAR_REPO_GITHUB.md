# 🚀 Cómo crear el repositorio `mysql-local-2.4` en GitHub
### Guía paso a paso para principiantes

> **¿Qué vas a lograr?**
> Publicar el proyecto `mysql-local-2.4` en un repositorio nuevo de GitHub,
> configurar los secrets necesarios para el pipeline y ejecutar el CI completo
> con SonarCloud y Snyk por primera vez.

---

## 🧠 Antes de empezar — ¿Qué es cada cosa?

| Concepto | Qué es en simple |
|---|---|
| **Git** | Programa en tu computador que guarda el historial de cambios de tu código |
| **GitHub** | Sitio web donde guardas tu código en la nube (como Google Drive pero para código) |
| **Repositorio (repo)** | La carpeta de tu proyecto guardada en GitHub |
| **Commit** | Un "guardado" con mensaje que registra los cambios en Git |
| **Push** | Enviar tus commits locales a GitHub |
| **Remote** | La dirección del repositorio en GitHub (como la URL de tu carpeta en la nube) |
| **Secret** | Variable cifrada en GitHub que el pipeline usa sin exponer su valor en el código |
| **`gh` CLI** | Herramienta de línea de comandos para controlar GitHub desde la terminal |

---

## ✅ Requisitos previos

Antes de ejecutar cualquier comando, verifica que tienes todo instalado:

```bash
# 1. Git instalado
git --version
# Debe mostrar: git version 2.x.x

# 2. GitHub CLI instalado y autenticado
gh --version
# Debe mostrar: gh version 2.x.x

gh auth status
# Debe mostrar: ✓ Logged in to github.com account TU-USUARIO

# 3. Maven instalado (para verificar que el proyecto compila)
mvn -version
# Debe mostrar: Apache Maven 3.x.x
```

> ⚠️ Si `gh auth status` muestra que no estás autenticado, ejecuta:
> ```bash
> gh auth login
> ```
> Sigue las instrucciones: elige GitHub.com → HTTPS → autenticar con navegador.

---

## 📁 Paso 1 — Abre la terminal en la carpeta correcta

Toda la actividad se hace dentro de la carpeta `mysql-local-2.4`.
Abre tu terminal y navega hasta ella:

```bash
cd /Users/rodrigoseguel/Desktop/MyWork/2_Personal/1_DUOC/2_DEVOPS_003D_OLS/BDGET/EA2/mysql_local-clase/mysql-local-2.4
```

Verifica que estás en el lugar correcto:

```bash
pwd
# Debe mostrar la ruta completa terminando en .../mysql-local-2.4

ls
# Debe mostrar: Dockerfile  docker-compose.yml  pom.xml  src/  .github/  GUIA_ESTUDIANTE_2.4.md
```

> 💡 **Tip:** En macOS puedes arrastrar la carpeta desde el Finder a la ventana
> de Terminal para escribir automáticamente la ruta completa.

---

## 🔧 Paso 2 — Inicializa Git en la carpeta

Git necesita que le digas explícitamente que una carpeta es un proyecto que quieres controlar.
Ese proceso se llama "inicializar" el repositorio:

```bash
git init
```

Deberías ver:
```
Initialized empty Git repository in .../mysql-local-2.4/.git/
```

Esto crea una carpeta oculta llamada `.git/` que es donde Git guarda todo el historial.
**Nunca borres esa carpeta.**

Verifica el estado actual del proyecto:

```bash
git status
```

Verás una lista larga de archivos en rojo bajo "Untracked files".
Eso significa que Git los ve pero aún no los está rastreando — es normal en este punto.

---

## 📝 Paso 3 — Verifica el `.gitignore`

El archivo `.gitignore` le dice a Git qué archivos **nunca** debe subir a GitHub
(carpetas generadas por Maven, archivos del IDE, etc.).

Ya tienes uno creado. Verifica su contenido:

```bash
cat .gitignore
```

Debes ver algo como esto:
```
# Maven
target/
*.class
*.jar
*.war

# IDE
.idea/
*.iml
.vscode/
*.eclipse

# macOS
.DS_Store

# Logs
*.log

# Credenciales locales (nunca subir)
*.env
.env
```

> ⚠️ Si el archivo **no existe**, créalo ahora con este comando:
> ```bash
> cat > .gitignore << 'EOF'
> # Maven
> target/
> *.class
> *.jar
> *.war
>
> # IDE
> .idea/
> *.iml
> .vscode/
> *.eclipse
>
> # macOS
> .DS_Store
>
> # Logs
> *.log
>
> # Credenciales locales (nunca subir)
> *.env
> .env
> EOF
> ```

---

## 📦 Paso 4 — Agrega los archivos y haz el primer commit

Un **commit** es como un "punto de guardado" con nombre.
Primero le decimos a Git qué archivos incluir (`git add`), luego creamos el commit.

### Agregar todos los archivos al "staging" (área de preparación):

```bash
git add .
```

El punto `.` significa "agrega todo lo que está en esta carpeta".

### Revisar qué va a entrar en el commit:

```bash
git status
```

Ahora deberías ver los archivos en **verde** bajo "Changes to be committed".
Verifica que **no aparezca** la carpeta `target/` — si aparece, tu `.gitignore` no está funcionando.

### Crear el commit:

```bash
git commit -m "feat: actividad 2.4 - integración SonarCloud y Snyk en pipeline CI"
```

El texto entre comillas es el mensaje del commit — describe qué cambios incluye.

Deberías ver algo como:
```
[main (root-commit) a1b2c3d] feat: actividad 2.4 - integración SonarCloud y Snyk en pipeline CI
 18 files changed, 620 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 .github/workflows/main.yml
 create mode 100644 Dockerfile
 create mode 100644 GUIA_ESTUDIANTE_2.4.md
 create mode 100644 docker-compose.yml
 create mode 100644 pom.xml
 ...
```

> 💡 Si ves el error `Author identity unknown`, necesitas configurar tu nombre:
> ```bash
> git config --global user.name "Tu Nombre"
> git config --global user.email "tu@email.com"
> ```
> Luego repite el comando `git commit`.

---

## ☁️ Paso 5 — Crea el repositorio en GitHub y sube el código

Aquí es donde usamos `gh` (GitHub CLI) para crear el repositorio en GitHub
y enviar tu código en un solo comando:

```bash
gh repo create mysql-local-2.4 \
  --public \
  --description "Actividad 2.4 DevOps - Spring Boot + MySQL + SonarCloud + Snyk" \
  --source=. \
  --remote=origin \
  --push
```

**¿Qué hace cada parte de ese comando?**

| Parte del comando | Qué hace |
|---|---|
| `gh repo create mysql-local-2.4` | Crea un repositorio llamado `mysql-local-2.4` en tu cuenta de GitHub |
| `--public` | El repo es visible públicamente (necesario para SonarCloud gratuito) |
| `--description "..."` | Texto descriptivo que aparece en la página del repo |
| `--source=.` | Usa la carpeta actual como el código fuente del repo |
| `--remote=origin` | Configura automáticamente la conexión entre tu carpeta local y GitHub |
| `--push` | Envía el commit que hiciste en el paso anterior a GitHub de inmediato |

Si todo funciona verás:
```
✓ Created repository rodrigoseguelb/mysql-local-2.4 on GitHub
  https://github.com/rodrigoseguelb/mysql-local-2.4
✓ Added remote https://github.com/rodrigoseguelb/mysql-local-2.4.git
✓ Pushed commits to https://github.com/rodrigoseguelb/mysql-local-2.4.git
```

### Verifica que el repo existe en GitHub:

```bash
gh repo view --web
```

Esto abre el navegador directamente en tu nuevo repositorio.
Deberías ver todos tus archivos ahí.

---

## 🔑 Paso 6 — Configura los Secrets del pipeline

Los Secrets son variables cifradas que el pipeline de GitHub Actions necesita
para autenticarse en SonarCloud, Snyk y DockerHub.
**Nunca se escriben directamente en el código** — se guardan en GitHub de forma segura.

Debes configurar 6 secrets en total. Ejecuta cada comando uno por uno:

```bash
# Secret 1: Token de SonarCloud
gh secret set SONAR_TOKEN
# → Pega tu token de SonarCloud y presiona Enter

# Secret 2: Clave del proyecto en SonarCloud
gh secret set SONAR_PROJECT_KEY
# → Escribe algo como: rodrigoseguelb_mysql-local-2.4

# Secret 3: Nombre de tu organización en SonarCloud
gh secret set SONAR_ORGANIZATION
# → Escribe tu usuario de GitHub, ej: rodrigoseguelb

# Secret 4: Token de Snyk
gh secret set SNYK_TOKEN
# → Pega tu token de Snyk y presiona Enter

# Secret 5: Tu usuario de DockerHub
gh secret set DOCKERHUB_USERNAME
# → Escribe tu usuario de DockerHub

# Secret 6: Token de acceso de DockerHub
gh secret set DOCKERHUB_TOKEN
# → Pega tu token de DockerHub y presiona Enter
```

> 💡 **¿De dónde saco cada token?**
>
> | Secret | Dónde obtenerlo |
> |---|---|
> | `SONAR_TOKEN` | https://sonarcloud.io → tu proyecto → Administration → Analysis Method → GitHub Actions |
> | `SONAR_PROJECT_KEY` | Mismo lugar — formato típico: `tuusuario_mysql-local-2.4` |
> | `SONAR_ORGANIZATION` | Mismo lugar — generalmente tu usuario de GitHub |
> | `SNYK_TOKEN` | https://snyk.io → avatar → Account settings → Auth Token |
> | `DOCKERHUB_USERNAME` | Tu usuario en https://hub.docker.com |
> | `DOCKERHUB_TOKEN` | https://hub.docker.com → Account settings → Security → New Access Token |

### Verifica que los 6 secrets están configurados:

```bash
gh secret list
```

Debe mostrar exactamente esto (sin los valores, solo los nombres):
```
DOCKERHUB_TOKEN       Updated a few seconds ago
DOCKERHUB_USERNAME    Updated a few seconds ago
SNYK_TOKEN            Updated a few seconds ago
SONAR_ORGANIZATION    Updated a few seconds ago
SONAR_PROJECT_KEY     Updated a few seconds ago
SONAR_TOKEN           Updated a few seconds ago
```

> 📸 **Toma una captura de pantalla** — es uno de los entregables de la actividad.

---

## ✏️ Paso 7 — Actualiza el `pom.xml` con tus valores reales

El archivo `pom.xml` tiene valores de ejemplo que debes reemplazar por los tuyos.

Abre `pom.xml` en tu editor (VS Code, IntelliJ, etc.) y busca estas líneas:

```xml
<!-- ANTES — valores de ejemplo -->
<sonar.projectKey>TU-USUARIO_bdget</sonar.projectKey>
<sonar.organization>TU-USUARIO-SONARCLOUD</sonar.organization>
```

Cámbialas por tus valores reales:

```xml
<!-- DESPUÉS — tus valores reales -->
<sonar.projectKey>rodrigoseguelb_mysql-local-2.4</sonar.projectKey>
<sonar.organization>rodrigoseguelb</sonar.organization>
```

> ⚠️ Usa exactamente los mismos valores que pusiste en los secrets
> `SONAR_PROJECT_KEY` y `SONAR_ORGANIZATION` en el paso anterior.

Guarda el archivo y luego sube el cambio a GitHub:

```bash
# Verifica el cambio
git diff pom.xml

# Agrega el archivo modificado
git add pom.xml

# Commit con mensaje descriptivo
git commit -m "config: actualiza sonar.projectKey y sonar.organization con valores reales"

# Sube el cambio a GitHub (esto activa el pipeline automáticamente)
git push origin main
```

---

## 👀 Paso 8 — Observa el pipeline ejecutándose

El `git push` del paso anterior activa el pipeline automáticamente.
Puedes ver la ejecución de dos formas:

### Desde la terminal:

```bash
# Lista las últimas ejecuciones del pipeline
gh run list --limit 5

# Ve el progreso en tiempo real de la última ejecución
gh run watch
```

### Desde el navegador:

```bash
# Abre la pestaña Actions directamente
gh run view --web
```

O manualmente: ve a `https://github.com/rodrigoseguelb/mysql-local-2.4` → pestaña **"Actions"**.

### ¿Qué deberías ver?

Un pipeline con 11 pasos ejecutándose en este orden:

```
✅  1. Checkout del repositorio          ~5s
✅  2. Configurar Java 17                ~15s
✅  3. Ejecutar tests                    ~60s
✅  4. Subir reporte JaCoCo              ~5s
✅  5. Análisis SonarCloud               ~90s
✅  6. Escaneo Snyk (dependencias)       ~30s
✅  7. Build del proyecto                ~30s
✅  8. Login en DockerHub                ~5s
✅  9. Build de la imagen Docker         ~120s
✅ 10. Escaneo Snyk (imagen Docker)      ~45s
✅ 11. Push de la imagen a DockerHub     ~30s
```

> ⏱️ La primera ejecución tarda entre 7 y 10 minutos.
> Las siguientes son más rápidas gracias al caché de Maven.

---

## 📋 Resumen de todos los comandos — para copiar y pegar

Copia este bloque completo en un bloc de notas y ejecútalos en orden:

```bash
# ── PASO 1: Ir a la carpeta del proyecto ──────────────────────────────
cd /Users/rodrigoseguel/Desktop/MyWork/2_Personal/1_DUOC/2_DEVOPS_003D_OLS/BDGET/EA2/mysql_local-clase/mysql-local-2.4

# ── PASO 2: Inicializar Git ───────────────────────────────────────────
git init

# ── PASO 3: Verificar .gitignore (ya existe, solo verificar) ──────────
cat .gitignore

# ── PASO 4: Primer commit ─────────────────────────────────────────────
git add .
git status                    # verificar antes de commitear
git commit -m "feat: actividad 2.4 - integración SonarCloud y Snyk en pipeline CI"

# ── PASO 5: Crear repo en GitHub y subir código ───────────────────────
gh repo create mysql-local-2.4 \
  --public \
  --description "Actividad 2.4 DevOps - Spring Boot + MySQL + SonarCloud + Snyk" \
  --source=. \
  --remote=origin \
  --push

gh repo view --web             # verificar en el navegador

# ── PASO 6: Configurar secrets ────────────────────────────────────────
gh secret set SONAR_TOKEN
gh secret set SONAR_PROJECT_KEY
gh secret set SONAR_ORGANIZATION
gh secret set SNYK_TOKEN
gh secret set DOCKERHUB_USERNAME
gh secret set DOCKERHUB_TOKEN

gh secret list                 # verificar los 6 secrets

# ── PASO 7: Actualizar pom.xml con valores reales y publicar ──────────
# (edita pom.xml en tu editor primero, luego ejecuta:)
git add pom.xml
git commit -m "config: actualiza sonar.projectKey y sonar.organization con valores reales"
git push origin main

# ── PASO 8: Observar el pipeline ─────────────────────────────────────
gh run watch                   # progreso en tiempo real
# o:
gh run view --web              # abrir en el navegador
```

---

## 🛑 Errores comunes y cómo resolverlos

### ❌ "Repository already exists"

```
! Repository rodrigoseguelb/mysql-local-2.4 already exists
```

El repo ya existe en GitHub. Tienes dos opciones:

```bash
# Opción A: conectar el repo local al que ya existe en GitHub
git remote add origin https://github.com/rodrigoseguelb/mysql-local-2.4.git
git push -u origin main

# Opción B: borrar el repo existente y recrearlo (¡pierde el historial!)
gh repo delete mysql-local-2.4 --yes
# luego repite el comando gh repo create del Paso 5
```

---

### ❌ "fatal: not a git repository"

```
fatal: not a git repository (or any of the parent directories): .git
```

Olvidaste hacer `git init`. Vuelve al Paso 2.

---

### ❌ "nothing to commit, working tree clean"

Cuando ejecutas `git commit` y ves este mensaje, significa que no hay cambios nuevos.
Verifica que hiciste `git add .` antes del commit.

---

### ❌ El pipeline falla en el paso de SonarCloud

Causa más común: el `SONAR_PROJECT_KEY` en el `pom.xml` no coincide con el que
configuraste en el Secret o con el que creaste en SonarCloud.

```bash
# Verifica el valor en pom.xml
grep sonar.projectKey pom.xml

# Verifica que el secret está configurado
gh secret list
```

---

### ❌ "Authentication failed" al hacer push

```
remote: Invalid username or password.
fatal: Authentication failed
```

El token de `gh` expiró. Vuelve a autenticarte:

```bash
gh auth login
```

---

## 🗺️ El mapa completo del curso

Ahora que tienes `mysql-local-2.4` publicado, tu progreso en GitHub se ve así:

```
GitHub: rodrigoseguelb
│
├── mysql-local          (Actividad 2.3)
│   └── Pipeline: tests + JaCoCo + Docker
│
└── mysql-local-2.4      (Actividad 2.4)  ← acabas de crear este
    └── Pipeline: tests + JaCoCo + SonarCloud + Snyk + Docker
```

Cada repositorio es un **punto de avance independiente** — puedes volver a cualquiera
en cualquier momento y ver el estado exacto del proyecto en esa actividad.

---

*Guía de publicación en GitHub — Asignatura DevOps | Actividad 2.4*
