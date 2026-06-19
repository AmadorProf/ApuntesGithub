---
title: "9 — GitHub Actions"
---

# 9. GitHub Actions

GitHub Actions es la plataforma de CI/CD integrada en GitHub. Permite automatizar tareas (tests, builds, deploys) cada vez que ocurre un evento en el repositorio.

---

## 1. Conceptos clave

```
Evento (push, PR, schedule...)
    └── Workflow (.github/workflows/ci.yml)
            └── Job (set de pasos que corre en un Runner)
                    └── Step (comando o Action reutilizable)
```

| Concepto | Descripción |
|----------|-------------|
| **Workflow** | Proceso automatizado definido en YAML |
| **Event** | Lo que dispara el workflow (push, pull_request, schedule…) |
| **Job** | Conjunto de steps que corren en una misma máquina |
| **Step** | Comando shell o Action reutilizable |
| **Runner** | Máquina virtual donde se ejecuta el job (ubuntu, windows, macos) |
| **Action** | Bloque reutilizable publicado en GitHub Marketplace |

---

## 2. Estructura de un workflow

Los workflows viven en `.github/workflows/` con extensión `.yml`.

```yaml
name: CI                        # Nombre del workflow (aparece en la pestaña Actions)

on:                             # Eventos que lo disparan
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:                           # Uno o más jobs
  test:                         # Nombre del job
    runs-on: ubuntu-latest      # Sistema del runner

    steps:
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Configurar Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Instalar dependencias
        run: pip install -r requirements.txt

      - name: Ejecutar tests
        run: pytest
```

---

## 3. Eventos más comunes

```yaml
on:
  push:                         # Cualquier push
  push:
    branches: [main, develop]   # Push solo en estas ramas
    paths: ["src/**"]           # Solo si cambian archivos en src/

  pull_request:
    types: [opened, synchronize, reopened]

  schedule:
    - cron: "0 8 * * 1-5"      # Lunes a viernes a las 8:00 UTC

  workflow_dispatch:            # Disparado manualmente desde la UI
    inputs:
      environment:
        description: "Deploy target"
        required: true
        default: staging

  release:
    types: [published]          # Cuando se publica un release
```

---

## 4. Actions reutilizables

El Marketplace de GitHub tiene miles de Actions. Las más usadas:

```yaml
steps:
  # Descargar el código del repositorio
  - uses: actions/checkout@v4

  # Configurar Node.js
  - uses: actions/setup-node@v4
    with:
      node-version: "20"
      cache: "npm"

  # Configurar Python
  - uses: actions/setup-python@v5
    with:
      python-version: "3.12"

  # Cachear dependencias
  - uses: actions/cache@v4
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

  # Subir artefactos (ej: reportes de tests)
  - uses: actions/upload-artifact@v4
    with:
      name: test-results
      path: reports/

  # Notificar en Slack (Marketplace)
  - uses: slackapi/slack-github-action@v1
    with:
      slack-bot-token: ${{ secrets.SLACK_BOT_TOKEN }}
      channel-id: "C123456"
      slack-message: "Deploy completado ✅"
```

---

## 5. Variables de entorno y secrets

```yaml
env:
  NODE_ENV: production          # Variable de entorno global

jobs:
  deploy:
    runs-on: ubuntu-latest
    env:
      APP_PORT: 3000            # Variable a nivel de job

    steps:
      - name: Deploy
        env:
          API_KEY: ${{ secrets.API_KEY }}   # Secret encriptado
          DB_URL: ${{ vars.DB_URL }}        # Variable de repositorio (no encriptada)
        run: ./deploy.sh
```

Los **secrets** se configuran en: Settings → Secrets and variables → Actions → New repository secret.

---

## 6. Jobs paralelos y dependencias

```yaml
jobs:
  test-backend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test --workspace=backend

  test-frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test --workspace=frontend

  deploy:
    runs-on: ubuntu-latest
    needs: [test-backend, test-frontend]   # Solo corre si ambos tests pasan
    steps:
      - run: echo "Deploying..."
```

---

## 7. Ejemplos prácticos

### CI para Node.js

```yaml
name: Node.js CI
on: [push, pull_request]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20, 22]    # Probar en varias versiones

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: npm
      - run: npm ci
      - run: npm run build
      - run: npm test
```

### Deploy a GitHub Pages

```yaml
name: Deploy Pages
on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci && npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist/
      - uses: actions/deploy-pages@v4
```

### Lint + test + cobertura

```yaml
name: Quality Gate
on: [pull_request]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r requirements-dev.txt
      - run: ruff check .             # Linter
      - run: pytest --cov=src --cov-report=xml
      - uses: codecov/codecov-action@v4
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
```

---

## 8. Contextos y expresiones

```yaml
steps:
  - name: Info del evento
    run: |
      echo "Rama: ${{ github.ref_name }}"
      echo "Actor: ${{ github.actor }}"
      echo "SHA: ${{ github.sha }}"
      echo "Repo: ${{ github.repository }}"

  - name: Solo en main
    if: github.ref == 'refs/heads/main'
    run: echo "Esto solo corre en main"

  - name: Solo si falló el paso anterior
    if: failure()
    run: echo "Algo salió mal"
```

---

## Ejercicios

**9.1** — Crea un workflow `.github/workflows/ci.yml` en un repositorio Node.js que se dispare en cada push a `main` y en cada Pull Request. El workflow debe: instalar dependencias con `npm ci`, ejecutar `npm test` y fallar si los tests no pasan.

**9.2** — Amplía el workflow anterior para que también ejecute `npm run lint`. Los jobs de lint y test deben correr **en paralelo**. Un job de `deploy` solo debe ejecutarse si ambos pasan y el push es a `main`.

**9.3** — Añade una variable de entorno `NODE_ENV=test` al job de tests y un secret `DB_URL` que se pase al script de tests. ¿Cómo configurarías ese secret en GitHub sin que aparezca en los logs?

**9.4** — Crea un workflow que se dispare manualmente (`workflow_dispatch`) con un input `environment` (valores: `staging` o `production`). El workflow debe imprimir "Desplegando en: staging/production" según el valor elegido.

<details markdown="1">
<summary>Soluciones</summary>

---

**9.1**

Crear `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: npm

      - name: Instalar dependencias
        run: npm ci

      - name: Ejecutar tests
        run: npm test
```

Si `npm test` devuelve exit code distinto de 0, el job falla y GitHub lo marca en rojo.

---

**9.2**

```yaml
name: CI + Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "20", cache: npm }
      - run: npm ci
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "20", cache: npm }
      - run: npm ci
      - run: npm test

  deploy:
    runs-on: ubuntu-latest
    needs: [lint, test]
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    steps:
      - uses: actions/checkout@v4
      - run: echo "Desplegando a producción..."
```

`lint` y `test` corren en paralelo (sin `needs`). `deploy` espera a los dos.

---

**9.3**

```yaml
  test:
    runs-on: ubuntu-latest
    env:
      NODE_ENV: test
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "20", cache: npm }
      - run: npm ci
      - name: Ejecutar tests
        env:
          DB_URL: ${{ secrets.DB_URL }}
        run: npm test
```

Para configurar el secret:
1. GitHub → Repositorio → Settings → Secrets and variables → Actions
2. **New repository secret** → Name: `DB_URL`, Value: la cadena de conexión
3. El valor nunca aparece en logs (GitHub lo enmascara con `***`)

---

**9.4**

```yaml
name: Deploy manual

on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Entorno de destino"
        required: true
        default: staging
        type: choice
        options:
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Mostrar entorno
        run: echo "Desplegando en: ${{ inputs.environment }}"

      - name: Deploy a staging
        if: inputs.environment == 'staging'
        run: echo "Conectando a servidores de staging..."

      - name: Deploy a production
        if: inputs.environment == 'production'
        run: echo "Conectando a servidores de producción..."
```

Se dispara desde: Actions → Deploy manual → Run workflow → selecciona entorno.

</details>
