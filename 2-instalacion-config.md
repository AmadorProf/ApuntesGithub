# 2. Instalación y Configuración de Git

A continuación detallamos los pasos para instalar y configurar Git en diferentes sistemas operativos, así como las configuraciones básicas necesarias para empezar a trabajar con este sistema de control de versiones.

---

## 1. Instalación de Git

Git puede instalarse en diferentes sistemas operativos. A continuación, se detallan los pasos para cada uno.

### 1.1 Instalación en Windows

**Descargar Git**:  
- Visita [git-scm.com/downloads](https://git-scm.com/downloads).  
- Haz clic en "Download for Windows".

**Ejecutar el instalador**:  
- Abre el archivo descargado (`Git-X.X.XX-64-bit.exe`).  
- Sigue las instrucciones del asistente de instalación.

**Configuración recomendada durante la instalación**:  
- Selecciona: **"Use Git from the Windows Command Prompt"**.  
- Elige: **"Checkout Windows-style, commit Unix-style line endings"**.

**Finalizar instalación**:  
- Marca **"Launch Git Bash"** para abrir la terminal de Git.

---

### 1.2 Instalación en macOS

**Opción 1: Usando Homebrew (recomendado)**

```sh
brew install git
```

**Opción 2: Descargar desde git-scm.com**  
- Visita [git-scm.com/download/mac](https://git-scm.com/download/mac).  
- Descarga el instalador y sigue los pasos.

---

### 1.3 Instalación en Linux

**Debian / Ubuntu (APT)**

```sh
sudo apt update && sudo apt install git -y
```

**Fedora (DNF)**

```sh
sudo dnf install git -y
```

**Arch Linux (Pacman)**

```sh
sudo pacman -S git
```

---

## 2. Verificación de la Instalación

Para confirmar que Git se instaló correctamente, ejecuta en la terminal:

```sh
git --version
```

**Salida esperada** (la versión puede variar):

```
git version 2.40.1
```

---

## 3. Configuración Inicial de Git

### 3.1 Configurar Usuario y Correo

Git requiere identificar tus commits con un nombre y correo electrónico:

```sh
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
```

---

### 3.2 Configurar Editor de Texto por Defecto

Puedes definir un editor alternativo a `vi`, por ejemplo VS Code o Nano:

```sh
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "nano"         # Nano
```

---

### 3.3 Aliases Útiles

Puedes crear atajos para comandos comunes:

```sh
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
```

---

## 4. Archivo `.gitconfig`

Git guarda las configuraciones en un archivo llamado `.gitconfig`.

### 4.1 Ubicación

- **Global**: `~/.gitconfig` (Linux/macOS) o `C:\Users\<usuario>\.gitconfig` (Windows)  
- **Local**: `.git/config` (dentro de un repositorio)

### 4.2 Configuración Global vs Local

| Ámbito  | Comando               | Descripción                              |
|---------|------------------------|------------------------------------------|
| Global  | `git config --global` | Afecta a todos los repositorios del usuario |
| Local   | `git config --local`  | Solo aplica al repositorio actual       |

**Ver configuraciones actuales**:

```sh
git config --list
git config --global --list
```

---

## 5. Diagrama de Configuración de Git

```mermaid
graph TD
    A[Git Config] --> B[Global (~/.gitconfig)]
    A --> C[Local (.git/config)]
    B --> D[user.name]
    B --> E[user.email]
    B --> F[core.editor]
    B --> G[aliases]
    C --> H[repo-specific settings]
```





---

## Ejercicios

**2.1** — Instala Git en tu sistema y verifica la instalación. Configura tu nombre y correo de forma global. Después muestra la configuración actual.

```sh
# Pista: git --version | git config --global ... | git config --list
```

**2.2** — Crea los siguientes aliases en tu `.gitconfig` y comprueba que funcionan:
- `lg` → `log --oneline --graph --all --decorate`
- `st` → `status -sb`
- `undo` → `reset HEAD~1 --soft`

**2.3** — ¿Cuándo usarías `git config --local` en lugar de `git config --global`? Pon un ejemplo concreto de una configuración que tendría sentido definir a nivel local.

**2.4** — Abre el fichero `~/.gitconfig` con un editor y añade manualmente (sin usar `git config`) un alias `aliases` que ejecute `config --get-regexp alias`. Úsalo para listar todos tus aliases.

<details markdown="1">
<summary>Soluciones</summary>

---

**2.1**

```sh
git --version
# git version 2.44.0

git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"

git config --list
# user.name=Tu Nombre
# user.email=tu@email.com
```

---

**2.2**

```sh
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.st "status -sb"
git config --global alias.undo "reset HEAD~1 --soft"

# Prueba:
git lg
git st
```

---

**2.3**

`--local` tiene sentido para configuraciones específicas de un proyecto, por ejemplo:
- Un email diferente si en ese repo trabajas con tu cuenta de empresa en lugar de la personal.
- Un editor diferente solo para ese proyecto.
- Un hook o configuración de merge tool específica del equipo.

```sh
cd mi-proyecto-empresa
git config --local user.email "amador@empresa.com"
```

---

**2.4**

Edita `~/.gitconfig` y añade bajo `[alias]`:
```ini
[alias]
    aliases = config --get-regexp alias
```

Luego ejecuta:
```sh
git aliases
# alias.lg log --oneline --graph --all --decorate
# alias.st status -sb
# alias.undo reset HEAD~1 --soft
# alias.aliases config --get-regexp alias
```

</details>
