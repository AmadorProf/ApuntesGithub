---
title: "10 — GitHub avanzado y CLI"
---

# 10. GitHub Avanzado y GitHub CLI

## 1. GitHub CLI (`gh`)

La CLI oficial de GitHub permite gestionar repos, PRs, Issues y más directamente desde la terminal.

### 1.1. Instalación y autenticación

```sh
# macOS
brew install gh

# Linux (Debian/Ubuntu)
sudo apt install gh

# Windows
winget install GitHub.cli

# Autenticarse
gh auth login
# Elige GitHub.com → HTTPS → Login with a web browser
```

### 1.2. Gestión de repositorios

```sh
# Crear repositorio
gh repo create mi-proyecto --public --description "Mi proyecto" --clone

# Crear repo desde directorio actual y subirlo
gh repo create --public --source=. --push

# Ver información del repo actual
gh repo view

# Ver en el navegador
gh repo view --web

# Clonar
gh repo clone AmadorProf/ApuntesGithub

# Listar repos propios
gh repo list

# Listar repos de una organización
gh repo list mi-organizacion
```

### 1.3. Pull Requests

```sh
# Crear PR (abre editor interactivo)
gh pr create

# Crear PR con título y descripción
gh pr create --title "feat: nuevo login" --body "Implementa autenticación JWT"

# Crear PR dirigido a rama específica
gh pr create --base develop --title "feat: login"

# Listar PRs abiertos
gh pr list

# Ver PR específico
gh pr view 42

# Hacer checkout a la rama de un PR
gh pr checkout 42

# Aprobar un PR
gh pr review 42 --approve

# Mergear PR
gh pr merge 42 --squash

# Cerrar PR
gh pr close 42
```

### 1.4. Issues

```sh
# Crear issue
gh issue create --title "Bug en login" --body "El login falla con email vacío" --label bug

# Listar issues
gh issue list
gh issue list --label "bug" --state open

# Ver issue
gh issue view 15

# Cerrar issue
gh issue close 15

# Reabrir issue
gh issue reopen 15

# Comentar en issue
gh issue comment 15 --body "Investigando el problema..."
```

### 1.5. Workflows y Actions

```sh
# Listar workflows
gh workflow list

# Ver ejecuciones recientes
gh run list

# Ver detalles de una ejecución
gh run view 123456

# Ver logs de una ejecución
gh run view 123456 --log

# Disparar workflow manualmente
gh workflow run ci.yml

# Disparar con inputs
gh workflow run deploy.yml --field environment=production
```

---

## 2. Protección de ramas (Branch Protection)

Las reglas de protección evitan pushes directos a ramas importantes y fuerzan revisiones de código.

### 2.1. Configurar desde la web

**Settings → Branches → Add branch protection rule**

Opciones más importantes:

| Opción | Efecto |
|--------|--------|
| **Require a pull request** | Nadie puede hacer push directo; todo va por PR |
| **Required approvals** | Número mínimo de aprobaciones antes de merge |
| **Require status checks** | Los checks de CI deben pasar antes del merge |
| **Require branches to be up to date** | La rama debe estar actualizada con `main` |
| **Restrict who can push** | Solo usuarios/equipos específicos |
| **Require signed commits** | Todos los commits deben estar firmados con GPG |

### 2.2. Configurar con GitHub CLI

```sh
gh api repos/OWNER/REPO/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["CI"]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1}' \
  --field restrictions=null
```

---

## 3. CODEOWNERS

El archivo `CODEOWNERS` define qué usuarios o equipos deben revisar cambios en partes específicas del código.

### 3.1. Ubicación

Puede estar en la raíz, en `.github/` o en `docs/`.

### 3.2. Sintaxis

```
# .github/CODEOWNERS

# Dueño por defecto (todo lo que no esté en otra regla)
*                   @amadorprof

# El equipo de frontend revisa todos los cambios en src/
src/                @mi-org/frontend-team

# Un usuario específico revisa la configuración de CI
.github/workflows/  @devops-lead

# Múltiples propietarios
*.sql               @amadorprof @db-team

# Archivos específicos
package.json        @mi-org/arquitectura
```

Cuando un PR toca archivos con CODEOWNERS, GitHub solicita automáticamente revisión a los propietarios.

---

## 4. GitHub Projects

GitHub Projects es un tablero Kanban/table integrado para gestionar el trabajo.

```sh
# Ver proyectos del repositorio
gh project list

# Crear proyecto
gh project create --owner "@me" --title "Sprint 1"

# Añadir issue a un proyecto
gh project item-add 1 --owner "@me" --url "https://github.com/owner/repo/issues/42"
```

---

## 5. Dependabot

Dependabot analiza las dependencias del proyecto y abre PRs automáticos cuando hay actualizaciones o vulnerabilidades.

### 5.1. Configurar Dependabot

Crear `.github/dependabot.yml`:

```yaml
version: 2
updates:
  # Actualizar dependencias npm
  - package-ecosystem: npm
    directory: /
    schedule:
      interval: weekly
    open-pull-requests-limit: 10

  # Actualizar GitHub Actions
  - package-ecosystem: github-actions
    directory: /
    schedule:
      interval: weekly
```

### 5.2. Alerts de seguridad

GitHub analiza automáticamente las dependencias en busca de CVEs conocidos. Cuando encuentra una vulnerabilidad:

1. Envía alerta por email
2. Aparece en **Security → Dependabot alerts**
3. Con Dependabot habilitado, abre un PR con el fix automáticamente

---

## 6. GitHub Packages

Permite publicar paquetes npm, Docker images, Maven, etc. directamente en GitHub.

```sh
# Publicar paquete npm en GitHub Packages
# En package.json:
# "name": "@amadorprof/mi-paquete"
# "publishConfig": {"registry": "https://npm.pkg.github.com"}

npm publish

# Instalar paquete desde GitHub Packages
npm install @amadorprof/mi-paquete --registry https://npm.pkg.github.com

# Docker image
docker tag mi-imagen ghcr.io/amadorprof/mi-imagen:latest
docker push ghcr.io/amadorprof/mi-imagen:latest
```

---

## Ejercicios

**10.1** — Instala GitHub CLI y autentícate. Luego usa `gh repo create` para crear un repositorio público directamente desde la terminal, clonarlo y hacer un primer commit.

**10.2** — Con `gh pr create`, abre un Pull Request en cualquiera de tus repositorios. Añade título, descripción y una etiqueta. Luego ciérralo con `gh pr close`.

**10.3** — Configura una **branch protection rule** en `main` de un repositorio tuyo que: requiera al menos 1 aprobación en PR, exija que los status checks (CI) pasen, y prohíba el push directo (incluso para admins). Verifica intentando hacer push directo.

**10.4** — Crea un archivo `.github/CODEOWNERS` en un repositorio tuyo donde tú seas el propietario de todos los archivos `*.md` y de la carpeta `src/`. Abre un PR que modifique un `.md` y verifica que GitHub te pide revisión automáticamente.

<details markdown="1">
<summary>Soluciones</summary>

---

**10.1**

```sh
# Instalar (macOS)
brew install gh

# Autenticar
gh auth login
# → GitHub.com → HTTPS → Login with a web browser

# Crear repositorio y clonarlo
gh repo create mi-nuevo-repo --public --description "Repo creado con gh CLI" --clone
cd mi-nuevo-repo

# Primer commit
echo "# Mi Nuevo Repo" > README.md
git add README.md
git commit -m "docs: initial README"
git push
```

---

**10.2**

```sh
# Crear rama con cambio
git checkout -b feature/prueba-gh-cli
echo "cambio" >> README.md
git add . && git commit -m "feat: test gh cli pr"
git push -u origin feature/prueba-gh-cli

# Abrir PR
gh pr create \
  --title "feat: prueba gh CLI" \
  --body "PR creado desde la terminal con gh CLI" \
  --label "documentation"

# Listar PRs para ver el número
gh pr list

# Cerrar PR (sin merge)
gh pr close 1
```

---

**10.3**

En GitHub web:
1. Settings → Branches → Add branch protection rule
2. Branch name pattern: `main`
3. Activar: ✅ Require a pull request before merging → Required approvals: 1
4. Activar: ✅ Require status checks to pass → añadir el check de CI
5. Activar: ✅ Do not allow bypassing the above settings (incluye admins)
6. Save changes

Verificación:
```sh
git checkout main
echo "test" >> README.md
git add . && git commit -m "test direct push"
git push
# remote: error: GH006: Protected branch update failed for refs/heads/main.
# remote: error: At least 1 approving review is required...
```

---

**10.4**

```
# .github/CODEOWNERS

# Todos los markdown
*.md    @amadorprof

# Carpeta src
src/    @amadorprof
```

```sh
mkdir -p .github
cat > .github/CODEOWNERS << 'EOF'
*.md    @amadorprof
src/    @amadorprof
EOF

git add .github/CODEOWNERS
git commit -m "chore: add CODEOWNERS"
git push
```

Al abrir un PR que modifique un `.md`, en la sección **Reviewers** del PR aparecerá `@amadorprof` solicitado automáticamente.

</details>
