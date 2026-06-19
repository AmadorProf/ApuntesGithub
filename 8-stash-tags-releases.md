---
title: "8 — Stash, tags y releases"
---

# 8. Stash, Tags y Releases

## 1. Git Stash — Guardar trabajo sin confirmar

`git stash` guarda temporalmente los cambios del directorio de trabajo y el staging area sin hacer commit, permitiendo cambiar de rama con el espacio de trabajo limpio.

### 1.1. Comandos básicos

```sh
# Guardar todos los cambios (tracked)
git stash

# Guardar con nombre descriptivo
git stash push -m "wip: formulario de login"

# Incluir archivos nuevos sin seguimiento
git stash push -u -m "wip: login + nuevos archivos"

# Ver lista de stashes guardados
git stash list
# stash@{0}: On main: wip: formulario de login
# stash@{1}: On develop: wip: navbar

# Aplicar el último stash (lo mantiene en la lista)
git stash apply

# Aplicar stash específico
git stash apply stash@{1}

# Aplicar y eliminar de la lista
git stash pop

# Eliminar un stash sin aplicar
git stash drop stash@{0}

# Eliminar todos los stashes
git stash clear
```

### 1.2. Inspeccionar un stash

```sh
# Ver qué contiene un stash
git stash show stash@{0}

# Ver el diff completo
git stash show -p stash@{0}
```

### 1.3. Crear rama desde un stash

```sh
# Muy útil si el stash ya no aplica limpiamente sobre la rama actual
git stash branch feature/nuevo-desde-stash stash@{0}
```

### 1.4. Flujo de uso típico

```sh
# 1. Tienes cambios a medias en main
echo "trabajo en curso" > login.js
git add login.js

# 2. Necesitas cambiar urgentemente a otra rama
git stash push -m "wip: login form"

# 3. Trabajas en la otra rama
git checkout hotfix
# ... haces tu trabajo ...
git checkout main

# 4. Recuperas tu trabajo
git stash pop
```

---

## 2. Git Tag — Marcar versiones

Los tags son referencias fijas a commits específicos, ideales para marcar versiones de software.

### 2.1. Tipos de tags

| Tipo | Descripción |
|------|-------------|
| **Lightweight** | Simple puntero a un commit (como un alias) |
| **Annotated** | Objeto completo con autor, fecha, mensaje y firma GPG opcional |

**Siempre usa tags anotados para releases.**

### 2.2. Crear tags

```sh
# Tag ligero
git tag v1.0.0

# Tag anotado (recomendado)
git tag -a v1.0.0 -m "Primera versión estable"

# Tag en un commit anterior
git tag -a v0.9.0 abc1234 -m "Release candidate"
```

### 2.3. Listar y ver tags

```sh
# Listar todos los tags
git tag

# Filtrar por patrón
git tag -l "v1.*"

# Ver detalles de un tag anotado
git show v1.0.0
# tag v1.0.0
# Tagger: Amador <amador@email.com>
# Date: ...
# Primera versión estable
# commit abc123...
```

### 2.4. Compartir tags con el remoto

```sh
# Push de un tag específico
git push origin v1.0.0

# Push de todos los tags locales
git push origin --tags

# Eliminar tag remoto
git push origin --delete v1.0.0

# Eliminar tag local
git tag -d v1.0.0
```

### 2.5. Versionado semántico (SemVer)

```
MAJOR.MINOR.PATCH
  │     │     └── Bug fixes compatibles
  │     └──────── Nuevas funcionalidades compatibles
  └────────────── Cambios incompatibles con versiones anteriores
```

| Versión | Cuándo usarla |
|---------|----------------|
| `v1.0.0` | Primera versión estable |
| `v1.1.0` | Nueva funcionalidad sin romper nada |
| `v1.1.1` | Bug fix en producción |
| `v2.0.0` | Cambio incompatible en la API |

```sh
# Pre-releases
git tag -a v2.0.0-alpha.1 -m "Alpha release"
git tag -a v2.0.0-beta.2 -m "Beta release"
git tag -a v2.0.0-rc.1 -m "Release candidate"
```

---

## 3. GitHub Releases

Las GitHub Releases envuelven un tag con notas de la versión, binarios descargables y changelogs.

### 3.1. Crear release desde la web

1. Repositorio → **Releases** → **Draft a new release**
2. Selecciona o crea un tag (`v1.0.0`)
3. Añade título y descripción (changelog, breaking changes, etc.)
4. Adjunta binarios si procede
5. Marca como pre-release si aún no es estable
6. **Publish release**

### 3.2. Crear release con GitHub CLI

```sh
# Release básica desde el tag existente
gh release create v1.0.0 --title "v1.0.0 — Primera versión" --notes "Primera versión estable."

# Release con fichero de notas
gh release create v1.1.0 --title "v1.1.0" --notes-file CHANGELOG.md

# Pre-release
gh release create v2.0.0-beta.1 --prerelease --title "v2.0.0 Beta"

# Adjuntar binarios
gh release create v1.0.0 ./dist/app-linux ./dist/app-macos --title "v1.0.0"
```

### 3.3. Changelog automático

GitHub puede generar changelogs automáticos basándose en los títulos de los PRs mergeados desde el último release.

```sh
gh release create v1.2.0 --generate-notes
```

O en la web, al crear la release: **Generate release notes**.

---

## Ejercicios

**8.1** — Tienes cambios sin terminar en `main`. Guárdalos con `git stash` (con mensaje descriptivo), crea una rama `hotfix` donde apliques un commit de fix, vuelve a `main` y recupera tu trabajo con `git stash pop`.

**8.2** — Lista todos tus stashes con `git stash list`. Aplica el stash número 1 (sin eliminarlo de la lista) con `git stash apply stash@{1}`. ¿Qué diferencia hay entre `apply` y `pop`?

**8.3** — Crea un tag anotado `v1.0.0` en el último commit de tu repositorio. Súbelo a GitHub. Después crea un segundo tag `v1.1.0` en el mismo commit y sube **todos** los tags de una vez.

**8.4** — Usando la GitHub CLI (`gh`), crea una release para `v1.0.0` con título "Primera versión estable" y al menos 2 líneas de notas de la versión. Verifica que aparece en la sección Releases de tu repositorio.

**8.5** — Explica cuándo usarías `v2.0.0` en lugar de `v1.5.0` según SemVer. Pon un ejemplo concreto de cambio que obligaría a incrementar la versión MAJOR.

<details markdown="1">
<summary>Soluciones</summary>

---

**8.1**

```sh
# Cambios en curso
echo "nueva funcionalidad" > feature.js
git add feature.js

# Guardar stash
git stash push -m "wip: nueva funcionalidad de búsqueda"

# Ir a hotfix
git checkout -b hotfix
echo "fix" > bugfix.js && git add . && git commit -m "fix: correct null check"
git checkout main
git merge hotfix --no-ff -m "merge: hotfix"

# Recuperar trabajo
git stash pop
git status  # feature.js aparece de nuevo en staging
```

---

**8.2**

```sh
git stash list
# stash@{0}: wip más reciente
# stash@{1}: wip anterior

git stash apply stash@{1}  # aplica pero mantiene el stash en la lista

# Diferencia:
# git stash apply → aplica y MANTIENE en la lista (puedes aplicarlo otra vez)
# git stash pop   → aplica y ELIMINA de la lista (uso habitual)
```

---

**8.3**

```sh
git tag -a v1.0.0 -m "Primera versión estable"
git push origin v1.0.0

git tag -a v1.1.0 -m "Añade formulario de contacto"
git push origin --tags  # sube v1.0.0 y v1.1.0
```

---

**8.4**

```sh
gh release create v1.0.0 \
  --title "Primera versión estable" \
  --notes "## Novedades
- Autenticación con JWT
- Formulario de contacto
- Soporte multiidioma"

# Verificar
gh release list
# v1.0.0  Latest  Primera versión estable
```

---

**8.5**

Se incrementa **MAJOR** cuando hay cambios incompatibles hacia atrás (*breaking changes*).

Ejemplo concreto: una API REST que tenía `GET /users` devolviendo un array plano:
```json
[{"id": 1, "name": "Ana"}]
```
Pasa a devolver un objeto envuelto:
```json
{"data": [{"id": 1, "name": "Ana"}], "total": 1}
```
Cualquier cliente que accediera al índice `[0]` directamente dejaría de funcionar. Eso es un **breaking change → v2.0.0**.

Un campo nuevo opcional (p.ej. añadir `"email"` al objeto usuario) sería `v1.x.0` porque no rompe los clientes existentes.

</details>
