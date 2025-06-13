# Apuntes de Git y GitHub

Guía práctica para aprender los fundamentos de Git y GitHub, desde configuración básica hasta flujos de trabajo colaborativos.

## Índice

### 1. [Introducción](1-introduccion.md)  
- ¿Qué es Git? ¿Qué es GitHub?  
- Diferencias entre Git y otros sistemas de control de versiones (SVN, Mercurial)  
- Ventajas del control de versiones distribuido  
- Casos de uso comunes y flujos de trabajo generales  
- Esquema de la aquitectura de github

### 2. [Instalación y Configuración](2-instalacion-config.md)  
- Cómo instalar Git en Windows, macOS y Linux  
- Verificación de instalación (`git --version`)  
- Configuración inicial con `git config`  
  - Usuario (`user.name`, `user.email`)  
  - Editor por defecto  
  - Aliases útiles (e.g., `st` para `status`)  
- Archivo `.gitconfig` y configuración global vs local  

### 3. [Comandos Básicos](3-comandos-basicos.md)  
- Crear y clonar repositorios: `git init`, `git clone`  
- Estado del repositorio: `git status`, `git diff`  
- Agregar cambios: `git add`, uso de `.` vs archivos específicos  
- Confirmar cambios: `git commit`, opción `-m`, commits múltiples  
- Historial de cambios: `git log`, `git log --oneline`, `git show`  
- Buenas prácticas en mensajes de commit (Convencional Commits, ejemplos)  

### 4. [Ramas y Fusiones](4-ramas.md)  
- Qué son las ramas y por qué usarlas  
- Crear y cambiar ramas: `git branch`, `git checkout`, `git switch`  
- Fusiones de ramas: `git merge` (fast-forward vs no fast-forward)  
- Rebase: cuándo y por qué usar `git rebase`  
- Resolución de conflictos paso a paso  
- Visualización de ramas: `git log --graph`, herramientas gráficas  

### 5. [Trabajo con GitHub](5-github.md)  
- Conectar un repositorio local con GitHub: `git remote add`  
- Subir y bajar cambios: `git push`, `git pull`, `git fetch`  
- Manejo de autenticación (HTTPS vs SSH, tokens personales)  
- Forks, Pull Requests (PRs) y revisión de código  
- Issues y gestión de tareas en GitHub  
- GitHub Projects, Actions y otras herramientas útiles  

### 6. [Ramas Avanzadas y Git Flow](6-ramas-avanzadas-gitflow.md)  
- Conceptos fundamentales de ramificación avanzada
- ¿Qué es Git Flow? Instalación y configuración
- Estructura de ramas: main, develop, feature, release, hotfix
- Comandos Git Flow: start, finish, publish
- GitHub Flow como alternativa simplificada
- Estrategias avanzadas de merge y rebase
  - Merge vs Rebase: cuándo usar cada uno
  - Rebase interactivo para limpiar historial
  - Squash merge y fast-forward
- Gestión avanzada de conflictos
- Automatización con Git Hooks (pre-commit, pre-push)
- Mejores prácticas: nomenclatura de ramas y commits semánticos
- Comparación de estrategias: Git Flow vs GitHub Flow vs GitLab Flow
- Herramientas complementarias y limpieza de ramas

### 7. [Ejemplos Prácticos](7-ejemplos.md)  
- Caso 1: Crear un repositorio desde cero  
  - Inicializar, agregar archivos, primer commit, subir a GitHub  
- Caso 2: Contribuir a un proyecto open-source  
  - Hacer fork, clonar, crear ramas, hacer PR  
- Caso 3: Flujo de trabajo en equipo (Git Flow básico)  
  - Ramas `main`, `develop`, `feature/*`, `hotfix/*`  
- Uso de `.gitignore` y archivos comunes a excluir  
- Analizar historial con herramientas como `tig` o GUIs  


## Documentación
- [*Pro Git* (libro gratuito)](https://git-scm.com/book/en/v2) – Guía completa y en profundidad sobre Git, con explicaciones claras y ejemplos prácticos.

- [Documentación oficial de Git](https://git-scm.com/docs/git) – Referencia técnica completa de todos los comandos y opciones disponibles en Git.
