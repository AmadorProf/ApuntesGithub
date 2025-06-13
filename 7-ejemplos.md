# 7. Ejemplos Prácticos

En esta sección se presentan casos reales de uso de Git y GitHub para consolidar los conocimientos adquiridos, desde proyectos individuales hasta colaboración en equipos y contribuciones a código abierto.

## 1. Caso 1: Crear un Repositorio desde Cero

### 1.1. Preparación del Entorno
```sh
# Crear directorio del proyecto
mkdir mi-proyecto-web
cd mi-proyecto-web

# Inicializar repositorio Git
git init

# Verificar estado
git status
```

### 1.2. Estructura Inicial del Proyecto
```sh
# Crear estructura de archivos
mkdir src css js
touch README.md index.html src/app.js css/styles.css

# Crear contenido inicial
echo "# Mi Proyecto Web" > README.md
echo "Aplicación web básica con HTML, CSS y JavaScript" >> README.md
```

**Contenido del index.html:**
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Proyecto Web</title>
    <link rel="stylesheet" href="css/styles.css">
</head>
<body>
    <h1>¡Hola Mundo!</h1>
    <p>Mi primera aplicación web con Git</p>
    <script src="src/app.js"></script>
</body>
</html>
```

### 1.3. Primer Commit
```sh
# Agregar archivos al staging area
git add .

# Verificar qué se va a commitear
git status

# Realizar primer commit
git commit -m "feat: estructura inicial del proyecto

- Añadir HTML básico
- Crear estructura de carpetas
- Incluir README.md con descripción"
```

### 1.4. Configurar .gitignore
```sh
# Crear archivo .gitignore
touch .gitignore
```

**Contenido del .gitignore:**
```gitignore
# Logs
*.log
logs/

# Dependencias
node_modules/
npm-debug.log*

# Archivos de entorno
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Archivos de IDE
.vscode/
.idea/
*.swp
*.swo
*~

# Archivos del sistema
.DS_Store
Thumbs.db

# Archivos de compilación
dist/
build/
*.min.js
*.min.css

# Archivos temporales
tmp/
temp/
```

### 1.5. Subir a GitHub
```sh
# Agregar .gitignore
git add .gitignore
git commit -m "chore: agregar .gitignore con exclusiones básicas"

# Crear repositorio en GitHub primero, luego:
git remote add origin https://github.com/tu-usuario/mi-proyecto-web.git

# Verificar conexión
git remote -v

# Subir código
git push -u origin main
```

## 2. Caso 2: Contribuir a un Proyecto Open-Source

### 2.1. Investigación y Preparación
```sh
# Antes de contribuir, explorar el proyecto
# 1. Leer README.md, CONTRIBUTING.md, CODE_OF_CONDUCT.md
# 2. Revisar issues abiertos
# 3. Entender la estructura del proyecto
```

### 2.2. Hacer Fork y Configurar Upstream
```sh
# 1. Hacer Fork desde GitHub UI
# 2. Clonar tu fork
git clone https://github.com/tu-usuario/proyecto-opensource.git
cd proyecto-opensource

# 3. Configurar upstream (repositorio original)
git remote add upstream https://github.com/autor-original/proyecto-opensource.git

# 4. Verificar remotos
git remote -v
# origin    https://github.com/tu-usuario/proyecto-opensource.git (fetch)
# origin    https://github.com/tu-usuario/proyecto-opensource.git (push)
# upstream  https://github.com/autor-original/proyecto-opensource.git (fetch)
# upstream  https://github.com/autor-original/proyecto-opensource.git (push)
```

### 2.3. Mantenerse Actualizado
```sh
# Sincronizar con el repositorio original
git fetch upstream
git checkout main
git merge upstream/main

# O usar rebase para historial más limpio
git rebase upstream/main
```

### 2.4. Desarrollar la Contribución
```sh
# Crear rama descriptiva
git checkout -b fix/issue-123-login-validation

# Hacer cambios necesarios
# ... editar archivos ...

# Agregar cambios por partes (buena práctica)
git add src/components/LoginForm.js
git commit -m "fix: validar email antes de enviar formulario

- Agregar validación de formato de email
- Mostrar mensaje de error específico
- Fixes #123"

# Si hay más cambios relacionados
git add tests/LoginForm.test.js
git commit -m "test: agregar tests para validación de email"
```

### 2.5. Preparar y Enviar Pull Request
```sh
# Asegurarse de estar actualizado
git fetch upstream
git rebase upstream/main

# Ejecutar tests localmente
npm test

# Subir rama
git push origin fix/issue-123-login-validation

# Crear PR desde GitHub UI con:
# - Título descriptivo
# - Descripción detallada
# - Referencias a issues
# - Screenshots si es necesario
```

### 2.6. Gestionar Feedback del Review
```sh
# Si piden cambios, hacer commits adicionales
git add .
git commit -m "fix: aplicar sugerencias del code review"

# Hacer push de los cambios
git push origin fix/issue-123-login-validation

# Si piden squash commits antes del merge
git rebase -i HEAD~3  # Para los últimos 3 commits
```

## 3. Caso 3: Flujo de Trabajo en Equipo con Git Flow

### 3.1. Configuración Inicial del Equipo
```sh
# Cada miembro del equipo clona el repo
git clone https://github.com/empresa/proyecto-equipo.git
cd proyecto-equipo

# Configurar Git Flow
git flow init

# Configuración típica:
# Branch name for production releases: [main]
# Branch name for "next release" development: [develop]
# Feature branches: [feature/]
# Release branches: [release/]
# Hotfix branches: [hotfix/]
# Support branches: [support/]
```

### 3.2. Estructura de Ramas

| Rama | Propósito | Origen | Destino |
|------|-----------|---------|---------|
| `main` | Versiones en producción | - | - |
| `develop` | Integración de desarrollo | `main` | `main` |
| `feature/*` | Nuevas funcionalidades | `develop` | `develop` |
| `release/*` | Preparación de versiones | `develop` | `main` + `develop` |
| `hotfix/*` | Correcciones urgentes | `main` | `main` + `develop` |

### 3.3. Desarrollar una Nueva Funcionalidad
```sh
# Desarrollador A: Nueva funcionalidad de dashboard
git flow feature start dashboard-analytics

# Trabajar en la funcionalidad
echo "Dashboard analytics code..." > src/dashboard.js
git add src/dashboard.js
git commit -m "feat: implementar dashboard de analytics

- Crear componente Dashboard
- Integrar con API de métricas
- Añadir gráficos interactivos"

# Más desarrollo...
git add .
git commit -m "feat: agregar filtros de fecha al dashboard"

# Publicar para colaboración (opcional)
git flow feature publish dashboard-analytics

# Finalizar funcionalidad
git flow feature finish dashboard-analytics
```

### 3.4. Colaboración en Funcionalidades
```sh
# Desarrollador B: Trabajar en la misma funcionalidad
git flow feature pull origin dashboard-analytics

# Hacer cambios
git add .
git commit -m "feat: optimizar consultas del dashboard"

# Subir cambios
git push origin feature/dashboard-analytics
```

### 3.5. Preparar Release
```sh
# Crear rama de release
git flow release start 2.1.0

# Ajustes finales
echo "version: 2.1.0" > version.txt
git add version.txt
git commit -m "chore: actualizar versión a 2.1.0"

# Ejecutar tests completos
npm run test:full
npm run lint
npm run build

# Finalizar release
git flow release finish 2.1.0
# Esto hará:
# - Merge a main
# - Tag v2.1.0
# - Merge back a develop
# - Eliminar rama release
```

### 3.6. Gestionar Hotfix
```sh
# Problema crítico en producción
git flow hotfix start 2.1.1

# Corregir el problema
git add .
git commit -m "fix: corregir vulnerabilidad de seguridad crítica

- Sanitizar entrada de usuario
- Actualizar dependencias de seguridad
- Agregar validación adicional"

# Ejecutar tests de seguridad
npm run test:security

# Finalizar hotfix
git flow hotfix finish 2.1.1
```

## 4. Uso Avanzado de .gitignore

### 4.1. Patrones Comunes por Tecnología

**Node.js / JavaScript:**
```gitignore
# Dependencias
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Archivos de producción
/dist
/build

# Variables de entorno
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Logs
logs
*.log

# Coverage
coverage/
.nyc_output
```

**Python:**
```gitignore
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# Virtual environments
venv/
env/
ENV/

# Distribution / packaging
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/

# PyInstaller
*.manifest
*.spec

# Jupyter Notebook
.ipynb_checkpoints
```

**Java:**
```gitignore
# Compiled class file
*.class

# Log file
*.log

# Maven
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties

# Gradle
.gradle/
build/
```

### 4.2. Gitignore Globales
```sh
# Configurar gitignore global para el sistema
git config --global core.excludesfile ~/.gitignore_global

# Crear archivo global
touch ~/.gitignore_global
```

**Contenido del ~/.gitignore_global:**
```gitignore
# Archivos del sistema
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Editores
*~
.vscode/
.idea/
*.swp
*.swo

# Archivos temporales
*.tmp
*.temp
```

### 4.3. Comandos Útiles para .gitignore
```sh
# Ver archivos ignorados
git status --ignored

# Limpiar archivos ignorados
git clean -fX

# Forzar agregar archivo ignorado
git add -f archivo-ignorado.txt

# Verificar por qué un archivo está ignorado
git check-ignore -v archivo.txt

# Dejar de trackear archivo ya commiteado
git rm --cached archivo.txt
echo "archivo.txt" >> .gitignore
git commit -m "chore: dejar de trackear archivo.txt"
```

## 5. Herramientas de Análisis de Historial

### 5.1. Comandos Básicos de Análisis
```sh
# Ver historial completo
git log --oneline --graph --all

# Ver cambios en un archivo específico
git log -p archivo.js

# Buscar commits por autor
git log --author="Juan Pérez"

# Buscar commits por mensaje
git log --grep="fix"

# Ver estadísticas de commits
git log --stat

# Ver commits entre fechas
git log --since="2023-01-01" --until="2023-12-31"
```

### 5.2. Análisis Avanzado
```sh
# Ver quién modificó cada línea
git blame archivo.js

# Buscar cuándo se introdujo un bug
git bisect start
git bisect bad HEAD
git bisect good v1.0.0

# Ver diferencias entre ramas
git diff main..develop

# Mostrar archivos modificados entre commits
git diff --name-only HEAD~3..HEAD

# Estadísticas de contribuciones
git shortlog -sn
```

### 5.3. Herramientas Gráficas

**Tig (Terminal):**
```sh
# Instalación
sudo apt install tig      # Ubuntu/Debian
brew install tig          # macOS
choco install tig         # Windows

# Uso básico
tig                       # Explorar historial
tig status               # Ver estado actual
tig blame archivo.js     # Ver blame de archivo
```

**Comandos útiles en Tig:**
- `q`: Salir
- `Enter`: Ver detalles del commit
- `d`: Ver diff del commit
- `t`: Ver árbol de archivos
- `/`: Buscar

**GUI Recomendadas:**
- **GitKraken**: Interfaz moderna y potente
- **GitHub Desktop**: Simple y efectiva
- **Sourcetree**: Completa y gratuita
- **GitLens (VS Code)**: Extensión para VS Code

### 5.4. Análisis de Productividad
```sh
# Commits por autor en el último mes
git log --since="1 month ago" --pretty=format:"%an" | sort | uniq -c | sort -nr

# Líneas de código por autor
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done

# Archivos más modificados
git log --pretty=format: --name-only | sort | uniq -c | sort -rg | head -10
```

## 6. Casos de Recuperación y Resolución de Problemas

### 6.1. Deshacer Cambios Locales
```sh
# Deshacer cambios no commiteados
git checkout -- archivo.js

# Deshacer todos los cambios no commiteados
git checkout .

# Remover archivos del staging area
git reset HEAD archivo.js

# Deshacer último commit (mantener cambios)
git reset HEAD~1

# Deshacer último commit (perder cambios)
git reset --hard HEAD~1
```

### 6.2. Recuperar Commits Perdidos
```sh
# Ver historial de referencias
git reflog

# Recuperar commit específico
git cherry-pick abc123

# Crear nueva rama desde commit perdido
git branch recuperacion abc123
```

### 6.3. Resolver Conflictos Complejos
```sh
# Configurar herramienta de merge
git config --global merge.tool vimdiff

# Usar herramienta gráfica
git mergetool

# Abortar merge problemático
git merge --abort

# Continuar merge después de resolver
git merge --continue
```

## 7. Mejores Prácticas Consolidadas

### 7.1. Flujo de Trabajo Diario
```sh
# Rutina matutina
git checkout main
git pull origin main
git checkout develop
git pull origin develop

# Crear nueva funcionalidad
git checkout -b feature/nueva-funcionalidad

# Trabajo durante el día
git add .
git commit -m "feat: progreso en nueva funcionalidad"

# Rutina antes de finalizar
git checkout develop
git pull origin develop
git checkout feature/nueva-funcionalidad
git rebase develop
```

### 7.2. Checklist Antes de Hacer Push
- [ ] Ejecutar tests localmente
- [ ] Revisar que .gitignore esté actualizado
- [ ] Verificar que no hay credenciales en el código
- [ ] Escribir mensaje de commit descriptivo
- [ ] Hacer rebase si es necesario
- [ ] Verificar que la rama está actualizada

### 7.3. Comandos de Emergencia
```sh
# Guardar trabajo actual rápidamente
git stash push -m "trabajo en progreso"

# Cambiar a otra rama urgentemente
git checkout main

# Volver al trabajo
git stash pop

# Crear backup de rama local
git branch backup-mi-trabajo
```


