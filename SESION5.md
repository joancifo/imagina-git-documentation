# Sesión 5: Git avanzado - Repaso integral, internals, optimización y técnicas expertas

## Índice de la sesión

1. [Resumen de conceptos fundamentales](#1-resumen-de-conceptos-fundamentales) (30 minutos)
   - 1.1. Fundamentos de Git vs SVN
   - 1.2. Flujo básico de trabajo
   - 1.3. Ramas y fusiones
   - 1.4. Trabajo colaborativo con GitLab

2. [Repaso de técnicas avanzadas](#2-repaso-de-técnicas-avanzadas) (25 minutos)
   - 2.1. Reescritura de historial
   - 2.2. Recuperación de cambios
   - 2.3. Gestión de cambios temporales (stash)
   - 2.4. Submódulos (resumen)

3. [Técnicas avanzadas de Git](#3-técnicas-avanzadas-de-git) (45 minutos)
   - 3.1. Cherry-pick: Aplicar commits selectivos
   - 3.2. Git bisect: Encontrar commits problemáticos
   - 3.3. Git worktree: Múltiples working directories
   - 3.4. Git hooks: Automatización de tareas
   - 3.5. Git rerere: Reutilizar resoluciones de conflictos
   - 3.6. Git blame y annotate: Rastrear autoría
   - 3.7. Configuraciones avanzadas y aliases
   - 3.8. Git attributes y filtros personalizados
   - 3.9. Git LFS: Archivos grandes
   - 3.10. Otras herramientas avanzadas

4. [Git internals: Entendiendo cómo funciona Git por dentro](#4-git-internals-entendiendo-cómo-funciona-git-por-dentro) (40 minutos)
   - 4.1. Estructura del directorio .git
   - 4.2. Objetos de Git: blobs, trees, commits
   - 4.3. Referencias (refs) y HEAD
   - 4.4. Packfiles y compresión
   - 4.5. Comandos plumbing vs porcelain

5. [Git filter-branch y filter-repo: Limpieza masiva de historial](#5-git-filter-branch-y-filter-repo-limpieza-masiva-de-historial) (35 minutos)
   - 2.1. Git filter-branch: Reescribir historial completo
   - 2.2. Git filter-repo: Herramienta moderna y segura
   - 2.3. Casos de uso: Eliminar archivos sensibles, cambiar estructura

6. [Git submódulos: Gestión de dependencias externas](#6-git-submódulos-gestión-de-dependencias-externas) (40 minutos)
   - 3.1. Concepto y casos de uso
   - 3.2. Añadir y configurar submódulos
   - 3.3. Trabajar con submódulos en el día a día
   - 3.4. Actualizar y sincronizar submódulos
   - 3.5. Troubleshooting y problemas comunes
   - 3.6. Mejores prácticas y cuándo usar submódulos

7. [Git subtree: Alternativa avanzada a submódulos](#7-git-subtree-alternativa-avanzada-a-submódulos) (30 minutos)
   - 4.1. Concepto y diferencias con submódulos
   - 4.2. Operaciones: add, pull, push, split
   - 4.3. Cuándo usar subtree vs submódulos

8. [Git workflows avanzados: Estrategias de organización](#8-git-workflows-avanzados-estrategias-de-organización) (35 minutos)
   - 5.1. Git Flow: Modelo de ramas completo
   - 5.2. GitHub Flow: Flujo simplificado
   - 5.3. GitLab Flow: Flujo con environments
   - 5.4. Trunk-based development

9. [VSCode y Git Graph: Visualización avanzada del historial](#9-vscode-y-git-graph-visualización-avanzada-del-historial) (30 minutos)
   - 6.1. Instalación y configuración de Git Graph
   - 6.2. Navegación visual del historial
   - 6.3. Operaciones avanzadas desde Git Graph
   - 6.4. Integración con GitLab desde VSCode
   - 6.5. GitLens: Funcionalidades adicionales

10. [Optimización y mantenimiento de repositorios](#10-optimización-y-mantenimiento-de-repositorios) (25 minutos)
   - 7.1. Git gc: Garbage collection
   - 7.2. Git maintenance: Tareas automáticas
   - 7.3. Optimización de repositorios grandes
   - 7.4. Shallow clones y partial clones

11. [Ejercicios prácticos con GitLab local](#11-ejercicios-prácticos-con-gitlab-local) (180 minutos)
   - 8.1. ⭐ Ejercicio principal: Reset, ramas, conflictos y recuperación (60 minutos)
   - 8.2. Ejercicio 2: Configurar GitLab local y explorar Git internals (25 minutos)
   - 8.3. Ejercicio 3: Limpiar historial y trabajar con filter-repo (30 minutos)
   - 8.4. Ejercicio 4: Implementar Git Flow completo en GitLab (35 minutos)
   - 8.5. Ejercicio 5: Optimizar repositorio y usar VSCode Git Graph (30 minutos)

12. [Técnicas expertas adicionales](#12-técnicas-expertas-adicionales) (20 minutos)
   - 9.1. Múltiples remotos y sincronización
   - 9.2. Git patches y aplicación manual
   - 9.3. Git protocol y transfer protocols
   - 9.4. Hooks del servidor (pre-receive, update, post-receive)

**Duración total estimada: 8 horas 10 minutos**

---

## 1. Resumen de conceptos fundamentales

### 1.1. Fundamentos de Git vs SVN

**Conceptos clave:**

- **Git (distribuido):** Cada desarrollador tiene una copia completa del repositorio con todo el historial. Permite trabajo offline y sincronización posterior.
- **SVN (centralizado):** Un único servidor central contiene el repositorio. Los desarrolladores trabajan con copias locales pero necesitan conexión para muchas operaciones.

**Diferencias principales:**

| Aspecto | Git | SVN |
|---------|-----|-----|
| Arquitectura | Distribuida | Centralizada |
| Operaciones | Locales y rápidas | Requieren servidor |
| Ramas | Instantáneas, baratas | Copias de directorios |
| Historial | Snapshots completos (SHA-1) | Deltas entre versiones |
| Commits | Locales primero | Directos al servidor |

**Comandos esenciales de configuración:**

```bash
# Configuración inicial (una vez por máquina)
git config --global user.name "Tu Nombre"
git config --global user.email "tu-email@ejemplo.com"
git config --global core.editor vim

# Ver configuración
git config --list
git config --global --list
```

### 1.2. Flujo básico de trabajo

**Ciclo de vida de un cambio:**

1. **Modificación:** Editas archivos en tu directorio de trabajo
2. **Staging:** `git add <archivo>` prepara cambios para commit
3. **Commit:** `git commit -m "mensaje"` crea una versión en el historial local
4. **Push:** `git push` envía commits al repositorio remoto (GitLab)

**Estados de archivos en Git:**

```
┌─────────────┐
│ Untracked   │ → git add → ┌─────────────┐
│ (no rastreado)│            │ Staged      │ → git commit → ┌─────────────┐
└─────────────┘              │ (preparado) │                │ Committed   │
                             └─────────────┘                │ (confirmado)│
                                                             └─────────────┘
```

**Comandos de estado y exploración:**

```bash
# Ver estado del repositorio
git status                    # Estado general
git status -s                 # Versión corta

# Ver diferencias
git diff                      # Cambios no staged
git diff --staged             # Cambios staged
git diff HEAD                 # Todos los cambios vs último commit

# Ver historial
git log                       # Historial completo
git log --oneline             # Historial compacto
git log --graph --oneline --all  # Historial con gráfico de ramas
```

**Creación y gestión de repositorios:**

```bash
# Inicializar repositorio local
git init                      # Crea .git/ en el directorio actual
git init <directorio>         # Crea directorio e inicializa repo

# Clonar repositorio remoto
git clone <url>               # Clona y crea directorio
git clone <url> <nombre>      # Clona en directorio con nombre específico

# Trabajar con remotos
git remote add origin <url>   # Añadir remoto llamado 'origin'
git remote -v                 # Ver remotos configurados
git remote show origin        # Información detallada del remoto
```

### 1.3. Ramas y fusiones

**Concepto de ramas:**

Las ramas en Git son punteros móviles a commits. Crear una rama es instantáneo y barato porque solo crea un nuevo puntero.

**Operaciones con ramas:**

```bash
# Crear y cambiar de rama
git branch <nombre>           # Crear rama (no cambia a ella)
git checkout <nombre>         # Cambiar a rama existente
git checkout -b <nombre>      # Crear y cambiar en un paso
git switch <nombre>           # Cambiar a rama (comando moderno)
git switch -c <nombre>        # Crear y cambiar (comando moderno)

# Listar y gestionar ramas
git branch                    # Listar ramas locales
git branch -a                 # Listar todas (locales y remotas)
git branch -r                 # Listar solo remotas
git branch -d <nombre>        # Eliminar rama local (si está fusionada)
git branch -D <nombre>        # Forzar eliminación de rama local

# Sincronizar ramas remotas
git fetch origin              # Descargar cambios sin fusionar
git pull origin <rama>        # Fetch + merge automático
git push origin <rama>        # Enviar rama al remoto
git push -u origin <rama>     # Push + establecer upstream
```

**Estrategias de fusión:**

```bash
# Merge (fusionar)
git merge <rama>              # Fusionar <rama> en la rama actual
git merge --no-ff <rama>      # Forzar commit de merge (evita fast-forward)
git merge --squash <rama>     # Aplastar todos los commits en uno solo
git merge --abort             # Cancelar merge en progreso

# Rebase (reorganizar)
git rebase <rama>             # Reaplicar commits actuales sobre <rama>
git rebase -i HEAD~<n>        # Rebase interactivo de últimos n commits
git rebase --abort            # Cancelar rebase en progreso
git rebase --continue         # Continuar rebase tras resolver conflictos
```

**Las tres estrategias principales de integración:**

Git ofrece tres formas principales de integrar cambios de una rama a otra. Cada una tiene sus ventajas y casos de uso específicos:

#### 1. Fast-forward merge (por defecto)

**Cuándo ocurre:** Cuando la rama destino (ej. `main`) no tiene commits nuevos desde que se creó la rama feature.

**Cómo funciona:** Git simplemente mueve el puntero de `main` hacia adelante hasta el último commit de la feature, sin crear un commit de merge.

**Visualización:**

```
Antes:
main:    A---B
              \
feature:       C---D---E

Después (fast-forward):
main:    A---B---C---D---E
```

**Comando:**

```bash
git checkout main
git merge feature          # Fast-forward automático si es posible
```

**Ventajas:**
- Historial lineal y limpio
- No crea commits adicionales
- Simple y directo

**Desventajas:**
- Pierdes la información de que fue una rama separada
- No queda claro en el historial que hubo trabajo en paralelo

**Forzar fast-forward (si no es posible por defecto):**

```bash
# Si hay commits nuevos en main, fast-forward no es posible
# Pero puedes hacer rebase primero y luego merge
git checkout feature
git rebase main
git checkout main
git merge feature          # Ahora sí será fast-forward
```

#### 2. Merge commit (merge normal con --no-ff)

**Cuándo usar:** Cuando quieres preservar la historia de que hubo trabajo en paralelo en una rama separada.

**Cómo funciona:** Git crea un commit de merge que combina ambas historias, manteniendo la estructura de ramas en el historial.

**Visualización:**

```
Antes:
main:    A---B---F
              \
feature:       C---D---E

Después (merge commit):
main:    A---B---F-------M
              \         /
feature:       C---D---E
```

**Comando:**

```bash
git checkout main
git merge --no-ff feature   # Fuerza creación de commit de merge
```

**O configurar por defecto:**

```bash
# Configurar para que siempre cree merge commits
git config merge.ff false
git merge feature            # Ahora siempre crea merge commit
```

**Ventajas:**
- Preserva la historia completa de ramas
- Fácil ver qué commits pertenecen a qué feature
- Útil para proyectos con muchas ramas paralelas

**Desventajas:**
- Historial más complejo con muchos commits de merge
- Puede ser difícil seguir el flujo lineal

**Ejemplo práctico:**

```bash
# Situación: main tiene commits nuevos, feature también
git checkout main
git pull origin main         # Actualizar main

# Opción A: Merge normal (crea merge commit)
git merge --no-ff feature

# Resultado: Historial muestra claramente la rama feature
```

#### 3. Rebase (reorganizar historial)

**Cuándo usar:** Cuando quieres un historial completamente lineal, como si el trabajo se hubiera hecho directamente en `main`.

**Cómo funciona:** Git toma los commits de la feature y los "re-aplica" uno por uno sobre la punta de `main`, creando nuevos commits con nuevos hashes.

**Visualización:**

```
Antes:
main:    A---B---F
              \
feature:       C---D---E

Después (rebase):
main:    A---B---F---C'---D'---E'
              \
feature:       C---D---E (original, ya no se usa)
```

**Comando:**

```bash
# Opción 1: Rebase manual
git checkout feature
git rebase main              # Reaplicar commits sobre main
git checkout main
git merge feature            # Fast-forward (ahora es posible)

# Opción 2: Configurar pull para usar rebase
git config pull.rebase true
git pull origin main         # Usa rebase en lugar de merge
```

**Ventajas:**
- Historial completamente lineal y fácil de seguir
- Facilita la lectura del historial
- Evita commits de merge innecesarios

**Desventajas:**
- Reescribe el historial (cambia hashes de commits)
- Puede ser confuso si otros ya tienen la rama
- Requiere force push si la rama ya está en remoto

**⚠️ Importante:** Solo hacer rebase de commits que aún no se han compartido (no hacer push). Si ya hiciste push, necesitarás `--force-with-lease`.

**Ejemplo práctico:**

```bash
# Situación: Trabajaste en feature, main avanzó
git checkout feature
git rebase main              # Reaplicar tus commits sobre main actualizado

# Si hay conflictos durante rebase:
# 1. Resolver conflictos en cada commit
# 2. git add <archivos-resueltos>
# 3. git rebase --continue

# Una vez completado el rebase:
git checkout main
git merge feature            # Fast-forward (historial lineal)
```

#### Comparación de las tres estrategias

| Aspecto | Fast-forward | Merge commit | Rebase |
|---------|--------------|--------------|--------|
| **Historial** | Lineal | Ramificado | Lineal |
| **Preserva ramas** | No | Sí | No |
| **Commits adicionales** | No | Sí (merge commit) | No |
| **Reescribe historial** | No | No | Sí |
| **Complejidad** | Baja | Media | Media-Alta |
| **Ideal para** | Features simples | Proyectos grandes | Historial limpio |

#### Configuración recomendada

**Para proyectos pequeños/medianos (historial limpio):**

```bash
# Usar rebase por defecto en pull
git config pull.rebase true

# O usar merge con fast-forward cuando sea posible
git config merge.ff only     # Solo permite fast-forward, falla si no es posible
```

**Para proyectos grandes (preservar historia):**

```bash
# Siempre crear merge commits
git config merge.ff false

# O permitir fast-forward pero preferir merge commits
# (comportamiento por defecto de Git)
```

**Configuración híbrida (recomendada):**

```bash
# Pull usa rebase (mantiene historial local limpio)
git config pull.rebase true

# Merge manual siempre crea merge commit (preserva historia de features)
git config merge.ff false

# Al hacer pull: rebase (historial limpio)
# Al hacer merge manual: merge commit (historia preservada)
```

#### Ejemplo completo: Comparando las tres opciones

```bash
# Situación inicial
git checkout -b feature-login
# ... hacer 3 commits en feature-login ...
# Mientras tanto, main tiene 2 commits nuevos

# OPCIÓN 1: Fast-forward (solo si main no avanzó)
git checkout main
git merge feature-login      # Fast-forward si es posible

# OPCIÓN 2: Merge commit (preserva historia)
git checkout main
git merge --no-ff feature-login
# Crea: A---B---F-------M
#              \       /
#               C---D---E

# OPCIÓN 3: Rebase (historial lineal)
git checkout feature-login
git rebase main              # Reaplica C, D, E sobre F
git checkout main
git merge feature-login      # Fast-forward
# Resultado: A---B---F---C'---D'---E' (lineal)
```

#### Interfaz gráfica de merge (VSCode/IDE)

Cuando usas una interfaz gráfica (como VSCode, GitKraken, o SourceTree) para hacer merge, normalmente verás un diálogo con opciones que corresponden a las estrategias que acabamos de explicar. Aquí te mostramos cómo interpretar estas opciones:

**Diálogo típico de merge:**

```
¿Estás seguro de que quieres fusionar la rama feature/buscador 
en develop (la rama actual)?

☑ Create a new commit even if fast-forward is possible
☐ Squash Commits
☐ No Commit

[Yes, merge]  [Cancel]
```

**Explicación de cada opción:**

1. **"Create a new commit even if fast-forward is possible"** (☑ marcada)
   - **Equivale a:** `git merge --no-ff feature/buscador`
   - **Qué hace:** Siempre crea un commit de merge, incluso si Git podría hacer un fast-forward
   - **Cuándo usar:** Cuando quieres preservar la historia de que hubo una rama separada
   - **Resultado:** Historial ramificado con commit de merge explícito
   - **Recomendado para:** Proyectos donde quieres ver claramente qué commits pertenecen a cada feature

2. **"Squash Commits"** (☐ sin marcar)
   - **Equivale a:** `git merge --squash feature/buscador`
   - **Qué hace:** Combina todos los commits de la rama feature en un solo commit nuevo
   - **Cuándo usar:** Cuando la feature tiene muchos commits pequeños y quieres un historial más limpio
   - **Resultado:** Un solo commit que contiene todos los cambios de la feature
   - **Ejemplo:**
     ```
     Antes: feature tiene 10 commits pequeños
     Después: Un solo commit "Merge feature/buscador (squashed)"
     ```
   - **⚠️ Nota:** Con squash, se pierde el historial detallado de la feature branch

3. **"No Commit"** (☐ sin marcar)
   - **Equivale a:** `git merge --no-commit feature/buscador`
   - **Qué hace:** Realiza el merge pero no crea el commit automáticamente
   - **Cuándo usar:** Cuando quieres revisar o modificar los cambios antes de hacer commit
   - **Resultado:** Cambios en staging area, listos para revisar y luego hacer `git commit` manualmente
   - **Útil para:** 
     - Revisar los cambios antes de confirmar
     - Añadir más cambios antes del commit
     - Modificar el mensaje de commit

**Combinaciones comunes:**

**Opción 1: Merge normal con commit (recomendado para la mayoría de casos)**
```
☑ Create a new commit even if fast-forward is possible
☐ Squash Commits
☐ No Commit
```
- Preserva la historia completa
- Crea commit de merge explícito
- Útil para proyectos colaborativos

**Opción 2: Squash para historial limpio**
```
☐ Create a new commit even if fast-forward is possible
☑ Squash Commits
☐ No Commit
```
- Combina todos los commits en uno
- Historial más simple
- Útil cuando la feature tiene muchos commits pequeños

**Opción 3: Revisar antes de commitear**
```
☑ Create a new commit even if fast-forward is possible
☐ Squash Commits
☑ No Commit
```
- Merge sin commit automático
- Permite revisar cambios antes de confirmar
- Útil para merges complejos que requieren revisión

**Equivalencia con comandos de terminal:**

| Interfaz gráfica | Comando terminal equivalente |
|------------------|------------------------------|
| ☑ Create new commit, ☐ Squash, ☐ No Commit | `git merge --no-ff feature/buscador` |
| ☐ Create new commit, ☑ Squash, ☐ No Commit | `git merge --squash feature/buscador` |
| ☑ Create new commit, ☐ Squash, ☑ No Commit | `git merge --no-ff --no-commit feature/buscador` |
| ☐ Create new commit, ☐ Squash, ☐ No Commit | `git merge feature/buscador` (fast-forward si es posible) |

**Recomendación:**

Para la mayoría de casos en proyectos colaborativos, usa la **Opción 1** (Create a new commit marcado, los otros sin marcar). Esto:
- Preserva la historia completa del proyecto
- Facilita el seguimiento de qué commits pertenecen a qué feature
- Permite revertir el merge completo si es necesario
- Es el comportamiento más seguro y transparente

**Resolución de conflictos:**

Cuando Git no puede fusionar automáticamente, marca los conflictos en los archivos:

```
<<<<<<< HEAD
Código de la rama actual (main)
=======
Código de la rama que se fusiona (feature)
>>>>>>> feature
```

**Proceso de resolución:**

1. Abrir archivo con conflictos
2. Editar manualmente, eliminando marcadores y dejando el código correcto
3. `git add <archivo-resuelto>`
4. `git commit` (o `git rebase --continue` si estás en rebase)

### 1.4. Trabajo colaborativo con GitLab

**Flujo colaborativo típico:**

```
1. git clone <url-gitlab>          # Clonar proyecto
2. git checkout -b feature-xyz     # Crear rama de trabajo
3. # ... hacer cambios ...
4. git add .
5. git commit -m "Descripción"
6. git push -u origin feature-xyz  # Subir rama
7. # Crear Merge Request en GitLab
8. # Revisión y aprobación
9. # Merge a main/master
10. git checkout main
11. git pull origin main           # Actualizar local
12. git branch -d feature-xyz      # Eliminar rama local
```

**Comandos de sincronización:**

```bash
# Obtener cambios del remoto
git fetch origin                  # Descargar sin fusionar
git fetch --all                   # Descargar de todos los remotos
git pull origin main              # Fetch + merge automático

# Enviar cambios al remoto
git push origin main              # Enviar commits a rama main
git push --force origin main      # Forzar push (¡cuidado! reescribe remoto)
git push --force-with-lease       # Forzar solo si remoto no cambió (más seguro)

# Trabajar con tags
git tag v1.0.0                    # Crear tag ligero
git tag -a v1.0.0 -m "Release"    # Crear tag anotado
git push origin v1.0.0            # Enviar tag al remoto
git push --tags                   # Enviar todos los tags
```

```bash
# Ver contenido de un commit
git cat-file -p HEAD
# O de un commit específico
git cat-file -p <hash>

# Salida típica:
# tree abc123...
# parent def456...
# author Juan Pérez <juan@example.com> 1234567890 +0100
# committer Juan Pérez <juan@example.com> 1234567890 +0100
#
# Mensaje del commit
```

**Ver estructura completa:**

```bash
# Ver commit actual
git cat-file -p HEAD

# Ver el tree del commit
git cat-file -p HEAD^{tree}

# Ver un blob específico del tree
git cat-file -p HEAD^{tree}:README.md
```

#### Annotated Tags

Los tags anotados son objetos especiales que apuntan a commits.

```bash
# Crear tag anotado
git tag -a v1.0 -m "Versión 1.0"

# Ver contenido del tag
git cat-file -p v1.0

# Salida:
# object <hash-del-commit>
# type commit
# tag v1.0
# tagger Juan Pérez <juan@example.com> 1234567890 +0100
#
# Versión 1.0
```

### 1.3. Referencias (refs) y HEAD

Las referencias son punteros a commits. HEAD es una referencia especial que apunta a la rama actual.

**Tipos de referencias:**

```bash
# Ver todas las referencias
git show-ref

# Ver ramas (refs/heads/)
ls .git/refs/heads/
cat .git/refs/heads/main
# Contiene: hash del commit más reciente

# Ver tags (refs/tags/)
ls .git/refs/tags/
cat .git/refs/tags/v1.0

# Ver remotos (refs/remotes/)
ls .git/refs/remotes/origin/

# Ver HEAD
cat .git/HEAD
# Contiene: ref: refs/heads/main
```

**HEAD y referencias simbólicas:**

```bash
# HEAD puede ser:
# 1. Referencia simbólica (normal)
cat .git/HEAD
# ref: refs/heads/main

# 2. Referencia directa (detached HEAD)
git checkout <hash>
cat .git/HEAD
# <hash-directo>

# Ver a qué commit apunta HEAD
git rev-parse HEAD
```

**Operaciones con referencias:**

```bash
# Crear referencia manualmente
echo <hash> > .git/refs/heads/nueva-rama

# Actualizar referencia
git update-ref refs/heads/main <nuevo-hash>

# Eliminar referencia
git update-ref -d refs/heads/rama-vieja
```

### 1.4. Packfiles y compresión

Para optimizar el almacenamiento, Git empaqueta objetos en packfiles.

```bash
# Ver objetos sueltos
find .git/objects -type f | grep -v pack

# Ver packfiles
ls .git/objects/pack/

# Verificar packfiles
git verify-pack -v .git/objects/pack/pack-*.idx

# Forzar empaquetado
git gc --aggressive
```

**Cómo funcionan los packfiles:**

- Git agrupa objetos similares
- Usa delta compression (solo almacena diferencias)
- Reduce significativamente el tamaño del repositorio
- Se crean automáticamente con `git gc`

### 1.5. Comandos plumbing vs porcelain

Git tiene dos categorías de comandos:

**Porcelain (comandos de alto nivel):**
- `git add`, `git commit`, `git status`, `git log`
- Diseñados para uso humano
- Más amigables y con mejor output

**Plumbing (comandos de bajo nivel):**
- `git cat-file`, `git hash-object`, `git update-ref`
- Herramientas para construir otros comandos
- Output más crudo, diseñado para scripts

**Ejemplos de comandos plumbing:**

```bash
# hash-object: Calcular hash de contenido
echo "test" | git hash-object --stdin

# cat-file: Ver contenido de objetos
git cat-file -p <hash>
git cat-file -t <hash>  # Tipo
git cat-file -s <hash>  # Tamaño

# update-ref: Actualizar referencias
git update-ref refs/heads/main <hash>

# rev-parse: Parsear referencias
git rev-parse HEAD
git rev-parse --abbrev-ref HEAD

# ls-tree: Listar contenido de tree
git ls-tree HEAD
git ls-tree -r HEAD  # Recursivo

# write-tree: Crear tree desde index
git write-tree

# commit-tree: Crear commit desde tree
git commit-tree <tree-hash> -p <parent-hash> -m "mensaje"
```

---

## 2. Git filter-branch y filter-repo: Limpieza masiva de historial

### 2.1. Git filter-branch: Reescribir historial completo

`git filter-branch` permite reescribir todo el historial del repositorio. Es potente pero lento y puede ser peligroso.

**⚠️ Advertencia:** `git filter-branch` está deprecado en favor de `git filter-repo`, pero aún se usa en algunos casos.

**Eliminar archivo de todo el historial:**

```bash
# Eliminar archivo sensible de todo el historial
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch archivo-secreto.txt" \
  --prune-empty --tag-name-filter cat -- --all

# Esto reescribe TODO el historial
# Cada commit que contenía el archivo será reescrito
```

**Cambiar email de autor en todo el historial:**

```bash
git filter-branch --force --env-filter '
OLD_EMAIL="viejo@email.com"
CORRECT_NAME="Nombre Correcto"
CORRECT_EMAIL="nuevo@email.com"

if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

**Mover directorio en todo el historial:**

```bash
git filter-branch --force --tree-filter \
  "mkdir -p nuevo/dir && git mv viejo/dir/* nuevo/dir/ && rmdir viejo/dir || true" \
  --prune-empty --tag-name-filter cat -- --all
```

**Después de filter-branch:**

```bash
# Limpiar referencias de backup
rm -rf .git/refs/original/

# Forzar garbage collection
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Force push (¡cuidado! reescribe todo el remoto)
git push --force --all
git push --force --tags
```

### 2.2. Git filter-repo: Herramienta moderna y segura

`git filter-repo` es la herramienta recomendada para reescribir historial. Es más rápida, segura y fácil de usar.

**Instalación:**

```bash
# Python 3.5+ requerido
pip install git-filter-repo

# O con package manager
# Ubuntu/Debian:
sudo apt install git-filter-repo
# macOS:
brew install git-filter-repo
```

**Eliminar archivo de todo el historial:**

```bash
# Eliminar archivo específico
git filter-repo --path archivo-secreto.txt --invert-paths

# Eliminar directorio completo
git filter-repo --path directorio-secreto/ --invert-paths

# Eliminar múltiples archivos
git filter-repo --path archivo1.txt --path archivo2.txt --invert-paths
```

**Cambiar email de autor:**

```bash
# Cambiar email en todo el historial
git filter-repo --email-callback '
return email.replace(b"viejo@email.com", b"nuevo@email.com")
'

# Cambiar nombre y email
git filter-repo --name-callback 'return b"Nuevo Nombre"' \
  --email-callback 'return b"nuevo@email.com"'
```

**Mover/renombrar archivos:**

```bash
# Mover archivo en todo el historial
git filter-repo --path-rename viejo/path:new/path

# Renombrar archivo
git filter-repo --path-rename archivo-viejo.txt:archivo-nuevo.txt
```

**Filtrar por rango de fechas:**

```bash
# Eliminar commits antes de una fecha
git filter-repo --invert-paths --commit-callback '
if commit.committer_date < b"2020-01-01":
    commit.skip()
'
```

**Ventajas de filter-repo sobre filter-branch:**

- ✅ Más rápido (10-50x)
- ✅ Más seguro (mejor manejo de edge cases)
- ✅ Mejor documentación
- ✅ No deja backups que confundan
- ✅ Mejor manejo de tags y branches
- ✅ Soporte para callbacks Python

### 2.3. Casos de uso: Eliminar archivos sensibles, cambiar estructura

**Caso 1: Eliminar credenciales expuestas**

```bash
# Situación: Se commitearon credenciales por error
# Necesitas eliminarlas de TODO el historial

# 1. Identificar el archivo
git log --all --full-history -- config/secrets.json

# 2. Eliminar del historial
git filter-repo --path config/secrets.json --invert-paths

# 3. Rotar las credenciales (importante!)
# Las credenciales siguen en el historial de otros que clonaron antes

# 4. Force push
git push --force --all
git push --force --tags

# 5. Notificar a todo el equipo
# Todos deben re-clonar o hacer:
git fetch origin
git reset --hard origin/main
```

**Caso 2: Reorganizar estructura de directorios**

```bash
# Situación: Quieres mover todo de src/ a lib/

git filter-repo --path-rename src/:lib/

# Verificar cambios
git log --all --oneline

# Force push
git push --force --all
```

**Caso 3: Separar subdirectorio en repositorio independiente**

```bash
# Situación: Tienes un monorepo y quieres extraer un subdirectorio

# Crear nuevo repositorio solo con ese directorio
git filter-repo --path subdirectorio/ --to-subdirectory-filter subdirectorio/

# Ahora el repositorio solo contiene ese subdirectorio
# Puedes crear un nuevo repo y hacer push allí
```

---

## 3. Git submódulos: Gestión de dependencias externas

### 3.1. Concepto y casos de uso

**¿Qué son los submódulos?**

Los submódulos de Git permiten incluir un repositorio Git dentro de otro como un subdirectorio. El submódulo mantiene su propio historial independiente y apunta a un commit específico del repositorio externo.

**Estructura:**

```
proyecto-principal/
├── .git/
├── .gitmodules          # Configuración de submódulos
├── src/
├── libs/
│   └── libreria-externa/  # Submódulo (es un repo Git completo)
│       ├── .git/
│       └── archivos...
└── README.md
```

**Casos de uso ideales:**

1. **Librerías compartidas entre proyectos:**
   - Tienes una librería común usada por múltiples proyectos
   - Quieres mantener versiones específicas en cada proyecto
   - La librería tiene su propio ciclo de desarrollo

2. **Dependencias de terceros versionadas:**
   - Incluir código de terceros que quieres versionar
   - Mantener control sobre qué versión usas
   - Poder hacer cambios locales si es necesario

3. **Componentes modulares:**
   - Proyectos grandes divididos en componentes independientes
   - Cada componente tiene su propio repositorio
   - El proyecto principal los integra como submódulos

4. **Documentación o assets compartidos:**
   - Documentación mantenida en repositorio separado
   - Assets (imágenes, videos) en repositorio grande
   - Quieres mantener el repo principal ligero

**Ejemplo real:**

```bash
# Proyecto web que usa una librería de componentes UI
proyecto-web/
├── src/
├── libs/
│   └── ui-components/  # Submódulo: librería de componentes
│       └── (repo Git independiente)
└── docs/              # Submódulo: documentación
    └── (repo Git independiente)
```

### 3.2. Añadir y configurar submódulos

**Añadir un submódulo:**

```bash
# Sintaxis básica
git submodule add <url-repositorio> <ruta-destino>

# Ejemplo: Añadir librería externa
git submodule add https://gitlab.local/empresa/ui-components.git libs/ui-components

# Esto:
# 1. Clona el repositorio externo en libs/ui-components
# 2. Crea/actualiza el archivo .gitmodules
# 3. Añade el submódulo al staging area
```

**Archivo .gitmodules:**

Después de añadir un submódulo, Git crea/actualiza `.gitmodules`:

```ini
[submodule "libs/ui-components"]
    path = libs/ui-components
    url = https://gitlab.local/empresa/ui-components.git
    branch = main
```

**Configuración avanzada:**

```bash
# Añadir submódulo apuntando a rama específica
git submodule add -b develop \
  https://gitlab.local/empresa/lib.git libs/lib

# Añadir submódulo con nombre personalizado
git submodule add \
  https://gitlab.local/empresa/lib.git libs/mi-libreria

# Ver configuración de submódulos
cat .gitmodules
git config --file=.gitmodules --list
```

**Commit del submódulo:**

```bash
# Después de añadir, necesitas hacer commit
git add .gitmodules libs/ui-components
git commit -m "feat: añadir librería UI como submódulo"

# El commit guarda la referencia al commit específico del submódulo
```

**Ver estado de submódulos:**

```bash
# Ver todos los submódulos
git submodule status

# Salida típica:
#  abc1234 libs/ui-components (v1.2.3-5-gabc1234)
#  def5678 libs/docs (heads/main)

# El hash muestra el commit al que apunta el submódulo
# El prefijo indica si está actualizado:
#   - Sin prefijo: Submódulo actualizado
#   + Con +: Submódulo tiene commits nuevos disponibles
#   U Con U: Submódulo tiene conflictos de merge
```

### 3.3. Trabajar con submódulos en el día a día

**Clonar repositorio con submódulos:**

```bash
# Opción 1: Clonar y luego inicializar (2 pasos)
git clone https://gitlab.local/usuario/proyecto.git
cd proyecto
git submodule init      # Inicializar submódulos (lee .gitmodules)
git submodule update    # Descargar contenido de submódulos

# Opción 2: Todo en un paso (recomendado)
git clone --recurse-submodules https://gitlab.local/usuario/proyecto.git

# Opción 3: Si olvidaste --recurse-submodules
git clone https://gitlab.local/usuario/proyecto.git
cd proyecto
git submodule update --init --recursive
```

**Trabajar dentro de un submódulo:**

```bash
# Entrar al directorio del submódulo
cd libs/ui-components

# Ahora estás en un repositorio Git normal
git status
git log
git branch

# Hacer cambios en el submódulo
echo "nueva funcionalidad" > nuevo-componente.js
git add nuevo-componente.js
git commit -m "feat: añadir nuevo componente"

# Push del submódulo
git push origin main

# Volver al proyecto principal
cd ../..

# Actualizar referencia en proyecto principal
git add libs/ui-components
git commit -m "chore: actualizar submódulo ui-components"
git push origin main
```

**Actualizar submódulo a última versión:**

```bash
# Opción 1: Actualizar a último commit de la rama
cd libs/ui-components
git pull origin main
cd ../..
git add libs/ui-components
git commit -m "chore: actualizar submódulo"

# Opción 2: Desde el proyecto principal
git submodule update --remote libs/ui-components
# Esto hace fetch y checkout del último commit de la rama configurada

# Actualizar todos los submódulos
git submodule update --remote

# Commit los cambios
git add .gitmodules libs/ui-components
git commit -m "chore: actualizar submódulos"
```

**Cambiar rama de un submódulo:**

```bash
# Cambiar a otra rama del submódulo
cd libs/ui-components
git checkout develop
cd ../..

# Actualizar .gitmodules para que use develop por defecto
git config -f .gitmodules submodule.libs/ui-components.branch develop
git add .gitmodules
git commit -m "chore: cambiar rama de submódulo a develop"

# Ahora update --remote usará develop
git submodule update --remote libs/ui-components
```

### 3.4. Actualizar y sincronizar submódulos

**Actualizar submódulos después de pull:**

```bash
# Situación: Alguien actualizó la referencia del submódulo
git pull origin main

# Ver estado
git submodule status
# Verás que el submódulo está desactualizado

# Actualizar submódulos
git submodule update

# O en un paso
git pull --recurse-submodules
```

**Sincronizar submódulos con remoto:**

```bash
# Sincronizar configuración de .gitmodules con .git/config
git submodule sync

# Útil cuando cambias la URL de un submódulo
# 1. Editar .gitmodules manualmente
# 2. git submodule sync
# 3. git submodule update --init
```

**Actualizar recursivo (submódulos dentro de submódulos):**

```bash
# Si un submódulo tiene sus propios submódulos
git submodule update --init --recursive

# O al clonar
git clone --recurse-submodules --recursive <url>
```

**Ejecutar comandos en todos los submódulos:**

```bash
# Ejecutar comando en cada submódulo
git submodule foreach 'git pull origin main'

# Ejemplo: Ver estado de todos
git submodule foreach 'git status'

# Ejemplo: Cambiar a rama específica en todos
git submodule foreach 'git checkout main'

# Con condición
git submodule foreach 'if [ "$name" != "docs" ]; then git pull; fi'
```

### 3.5. Troubleshooting y problemas comunes

**Problema 1: Submódulo en estado "detached HEAD"**

```bash
# Situación: Submódulo no está en ninguna rama
cd libs/ui-components
git status
# HEAD detached at abc1234

# Solución: Cambiar a rama
git checkout main
# O crear rama desde el commit actual
git checkout -b temp-branch
```

**Problema 2: Cambios no commiteados en submódulo**

```bash
# Situación: Tienes cambios sin commitear en submódulo
cd libs/ui-components
git status
# Changes not staged for commit

# Opción A: Commitear los cambios
git add .
git commit -m "mis cambios"
git push origin main

# Opción B: Descartar cambios
git checkout -- .

# Opción C: Guardar en stash
git stash
```

**Problema 3: Submódulo apunta a commit incorrecto**

```bash
# Ver a qué commit apunta
cd libs/ui-components
git rev-parse HEAD

# Cambiar a commit específico
git checkout <hash-commit-deseado>

# O cambiar a tag
git checkout v1.2.3

# Actualizar referencia en proyecto principal
cd ../..
git add libs/ui-components
git commit -m "fix: corregir versión de submódulo"
```

**Problema 4: Eliminar submódulo**

```bash
# Proceso completo para eliminar submódulo:

# 1. Eliminar entrada de .gitmodules
git config -f .gitmodules --remove-section submodule.libs/ui-components

# 2. Eliminar del índice de Git
git rm --cached libs/ui-components

# 3. Eliminar directorio .git/modules (si existe)
rm -rf .git/modules/libs/ui-components

# 4. Eliminar directorio del submódulo
rm -rf libs/ui-components

# 5. Commit
git commit -m "chore: eliminar submódulo ui-components"
```

**Problema 5: Mover submódulo a otra ubicación**

```bash
# 1. Mover el directorio
git mv libs/ui-components components/ui

# 2. Actualizar .gitmodules
git config -f .gitmodules submodule.libs/ui-components.path components/ui

# 3. Sincronizar
git submodule sync

# 4. Commit
git add .gitmodules
git commit -m "chore: mover submódulo a components/ui"
```

**Problema 6: Submódulo no se actualiza**

```bash
# Verificar configuración
git config --file=.gitmodules --get submodule.libs/ui-components.url
git config --file=.gitmodules --get submodule.libs/ui-components.branch

# Forzar actualización
git submodule update --init --force libs/ui-components

# O actualizar manualmente
cd libs/ui-components
git fetch origin
git checkout origin/main
cd ../..
git add libs/ui-components
```

### 3.6. Mejores prácticas y cuándo usar submódulos

**Mejores prácticas:**

1. **Commits atómicos:**
   ```bash
   # ✅ Bueno: Commit del submódulo y referencia juntos
   cd libs/ui-components
   git commit -m "feat: nueva funcionalidad"
   git push
   cd ../..
   git add libs/ui-components
   git commit -m "chore: actualizar ui-components con nueva funcionalidad"
   
   # ❌ Malo: Actualizar referencia sin commitear cambios del submódulo
   ```

2. **Documentar versiones:**
   ```bash
   # Usar tags en submódulos para versionar
   cd libs/ui-components
   git tag v1.2.3
   git push origin v1.2.3
   git checkout v1.2.3
   cd ../..
   git add libs/ui-components
   git commit -m "chore: fijar ui-components a v1.2.3"
   ```

3. **Configurar ramas por defecto:**
   ```bash
   # Configurar rama en .gitmodules
   git config -f .gitmodules submodule.libs/ui-components.branch main
   ```

4. **Scripts de inicialización:**
   ```bash
   # Crear script setup.sh para nuevos desarrolladores
   #!/bin/bash
   git clone --recurse-submodules <url>
   git submodule update --init --recursive
   ```

**Cuándo usar submódulos:**

✅ **Usa submódulos cuando:**
- Tienes dependencias externas que quieres versionar específicamente
- El código del submódulo se desarrolla independientemente
- Quieres mantener el repositorio principal pequeño
- Necesitas control fino sobre qué versión usas
- El submódulo es usado por múltiples proyectos
- Quieres mantener historiales completamente separados

❌ **NO uses submódulos cuando:**
- El código cambia frecuentemente y necesitas siempre la última versión
- Tu equipo no está familiarizado con Git avanzado
- El submódulo es muy pequeño (mejor copiar el código)
- Necesitas modificar el código del submódulo frecuentemente
- Quieres un workflow simple sin complejidad adicional

**Alternativas a considerar:**

1. **Package managers:** npm, pip, maven, etc. (para dependencias)
2. **Subtrees:** Si quieres fusionar el historial
3. **Monorepo:** Si todo debe estar en un solo repositorio
4. **Copiar código:** Si el código es pequeño y raramente cambia

**Ejemplo de workflow completo:**

```bash
# 1. Añadir submódulo
git submodule add https://gitlab.local/empresa/lib.git libs/lib
git commit -m "feat: añadir librería como submódulo"

# 2. Trabajar normalmente
# ... desarrollo en proyecto principal ...

# 3. Actualizar submódulo periódicamente
git submodule update --remote libs/lib
git add libs/lib
git commit -m "chore: actualizar librería a última versión"

# 4. Si necesitas modificar el submódulo
cd libs/lib
# ... hacer cambios ...
git commit -m "fix: corrección en librería"
git push origin main
cd ../..
git add libs/lib
git commit -m "chore: actualizar referencia de librería"
```

---

## 4. Git subtree: Alternativa avanzada a submódulos

### 4.1. Concepto y diferencias con submódulos

**Git subtree** permite incluir un repositorio dentro de otro como un subdirectorio, pero a diferencia de los submódulos, el código se fusiona directamente en el historial del repositorio principal.

**Comparación:**

| Aspecto | Submódulos | Subtrees |
|---------|------------|----------|
| **Historial** | Separado | Fusionado |
| **Clonado** | Requiere `--recurse-submodules` | Automático |
| **Complejidad** | Alta | Media |
| **Actualización** | `git submodule update` | `git subtree pull` |
| **Tamaño repo** | Más pequeño | Más grande |
| **Ideal para** | Dependencias externas | Código compartido interno |

### 4.2. Operaciones: add, pull, push, split

**Añadir subtree:**

```bash
# Añadir repositorio externo como subtree
git subtree add --prefix=libs/externa \
  https://gitlab.com/empresa/libexterna.git main --squash

# --squash: Combina todos los commits del subtree en uno
# Sin --squash: Mantiene todo el historial del repo externo
```

**Actualizar subtree:**

```bash
# Pull cambios del repositorio externo
git subtree pull --prefix=libs/externa \
  https://gitlab.com/empresa/libexterna.git main --squash

# Esto fusiona los cambios del repo externo en tu repositorio
```

**Push cambios al subtree:**

```bash
# Si tienes permisos, puedes hacer push de cambios al repo externo
git subtree push --prefix=libs/externa \
  https://gitlab.com/empresa/libexterna.git main

# Esto envía tus cambios locales del subtree al repositorio externo
```

**Separar subtree (split):**

```bash
# Extraer el subtree de vuelta a un repositorio independiente
git subtree split --prefix=libs/externa -b subtree-externa

# Crear nuevo repositorio y hacer push
mkdir ../libexterna-nueva
cd ../libexterna-nueva
git init
git pull ../repo-principal subtree-externa
```

**Ver historial del subtree:**

```bash
# Ver solo commits que afectan el subtree
git log --oneline --prefix=libs/externa

# Ver historial con filtro
git log --follow -- libs/externa/
```

### 4.3. Cuándo usar subtree vs submódulos

**Usa subtrees cuando:**
- El código del subtree es parte integral de tu proyecto
- Quieres que el historial esté fusionado
- No necesitas trabajar directamente en el repo externo frecuentemente
- Quieres simplificar el workflow para tu equipo

**Usa submódulos cuando:**
- El código es una dependencia externa claramente separada
- Quieres mantener el historial completamente separado
- Necesitas trabajar en el repo externo independientemente
- Quieres mantener el repositorio principal más pequeño

**Ejemplo práctico:**

```bash
# Situación: Tienes una librería compartida entre proyectos

# Opción A: Subtree (recomendado si es código interno)
git subtree add --prefix=shared-lib \
  https://gitlab.com/empresa/shared-lib.git main --squash

# El código está fusionado, fácil de usar
# Actualizar:
git subtree pull --prefix=shared-lib \
  https://gitlab.com/empresa/shared-lib.git main --squash

# Opción B: Submódulo (si es dependencia externa)
git submodule add https://gitlab.com/empresa/external-lib.git libs/external-lib

# Historial separado, más control
# Actualizar:
git submodule update --remote libs/external-lib
```

---

## 4. Git workflows avanzados: Estrategias de organización

### 5.1. Git Flow: Modelo de ramas completo

Git Flow es un modelo de branching diseñado para proyectos con releases programadas.

**Ramas principales:**

- **main/master**: Código en producción
- **develop**: Código de desarrollo (integración)
- **feature/**: Nuevas funcionalidades
- **release/**: Preparación de releases
- **hotfix/**: Correcciones urgentes en producción

**Flujo visual:**

```
main:     A---B-------------------M---N (releases)
           \                     /
develop:    C---D---E---F---G---H---I (integración)
             \     / \       /
feature:      X---Y   Z-----W (features)
                      \
release:                R---S (preparación release)
                              \
hotfix:                         H---J (correcciones urgentes)
```

**Comandos Git Flow:**

```bash
# Instalar git-flow (opcional, ayuda con comandos)
# Ubuntu/Debian:
sudo apt install git-flow
# macOS:
brew install git-flow

# Inicializar git-flow
git flow init

# Crear feature
git flow feature start nueva-funcionalidad
# Equivale a: git checkout -b feature/nueva-funcionalidad develop

# Finalizar feature
git flow feature finish nueva-funcionalidad
# Fusiona a develop y elimina la rama

# Crear release
git flow release start 1.0.0
# Equivale a: git checkout -b release/1.0.0 develop

# Finalizar release
git flow release finish 1.0.0
# Fusiona a main y develop, crea tag

# Crear hotfix
git flow hotfix start bug-critico
# Equivale a: git checkout -b hotfix/bug-critico main

# Finalizar hotfix
git flow hotfix finish bug-critico
# Fusiona a main y develop
```

**Workflow manual (sin git-flow):**

```bash
# 1. Feature
git checkout develop
git checkout -b feature/login
# ... trabajar ...
git checkout develop
git merge --no-ff feature/login
git branch -d feature/login

# 2. Release
git checkout develop
git checkout -b release/1.0.0
# ... preparar release (versionado, docs) ...
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0
git checkout develop
git merge --no-ff release/1.0.0
git branch -d release/1.0.0

# 3. Hotfix
git checkout main
git checkout -b hotfix/critical-bug
# ... corregir bug ...
git checkout main
git merge --no-ff hotfix/critical-bug
git tag -a v1.0.1
git checkout develop
git merge --no-ff hotfix/critical-bug
git branch -d hotfix/critical-bug
```

**Ventajas:**
- ✅ Estructura clara y predecible
- ✅ Separación clara entre desarrollo y producción
- ✅ Bueno para proyectos con releases programadas

**Desventajas:**
- ❌ Complejo para proyectos pequeños
- ❌ Muchas ramas activas
- ❌ Puede ser lento para equipos ágiles

### 5.2. GitHub Flow: Flujo simplificado

GitHub Flow es un modelo más simple, ideal para deployments continuos.

**Ramas:**
- **main**: Siempre deployable
- **feature/**: Ramas de trabajo

**Flujo:**

```
main:     A---B---C---D---E (siempre deployable)
           \   \     /   /
feature:    X   Y---Z   W (features cortas)
```

**Workflow:**

```bash
# 1. Crear feature desde main
git checkout main
git pull origin main
git checkout -b feature/nueva-funcionalidad

# 2. Trabajar y hacer commits
# ... commits ...

# 3. Push y crear Pull Request
git push -u origin feature/nueva-funcionalidad
# Crear PR en GitHub/GitLab

# 4. Revisión y merge
# Después de aprobación, merge a main
# (se hace desde la interfaz web)

# 5. Deploy automático
# main se deploya automáticamente

# 6. Eliminar rama
git checkout main
git pull origin main
git branch -d feature/nueva-funcionalidad
```

**Ventajas:**
- ✅ Simple y fácil de entender
- ✅ Ideal para CI/CD
- ✅ Deployments frecuentes
- ✅ Menos overhead

**Desventajas:**
- ❌ No hay rama de desarrollo separada
- ❌ Puede ser problemático si main no está siempre estable

### 5.3. GitLab Flow: Flujo con environments

GitLab Flow combina Git Flow con environments (staging, pre-production, production).

**Ramas:**
- **main**: Desarrollo
- **staging**: Staging environment
- **production**: Production environment
- **feature/**: Features

**Flujo:**

```
main:        A---B---C---D
              \         \
staging:       E---------F
                \
production:      G
```

**Workflow:**

```bash
# 1. Feature en main
git checkout main
git checkout -b feature/nueva-funcionalidad
# ... trabajar ...
git checkout main
git merge feature/nueva-funcionalidad

# 2. Deploy a staging
git checkout staging
git merge main
# Deploy automático a staging

# 3. Testing en staging
# ... probar ...

# 4. Deploy a production
git checkout production
git merge staging
# Deploy automático a production
```

**Con release branches:**

```
main:        A---B---C---D---E
              \       \       \
release/1.0:   F-------G       \
                \               \
staging:         H---------------I
                  \
production:        J
```

### 5.4. Trunk-based development

Trunk-based development es el modelo más simple: todo el trabajo se hace directamente en main (trunk).

**Características:**
- Una sola rama principal (main/trunk)
- Features muy cortas (horas o días)
- Commits pequeños y frecuentes
- Feature flags para funcionalidades incompletas

**Workflow:**

```bash
# Todo se hace en main
git checkout main
git pull origin main

# Hacer cambios pequeños
# ... cambios ...
git add .
git commit -m "feat: añadir validación de email"

# Push frecuente
git push origin main

# Si necesitas aislar trabajo temporalmente:
git checkout -b feature/wip
# ... trabajar ...
git checkout main
git merge feature/wip
git branch -d feature/wip
```

**Ventajas:**
- ✅ Máxima simplicidad
- ✅ Integración continua real
- ✅ Menos conflictos (commits pequeños)
- ✅ Deployments muy frecuentes

**Desventajas:**
- ❌ Requiere disciplina del equipo
- ❌ Main debe estar siempre estable
- ❌ Puede ser difícil para equipos grandes

---

## 5. VSCode y Git Graph: Visualización avanzada del historial

### 6.1. Instalación y configuración de Git Graph

**Git Graph** es una extensión de VSCode que proporciona una visualización gráfica interactiva del historial de Git. Es una herramienta esencial para entender y navegar repositorios complejos.

**Instalación:**

1. Abrir VSCode
2. Ir a Extensions (Ctrl+Shift+X)
3. Buscar "Git Graph"
4. Instalar la extensión de "mhutchie"

**Configuración básica:**

```json
// settings.json de VSCode
{
  "gitGraph.maxDepthOfRepoSearch": 1000,
  "gitGraph.dateFormat": "YYYY-MM-DD HH:mm:ss",
  "gitGraph.uncommittedChanges.showStatusBar": true,
  "gitGraph.showStatusBar": true
}
```

**Abrir Git Graph:**

- **Método 1:** Click en el icono de Git Graph en la barra de estado (abajo a la izquierda)
- **Método 2:** Command Palette (Ctrl+Shift+P) → "Git Graph: View Git Graph"
- **Método 3:** Click derecho en un archivo → "Git Graph: View Git Graph (for file)"

### 6.2. Navegación visual del historial

**Interfaz de Git Graph:**

La interfaz muestra:
- **Gráfico de commits:** Visualización de ramas y commits
- **Lista de commits:** Detalles de cada commit
- **Información del commit seleccionado:** Autor, fecha, mensaje, cambios

**Navegación:**

- **Click en commit:** Selecciona y muestra detalles
- **Doble click en commit:** Abre diff del commit
- **Scroll:** Navegar por el historial
- **Zoom:** Ctrl + Scroll para acercar/alejar

**Filtros y búsqueda:**

```bash
# Desde la interfaz de Git Graph:
- Filtro por rama: Click en nombre de rama
- Filtro por autor: Usar búsqueda
- Filtro por mensaje: Buscar en mensajes de commit
- Filtro por archivo: Ver historial de archivo específico
```

**Visualización de ramas:**

- Cada rama tiene un color diferente
- Las líneas muestran la relación entre commits
- Los merge commits se muestran claramente
- Los tags aparecen como etiquetas en los commits

### 6.3. Operaciones avanzadas desde Git Graph

**Operaciones disponibles desde la interfaz:**

1. **Ver detalles de commit:**
   - Click en commit → Ver cambios, autor, fecha
   - Ver diff completo del commit
   - Ver archivos modificados

2. **Crear rama desde commit:**
   - Click derecho en commit → "Create Branch..."
   - Especificar nombre de rama
   - Automáticamente cambia a la nueva rama

3. **Checkout commit (detached HEAD):**
   - Click derecho en commit → "Checkout Commit"
   - Útil para inspeccionar estado histórico

4. **Cherry-pick:**
   - Click derecho en commit → "Cherry Pick Commit"
   - Aplica el commit a la rama actual

5. **Revert commit:**
   - Click derecho en commit → "Revert Commit"
   - Crea commit que deshace los cambios

6. **Reset a commit:**
   - Click derecho en commit → "Reset Current Branch to this Commit"
   - Opciones: Soft, Mixed, Hard

7. **Merge rama:**
   - Click derecho en rama → "Merge into Current Branch"
   - Fusiona la rama seleccionada

8. **Rebase:**
   - Click derecho en rama → "Rebase Current Branch onto..."
   - Reaplica commits sobre otra rama

9. **Crear tag:**
   - Click derecho en commit → "Create Tag..."
   - Añade tag anotado o ligero

10. **Ver historial de archivo:**
    - Click derecho en archivo en el explorador → "Git Graph: View Git Graph (for file)"
    - Muestra solo commits que afectan ese archivo

**Atajos de teclado:**

- `Ctrl+F`: Buscar en commits
- `Ctrl+R`: Refrescar gráfico
- `Esc`: Cerrar detalles de commit
- `Enter`: Abrir diff del commit seleccionado

### 6.4. Integración con GitLab desde VSCode

**Extensiones útiles:**

1. **GitLab Workflow:**
   - Ver Merge Requests
   - Crear MRs desde VSCode
   - Revisar código

2. **GitLab CI/CD:**
   - Ver pipelines
   - Ver logs de jobs
   - Trigger manual de pipelines

**Configuración:**

```json
// settings.json
{
  "gitlab.api": "https://gitlab.tu-empresa.com",
  "gitlab.token": "tu-token-personal",
  "gitlab.remote": "origin"
}
```

**Operaciones desde VSCode:**

```bash
# Command Palette (Ctrl+Shift+P):
- "GitLab: Create Merge Request"
- "GitLab: View Merge Requests"
- "GitLab: Open in GitLab"
- "GitLab: View Pipeline"
```

**Ver Merge Requests en Git Graph:**

- Git Graph puede mostrar información de MRs si está configurado
- Los commits asociados a MRs aparecen marcados
- Puedes ver el estado del MR directamente

### 6.5. GitLens: Funcionalidades adicionales

**GitLens** complementa Git Graph con funcionalidades adicionales:

**Instalación:**
- Extensions → Buscar "GitLens" → Instalar

**Funcionalidades principales:**

1. **Blame annotations:**
   - Ver autor y fecha de cada línea inline
   - Navegar al commit que modificó la línea

2. **File history:**
   - Ver historial completo de un archivo
   - Comparar versiones

3. **Commit search:**
   - Buscar commits por mensaje, autor, archivo
   - Filtros avanzados

4. **Compare references:**
   - Comparar ramas, commits, tags
   - Ver diferencias visuales

5. **Stash management:**
   - Ver y gestionar stashes desde la UI

6. **Branch comparison:**
   - Ver commits únicos de cada rama
   - Ver commits comunes

**Configuración recomendada:**

```json
{
  "gitlens.codeLens.enabled": true,
  "gitlens.currentLine.enabled": true,
  "gitlens.hovers.enabled": true,
  "gitlens.statusBar.enabled": true,
  "gitlens.views.repositories.location": "scm"
}
```

**Uso práctico:**

```bash
# Ver blame inline
# GitLens muestra automáticamente autor y fecha de cada línea

# Ver historial de archivo
# Click derecho en archivo → "Open File History"

# Comparar ramas
# Command Palette → "GitLens: Compare References"
# Seleccionar dos ramas para comparar

# Ver commits de autor específico
# GitLens sidebar → Commits → Filtrar por autor
```

**Integración con Git Graph:**

- GitLens y Git Graph trabajan juntos
- GitLens proporciona contexto adicional
- Git Graph proporciona visualización gráfica
- Usar ambos maximiza la productividad

---

## 6. Optimización y mantenimiento de repositorios

### 7.1. Git gc: Garbage collection

Git garbage collection limpia y optimiza el repositorio.

```bash
# Garbage collection normal
git gc

# Garbage collection agresiva (más lento, más optimización)
git gc --aggressive

# Garbage collection con pruning (elimina objetos no alcanzables)
git gc --prune=now

# Ver estadísticas
git gc --stat
```

**Qué hace git gc:**
- Empaqueta objetos sueltos en packfiles
- Elimina objetos no alcanzables
- Optimiza packfiles existentes
- Limpia reflog antiguo

**Cuándo ejecutar:**
- Automáticamente: Git lo hace automáticamente periódicamente
- Manualmente: Después de operaciones que crean muchos objetos (filter-branch, grandes imports)

### 7.2. Git maintenance: Tareas automáticas

Git 2.31+ incluye `git maintenance` para automatizar tareas de mantenimiento.

```bash
# Iniciar mantenimiento automático
git maintenance start

# Esto configura tareas programadas:
# - gc: Garbage collection semanal
# - commit-graph: Actualización diaria
# - prefetch: Fetch de remotos cada hora
# - loose-objects: Limpieza de objetos sueltos cada 2 horas

# Ver estado
git maintenance status

# Detener mantenimiento
git maintenance stop

# Ejecutar tareas manualmente
git maintenance run --task=gc
git maintenance run --task=commit-graph
git maintenance run --task=prefetch
git maintenance run --task=loose-objects
```

**Configurar frecuencia:**

```bash
# Cambiar frecuencia de gc (por defecto: semanal)
git config maintenance.schedule "daily"

# O configurar cron manualmente
```

### 7.3. Optimización de repositorios grandes

**Problemas comunes:**
- Repositorios muy grandes (>1GB)
- Historial muy largo
- Muchos archivos grandes

**Soluciones:**

**1. Usar Git LFS para archivos grandes:**

```bash
git lfs install
git lfs track "*.psd"
git lfs track "*.mp4"
git lfs track "*.zip"
```

**2. Shallow clone (clonar sin historial completo):**

```bash
# Clonar solo últimos commits
git clone --depth 1 <url>
git clone --depth 10 <url>

# Aumentar profundidad después
git fetch --unshallow
```

**3. Partial clone (clonar solo parte del repositorio):**

```bash
# Clonar sin blobs (solo historial)
git clone --filter=blob:none <url>

# Clonar solo archivos de un directorio
git clone --filter=tree:0 --sparse <url>
cd repo
git sparse-checkout set src/
```

**4. Limpiar historial antiguo:**

```bash
# Eliminar objetos antiguos del reflog
git reflog expire --expire=90.days.ago --all

# Garbage collection agresiva
git gc --prune=now --aggressive
```

**5. Dividir repositorio (monorepo → múltiples repos):**

```bash
# Usar filter-repo para extraer subdirectorios
git filter-repo --path subdirectorio/ --to-subdirectory-filter subdirectorio/
```

### 7.4. Shallow clones y partial clones

**Shallow clone:**

```bash
# Clonar solo último commit
git clone --depth 1 <url> repo-shallow

# Clonar últimos 10 commits
git clone --depth 10 <url>

# Ver información
cd repo-shallow
git log --oneline --all
# Solo verás los commits clonados

# Aumentar profundidad
git fetch --unshallow
# O profundidad específica
git fetch --depth=50
```

**Partial clone:**

```bash
# Clone sin blobs (solo historial y trees)
git clone --filter=blob:none <url> repo-partial

# Los blobs se descargan bajo demanda cuando los necesitas
cd repo-partial
git checkout main
# Descarga blobs necesarios automáticamente

# Clone sin trees grandes
git clone --filter=tree:1 <url>

# Clone con filtro de tamaño
git clone --filter=blob:limit=1m <url>
# Solo descarga blobs menores a 1MB
```

**Ventajas:**
- ✅ Clonado mucho más rápido
- ✅ Menor uso de espacio inicial
- ✅ Útil para CI/CD

**Desventajas:**
- ❌ Algunas operaciones pueden ser más lentas
- ❌ Requiere conexión para operaciones que necesitan objetos no descargados

---

## 8. Ejercicios prácticos con GitLab local

**Prerequisitos:**
- GitLab local instalado y funcionando (http://gitlab.local o similar)
- Acceso a GitLab con usuario y contraseña
- VSCode instalado con extensiones Git Graph y GitLens
- Token de acceso personal de GitLab (opcional pero recomendado)

### 8.1. ⭐ Ejercicio principal: Reset, ramas, conflictos y recuperación (60 minutos)

**Objetivo:** Dominar operaciones avanzadas de Git trabajando con reset, creación de ramas desde puntos históricos, resolución de conflictos complejos y recuperación usando reflog. Este ejercicio integra múltiples conceptos avanzados en un escenario realista.

**Escenario:** Estás trabajando en un proyecto y necesitas:
1. Deshacer commits recientes (reset)
2. Crear una rama desde un punto histórico
3. Trabajar en paralelo con otros desarrolladores
4. Resolver conflictos complejos
5. Recuperar trabajo perdido usando reflog

**Pasos:**

#### Parte 1: Preparar el escenario inicial (10 minutos)

1. **Crear proyecto en GitLab:**

```bash
# Crear proyecto en GitLab (desde interfaz web)
# Nombre: "ejercicio-principal-git"
# URL: http://gitlab.local/usuario/ejercicio-principal-git

# Clonar proyecto
git clone http://gitlab.local/usuario/ejercicio-principal-git.git
cd ejercicio-principal-git

# Configurar usuario
git config user.name "Tu Nombre"
git config user.email "tu-email@empresa.com"
```

2. **Crear historial inicial con múltiples commits:**

```bash
# Commit 1: Estructura inicial
mkdir -p src/utils
echo "# Proyecto Principal" > README.md
echo "version = '1.0.0'" > config.py
git add .
git commit -m "feat: estructura inicial del proyecto"
git push -u origin main

# Commit 2: Funcionalidad básica
echo "def saludar(nombre):" > src/main.py
echo "    return f'Hola, {nombre}!'" >> src/main.py
git add src/main.py
git commit -m "feat: añadir función de saludo"
git push origin main

# Commit 3: Utilidades
echo "def sumar(a, b):" > src/utils/math.py
echo "    return a + b" >> src/utils/math.py
git add src/utils/math.py
git commit -m "feat: añadir función sumar"
git push origin main

# Commit 4: Mejora de saludo
echo "def despedir(nombre):" >> src/main.py
echo "    return f'Adiós, {nombre}!'" >> src/main.py
git add src/main.py
git commit -m "feat: añadir función despedir"
git push origin main

# Commit 5: Documentación
echo "# Utilidades" > src/utils/README.md
echo "Funciones matemáticas básicas" >> src/utils/README.md
git add src/utils/README.md
git commit -m "docs: añadir documentación de utilidades"
git push origin main

# Ver historial
git log --oneline --graph --all
```

3. **Abrir en VSCode y ver en Git Graph:**

```bash
code .

# En VSCode:
# 1. Abrir Git Graph (icono en barra de estado)
# 2. Observar el historial lineal con 5 commits
# 3. Anotar los hashes de los commits (los necesitarás después)
```

#### Parte 2: Reset y recuperación (15 minutos)

4. **Simular error: Reset accidental (soft):**

```bash
# Situación: Quieres deshacer los últimos 2 commits pero mantener los cambios
# Ver estado actual
git log --oneline -5
git status

# Reset soft (mantiene cambios en staging)
git reset --soft HEAD~2

# Ver estado
git status
# Deberías ver los archivos de los últimos 2 commits en staging

# Ver historial
git log --oneline
# Ahora solo deberías ver 3 commits

# Ver en Git Graph
# Refrescar (Ctrl+R) y observar cómo cambió el historial
```

5. **Recuperar usando reflog:**

```bash
# Ver reflog para encontrar el commit perdido
git reflog

# Buscar la entrada que dice "commit: docs: añadir documentación..."
# Anotar el hash (ejemplo: HEAD@{1} o HEAD@{2})

# Verificar qué commit era
git show HEAD@{1}
# O
git log HEAD@{1} --oneline -1

# Recuperar creando rama desde ese punto
git checkout -b recuperacion-docs HEAD@{1}

# Verificar que se recuperó
git log --oneline
ls -la src/utils/README.md
# El archivo debería estar ahí

# Volver a main
git checkout main
```

6. **Reset hard (perder cambios):**

```bash
# ⚠️ ADVERTENCIA: Esto descarta cambios permanentemente
# Primero, guardar el estado actual en una rama de seguridad
git checkout -b backup-antes-reset

# Volver a main
git checkout main

# Reset hard (descarta cambios)
git reset --hard HEAD~2

# Ver estado
git status
# Debería estar limpio

# Ver historial
git log --oneline
# Solo 3 commits

# Los archivos de los últimos 2 commits ya no existen en working directory
ls src/utils/README.md
# No debería existir
```

7. **Recuperar desde reflog:**

```bash
# Ver reflog
git reflog

# Identificar el commit antes del reset
# Buscar algo como: "HEAD@{2}: commit: docs: añadir documentación..."

# Opción A: Restaurar directamente
git reset --hard HEAD@{2}

# Verificar
git log --oneline
ls src/utils/README.md
# Debería estar de vuelta

# Opción B: Crear rama desde ese punto (más seguro)
# Si usaste la opción A, primero:
git checkout -b rama-recuperada HEAD@{2}
```

#### Parte 3: Crear rama desde punto histórico y trabajar (15 minutos)

8. **Crear rama desde commit específico:**

```bash
# Volver a main
git checkout main

# Ver historial completo
git log --oneline --all

# Crear rama desde el commit 3 (antes de los cambios que hicimos)
# Primero, identificar el hash del commit 3
git log --oneline
# Anotar el hash del commit "feat: añadir función sumar"

# Crear rama desde ese commit
git checkout -b feature-nueva-funcionalidad <hash-commit-3>
# O usando HEAD~2 si estás en main
git checkout -b feature-nueva-funcionalidad HEAD~2

# Verificar en qué commit estás
git log --oneline -3
git status
```

9. **Desarrollar en la nueva rama:**

```bash
# Estás en feature-nueva-funcionalidad, en un commit anterior
# Añadir nueva funcionalidad
echo "def multiplicar(a, b):" >> src/utils/math.py
echo "    return a * b" >> src/utils/math.py
git add src/utils/math.py
git commit -m "feat: añadir función multiplicar"

echo "def dividir(a, b):" >> src/utils/math.py
echo "    if b == 0:" >> src/utils/math.py
echo "        raise ValueError('División por cero')" >> src/utils/math.py
echo "    return a / b" >> src/utils/math.py
git add src/utils/math.py
git commit -m "feat: añadir función dividir con validación"

# Ver historial de la rama
git log --oneline --graph
```

10. **Push de la rama a GitLab:**

```bash
# Push de la nueva rama
git push -u origin feature-nueva-funcionalidad

# Ver en GitLab web
# Ir a: http://gitlab.local/usuario/ejercicio-principal-git/-/branches
# Deberías ver la nueva rama

# Ver en Git Graph de VSCode
# Refrescar y observar la nueva rama
```

#### Parte 4: Trabajo paralelo y conflictos (15 minutos)

11. **Simular trabajo paralelo en main:**

```bash
# Cambiar a main
git checkout main

# Asegurar que main está actualizado
git pull origin main

# Hacer cambios que entrarán en conflicto
# Modificar el mismo archivo que se modificó en la feature
echo "# Funciones matemáticas" > src/utils/math.py
echo "def restar(a, b):" >> src/utils/math.py
echo "    return a - b" >> src/utils/math.py
git add src/utils/math.py
git commit -m "feat: añadir función restar en main"
git push origin main

# Modificar otro archivo también
echo "def calcular_promedio(numeros):" >> src/main.py
echo "    return sum(numeros) / len(numeros)" >> src/main.py
git add src/main.py
git commit -m "feat: añadir función calcular promedio"
git push origin main
```

12. **Intentar merge y resolver conflictos:**

```bash
# Volver a la feature branch
git checkout feature-nueva-funcionalidad

# Intentar merge de main
git merge main

# Git debería indicar conflictos
git status
# Deberías ver: "Unmerged paths: src/utils/math.py"

# Ver el conflicto
cat src/utils/math.py

# Deberías ver algo como:
# <<<<<<< HEAD
# def multiplicar(a, b):
#     return a * b
# def dividir(a, b):
#     if b == 0:
#         raise ValueError('División por cero')
#     return a / b
# =======
# # Funciones matemáticas
# def restar(a, b):
#     return a - b
# >>>>>>> main
```

13. **Resolver conflictos manualmente:**

```bash
# Editar src/utils/math.py para resolver el conflicto
# Dejar todas las funciones:
cat > src/utils/math.py << 'EOF'
# Funciones matemáticas
def sumar(a, b):
    return a + b

def restar(a, b):
    return a - b

def multiplicar(a, b):
    return a * b

def dividir(a, b):
    if b == 0:
        raise ValueError('División por cero')
    return a / b
EOF

# Marcar como resuelto
git add src/utils/math.py

# Verificar que no hay más conflictos
git status

# Completar el merge
git commit -m "merge: integrar cambios de main con feature-nueva-funcionalidad"

# Ver historial
git log --oneline --graph --all
```

14. **Ver en Git Graph:**

```bash
# En VSCode Git Graph:
# 1. Refrescar (Ctrl+R)
# 2. Observar el merge commit
# 3. Ver cómo se unen las dos ramas
# 4. Click en el merge commit para ver los cambios
```

#### Parte 5: Rebase y limpieza (10 minutos)

15. **Rebase interactivo para limpiar historial:**

```bash
# Antes de hacer merge a main, limpiar el historial de la feature
# Ver últimos commits
git log --oneline -5

# Rebase interactivo
git rebase -i HEAD~3

# En el editor que se abre:
# - Cambiar "pick" a "squash" en los commits de multiplicar y dividir
# - Dejar "pick" en el commit de merge
# Guardar y cerrar

# Git abrirá otro editor para combinar los mensajes
# Escribir: "feat: añadir funciones multiplicar y dividir con validación"
# Guardar y cerrar

# Ver historial limpio
git log --oneline --graph
```

16. **Force push de la rama actualizada:**

```bash
# Como el rebase reescribió el historial, necesitas force push
git push --force-with-lease origin feature-nueva-funcionalidad

# Verificar en GitLab
# La rama debería tener el historial limpio
```

#### Parte 6: Merge final y limpieza (5 minutos)

17. **Merge a main y limpieza:**

```bash
# Cambiar a main
git checkout main

# Merge de la feature
git merge --no-ff feature-nueva-funcionalidad -m "merge: integrar funciones matemáticas avanzadas"

# Ver historial final
git log --oneline --graph --all --decorate

# Push a GitLab
git push origin main

# Eliminar rama local
git branch -d feature-nueva-funcionalidad

# Eliminar rama remota (opcional)
git push origin --delete feature-nueva-funcionalidad
```

18. **Verificación final:**

```bash
# Ver estado final
git status
git log --oneline --graph --all

# Ver archivos finales
cat src/utils/math.py
cat src/main.py

# En Git Graph de VSCode:
# - Ver el historial completo
# - Observar el merge commit
# - Ver cómo se integraron los cambios

# En GitLab web:
# - Ver el historial del proyecto
# - Ver el merge commit
# - Ver los archivos finales
```

**Preguntas de reflexión:**

- ¿Cuál es la diferencia entre `reset --soft`, `reset --mixed` y `reset --hard`?
- ¿Por qué es importante el reflog para recuperar trabajo perdido?
- ¿Cuándo usarías `--force-with-lease` en lugar de `--force`?
- ¿Qué ventajas tiene crear una rama desde un commit histórico?
- ¿Cómo decides qué código mantener al resolver conflictos?
- ¿Cuándo es mejor usar rebase vs merge para integrar cambios?

**Conceptos practicados en este ejercicio:**

✅ Reset (soft, mixed, hard)  
✅ Reflog y recuperación  
✅ Crear ramas desde commits históricos  
✅ Trabajo paralelo en ramas  
✅ Resolución de conflictos complejos  
✅ Rebase interactivo  
✅ Force push con --force-with-lease  
✅ Merge con --no-ff  
✅ Visualización con Git Graph  
✅ Integración con GitLab  

---

### 8.2. Ejercicio 2: Configurar GitLab local y explorar Git internals (25 minutos)

**Objetivo:** Configurar conexión con GitLab local, crear proyecto y explorar la estructura interna de Git.

**Pasos:**

1. **Configurar GitLab local:**

```bash
# Verificar acceso a GitLab
curl http://gitlab.local
# O la URL de tu GitLab local

# Configurar Git para usar GitLab local
git config --global credential.helper store

# Crear token de acceso personal en GitLab:
# 1. Ir a GitLab → User Settings → Access Tokens
# 2. Crear token con permisos: api, read_repository, write_repository
# 3. Copiar el token
```

2. **Crear proyecto en GitLab:**

```bash
# Opción A: Desde la interfaz web de GitLab
# 1. Ir a GitLab → New Project → Create blank project
# 2. Nombre: "ejercicio-internals"
# 3. Visibility: Private
# 4. Copiar la URL del proyecto

# Opción B: Usar GitLab API (si tienes token)
curl --request POST \
  --header "PRIVATE-TOKEN: tu-token" \
  --data "name=ejercicio-internals&visibility=private" \
  http://gitlab.local/api/v4/projects
```

3. **Clonar y preparar repositorio:**

```bash
# Clonar proyecto desde GitLab
git clone http://gitlab.local/usuario/ejercicio-internals.git
cd ejercicio-internals

# Configurar usuario local
git config user.name "Tu Nombre"
git config user.email "tu-email@empresa.com"

# Crear estructura de archivos
mkdir -p src/utils
echo "def hello():" > src/main.py
echo "    print('Hello')" >> src/main.py
echo "def helper():" > src/utils/helper.py
echo "    return True" >> src/utils/helper.py
echo "# Proyecto" > README.md

git add .
git commit -m "Commit inicial con estructura"
git push -u origin main
```

4. **Explorar Git internals:**

```bash
# Ver estructura de .git
tree .git -L 2
# O
find .git -type f | head -20

# Ver HEAD
cat .git/HEAD

# Ver hash del commit
git rev-parse HEAD

# Ver contenido del commit
git cat-file -p HEAD

# Ver el tree del commit
TREE_HASH=$(git rev-parse HEAD^{tree})
echo "Tree hash: $TREE_HASH"
git cat-file -p $TREE_HASH

# Ver contenido de blobs
git cat-file -p HEAD:README.md
git cat-file -p HEAD:src/main.py

# Ver referencias
cat .git/refs/heads/main
git show-ref
```

5. **Ver en GitLab y VSCode:**

```bash
# Abrir en VSCode
code .

# En VSCode:
# 1. Abrir Git Graph (icono en barra de estado o Ctrl+Shift+P → "Git Graph")
# 2. Ver el gráfico del historial
# 3. Click en el commit para ver detalles
# 4. Explorar la estructura visual

# Ver en GitLab web:
# 1. Ir a http://gitlab.local/usuario/ejercicio-internals
# 2. Ver el commit en la interfaz web
# 3. Comparar con lo que viste en terminal
```

6. **Crear más commits y observar:**

```bash
# Hacer cambios
echo "def goodbye():" >> src/main.py
echo "    print('Goodbye')" >> src/main.py
git add src/main.py
git commit -m "Añadir función goodbye"

# Push a GitLab
git push origin main

# Ver en Git Graph de VSCode
# - Refrescar (Ctrl+R)
# - Ver el nuevo commit en el gráfico
# - Observar cómo se conecta con el commit anterior
```

**Preguntas de reflexión:**
- ¿Qué información contiene un commit object?
- ¿Cómo se relacionan blobs, trees y commits?
- ¿Qué ventajas tiene ver el historial en Git Graph vs terminal?

### 8.3. Ejercicio 3: Limpiar historial y trabajar con filter-repo (30 minutos)

**Objetivo:** Usar git filter-repo para eliminar archivos sensibles del historial completo trabajando con GitLab local.

**⚠️ Importante:** Este ejercicio reescribe el historial. Trabaja en un repositorio de prueba en GitLab.

**Pasos:**

1. **Crear proyecto en GitLab y preparar historial problemático:**

```bash
# Crear proyecto en GitLab (desde interfaz web o API)
# Nombre: "ejercicio-filter-repo"
# URL: http://gitlab.local/usuario/ejercicio-filter-repo

# Clonar proyecto
git clone http://gitlab.local/usuario/ejercicio-filter-repo.git
cd ejercicio-filter-repo

# Crear commits con archivo sensible
echo "password=secret123" > config.ini
git add config.ini
git commit -m "Añadir configuración"
git push origin main

echo "def login():" > app.py
echo "    return True" >> app.py
git add app.py
git commit -m "Añadir función login"
git push origin main

# Modificar archivo sensible
echo "password=secret123" > config.ini
echo "api_key=abc123" >> config.ini
git add config.ini
git commit -m "Añadir API key"
git push origin main

echo "def logout():" >> app.py
git add app.py
git commit -m "Añadir función logout"
git push origin main

# Ver historial en GitLab web
# Ir a: http://gitlab.local/usuario/ejercicio-filter-repo/-/commits/main
# Ver que config.ini está en el historial

# Ver historial local
git log --oneline --all
git log --all --full-history -- config.ini
```

2. **Instalar git-filter-repo y hacer backup:**

```bash
# Verificar si está instalado
git filter-repo --version

# Si no está, instalar:
# pip install git-filter-repo
# O: sudo apt install git-filter-repo

# IMPORTANTE: Hacer backup del repositorio remoto
# Opción 1: Clonar en otra ubicación como backup
cd ..
git clone http://gitlab.local/usuario/ejercicio-filter-repo.git ejercicio-filter-repo-backup
cd ejercicio-filter-repo

# Opción 2: Crear rama de backup en GitLab
git checkout -b backup-before-filter-repo
git push origin backup-before-filter-repo
git checkout main
```

3. **Eliminar archivo sensible del historial:**

```bash
# Eliminar archivo del historial completo
git filter-repo --path config.ini --invert-paths

# Verificar que se eliminó localmente
git log --oneline --all
git log --all --full-history -- config.ini
# No debería aparecer nada

# Verificar que otros archivos siguen
git log --all --full-history -- app.py
# Debería seguir apareciendo

# Ver en Git Graph de VSCode
code .
# Abrir Git Graph y observar cómo cambió el historial
```

4. **Force push a GitLab (reescribir historial remoto):**

```bash
# ⚠️ ADVERTENCIA: Esto reescribe el historial en GitLab
# Asegúrate de que nadie más está trabajando en este repo

# Force push con lease (más seguro)
git push --force-with-lease origin main

# Verificar en GitLab web
# Ir a: http://gitlab.local/usuario/ejercicio-filter-repo/-/commits/main
# El archivo config.ini ya no debería aparecer en el historial
```

5. **Cambiar email de autor en historial:**

```bash
# Ver emails actuales
git log --format='%ae' | sort -u

# Cambiar email en todo el historial
git filter-repo --email-callback '
return email.replace(b"tu-email@ejemplo.com", b"nuevo-email@ejemplo.com")
'

# Verificar cambio
git log --format='%ae' | sort -u

# Push cambios
git push --force-with-lease origin main
```

6. **Limpiar y optimizar:**

```bash
# Limpiar referencias
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Ver tamaño del repositorio
du -sh .git

# Ver en Git Graph cómo se ve el historial limpio
```

**Preguntas de reflexión:**
- ¿Por qué es importante hacer backup antes de filter-repo?
- ¿Qué pasa con los hashes de commits después de filter-repo?
- ¿Cómo notificarías a tu equipo después de reescribir historial en GitLab?
- ¿Qué ventajas tiene `--force-with-lease` sobre `--force`?

### 8.4. Ejercicio 4: Implementar Git Flow completo en GitLab (35 minutos)

**Objetivo:** Practicar el workflow completo de Git Flow con features, releases y hotfixes trabajando con GitLab local y Merge Requests.

**Pasos:**

1. **Crear proyecto en GitLab e inicializar Git Flow:**

```bash
# Crear proyecto en GitLab
# Nombre: "ejercicio-gitflow"
# URL: http://gitlab.local/usuario/ejercicio-gitflow

# Clonar proyecto
git clone http://gitlab.local/usuario/ejercicio-gitflow.git
cd ejercicio-gitflow

# Crear estructura inicial
echo "# Proyecto Git Flow" > README.md
git add README.md
git commit -m "Initial commit"
git branch -M main
git push -u origin main

# Crear rama develop
git checkout -b develop
echo "version = '0.1.0'" > version.py
git add version.py
git commit -m "Setup develop branch"
git push -u origin develop
```

2. **Trabajar en una feature con Merge Request:**

```bash
# Crear feature desde develop
git checkout develop
git pull origin develop
git checkout -b feature/user-authentication

# Desarrollar feature
echo "def login(username, password):" > auth.py
echo "    return True" >> auth.py
git add auth.py
git commit -m "feat: implementar login"

echo "def logout():" >> auth.py
git add auth.py
git commit -m "feat: implementar logout"

# Push de la feature
git push -u origin feature/user-authentication

# Crear Merge Request en GitLab:
# 1. Ir a GitLab → Merge Requests → New Merge Request
# 2. Source: feature/user-authentication
# 3. Target: develop
# 4. Título: "feat: implementar autenticación de usuarios"
# 5. Crear Merge Request

# Simular aprobación y merge desde GitLab web
# O hacer merge localmente:
git checkout develop
git merge --no-ff feature/user-authentication -m "Merge feature/user-authentication"
git push origin develop
git branch -d feature/user-authentication
git push origin --delete feature/user-authentication

# Ver historial en Git Graph
code .
# Abrir Git Graph y observar el merge commit
```

3. **Crear release:**

```bash
# Crear rama de release
git checkout develop
git pull origin develop
git checkout -b release/1.0.0

# Preparar release (actualizar versión, changelog, etc.)
echo "version = '1.0.0'" > version.py
echo "# Changelog" > CHANGELOG.md
echo "## 1.0.0" >> CHANGELOG.md
echo "- Autenticación de usuarios" >> CHANGELOG.md
git add .
git commit -m "chore: preparar release 1.0.0"
git push -u origin release/1.0.0

# Finalizar release (merge a main)
git checkout main
git pull origin main
git merge --no-ff release/1.0.0 -m "Release 1.0.0"
git tag -a v1.0.0 -m "Versión 1.0.0"
git push origin main
git push origin v1.0.0

# Merge a develop también
git checkout develop
git merge --no-ff release/1.0.0 -m "Merge release/1.0.0 to develop"
git push origin develop
git branch -d release/1.0.0
git push origin --delete release/1.0.0

# Ver en GitLab web y Git Graph
```

4. **Crear hotfix:**

```bash
# Simular bug en producción
git checkout main
git pull origin main
git checkout -b hotfix/security-patch

# Corregir bug
echo "# Security fix" >> auth.py
echo "def validate_input(data):" >> auth.py
echo "    # Validación de seguridad" >> auth.py
echo "    return True" >> auth.py
git add auth.py
git commit -m "fix: parche de seguridad en autenticación"
git push -u origin hotfix/security-patch

# Finalizar hotfix
git checkout main
git merge --no-ff hotfix/security-patch -m "Hotfix: security patch"
git tag -a v1.0.1 -m "Versión 1.0.1 - Security patch"
git push origin main
git push origin v1.0.1

# Merge a develop también
git checkout develop
git merge --no-ff hotfix/security-patch -m "Merge hotfix to develop"
git push origin develop
git branch -d hotfix/security-patch
git push origin --delete hotfix/security-patch

# Ver historial completo en Git Graph
```

5. **Visualizar el flujo completo:**

```bash
# Ver gráfico completo
git log --oneline --graph --all --decorate

# Ver en Git Graph de VSCode
# Observar la estructura completa de Git Flow

# Ver en GitLab web
# - Ver todas las ramas
# - Ver tags
# - Ver historial completo
```

**Preguntas de reflexión:**
- ¿Por qué se mergea el hotfix tanto a main como a develop?
- ¿Cuál es la diferencia entre release y hotfix?
- ¿Cuándo usarías Git Flow vs GitHub Flow?
- ¿Cómo se integra Git Flow con Merge Requests en GitLab?

---

### 8.5. Ejercicio 5: Optimizar repositorio y usar VSCode Git Graph (30 minutos)

**Objetivo:** Aplicar técnicas de optimización a un repositorio grande trabajando con GitLab local y visualizar todo con Git Graph.

**Pasos:**

1. **Crear proyecto en GitLab y preparar repositorio "grande":**

```bash
# Crear proyecto en GitLab
# Nombre: "ejercicio-optimizacion"
# URL: http://gitlab.local/usuario/ejercicio-optimizacion

# Clonar proyecto
git clone http://gitlab.local/usuario/ejercicio-optimizacion.git
cd ejercicio-optimizacion

# Crear muchos commits pequeños
for i in {1..50}; do
    echo "Commit $i" > "file$i.txt"
    git add "file$i.txt"
    git commit -m "Commit $i"
    # Push cada 10 commits para simular trabajo real
    if [ $((i % 10)) -eq 0 ]; then
        git push origin main
    fi
done
git push origin main

# Ver tamaño
du -sh .git
git count-objects -vH
```

2. **Explorar objetos:**

```bash
# Ver objetos sueltos
find .git/objects -type f | grep -v pack | wc -l

# Ver tamaño de objetos
du -sh .git/objects
```

3. **Ejecutar garbage collection:**

```bash
# GC normal
git gc

# Ver diferencia
du -sh .git
git count-objects -vH

# Ver packfiles creados
ls -lh .git/objects/pack/
```

4. **GC agresiva:**

```bash
# GC agresiva
git gc --aggressive --prune=now

# Ver resultados
du -sh .git
git count-objects -vH
```

5. **Limpiar reflog:**

```bash
# Ver reflog
git reflog | wc -l

# Expirar reflog antiguo
git reflog expire --expire=1.hour.ago --all

# GC después
git gc --prune=now

# Ver diferencia
du -sh .git
```

6. **Probar shallow clone desde GitLab:**

```bash
# Shallow clone desde GitLab
cd ..
git clone --depth 5 http://gitlab.local/usuario/ejercicio-optimizacion.git ejercicio-shallow

# Comparar tamaños
du -sh ejercicio-optimizacion/.git
du -sh ejercicio-shallow/.git

# Ver commits disponibles
cd ejercicio-shallow
git log --oneline --all
# Solo verás 5 commits

# Ver en Git Graph
code .
# Abrir Git Graph y observar que solo muestra 5 commits

# Aumentar profundidad
git fetch --unshallow
git log --oneline --all
# Ahora verás todos

# Refrescar Git Graph para ver todos los commits
```

7. **Configurar maintenance:**

```bash
cd ../ejercicio-optimizacion

# Iniciar maintenance
git maintenance start

# Ver estado
git maintenance status

# Ejecutar tareas manualmente
git maintenance run --task=gc
git maintenance run --task=commit-graph

# Detener (para el ejercicio)
git maintenance stop
```

7. **Visualizar optimizaciones en Git Graph:**

```bash
# Volver al repositorio original
cd ../ejercicio-optimizacion
code .

# En Git Graph:
# 1. Observar el historial completo
# 2. Ver cómo se ven los packfiles (no visible directamente, pero el repo es más pequeño)
# 3. Comparar antes y después de git gc
# 4. Usar filtros para navegar por los 50 commits
```

**Preguntas de reflexión:**
- ¿Cuándo es útil usar shallow clone?
- ¿Qué optimizaciones hace git gc?
- ¿Por qué es importante el mantenimiento regular?
- ¿Cómo ayuda Git Graph a visualizar repositorios grandes?

---

## 9. Técnicas expertas adicionales

### 9.1. Múltiples remotos y sincronización

Trabajar con múltiples remotos es útil para:
- Sincronizar con múltiples servidores
- Contribuir a forks y upstream
- Backup en múltiples ubicaciones

**Configurar múltiples remotos:**

```bash
# Añadir remotos adicionales
git remote add upstream https://gitlab.com/original/repo.git
git remote add backup https://backup-server.com/repo.git

# Ver todos los remotos
git remote -v

# Fetch de todos los remotos
git fetch --all

# Push a remoto específico
git push origin main
git push backup main

# Push a todos los remotos
git remote | xargs -I {} git push {} main
```

**Sincronizar fork con upstream:**

```bash
# Configurar upstream
git remote add upstream <url-repo-original>

# Fetch cambios de upstream
git fetch upstream

# Merge cambios de upstream a tu fork
git checkout main
git merge upstream/main

# O usar rebase
git rebase upstream/main

# Push a tu fork
git push origin main
```

### 9.2. Git patches y aplicación manual

Los patches permiten compartir cambios sin acceso directo al repositorio.

**Crear patch:**

```bash
# Patch del último commit
git format-patch -1 HEAD

# Patch de múltiples commits
git format-patch HEAD~3

# Patch de rango
git format-patch origin/main..HEAD

# Patch de rama completa
git format-patch main..feature-branch
```

**Aplicar patch:**

```bash
# Aplicar patch
git apply patch-file.patch

# Verificar antes de aplicar
git apply --check patch-file.patch

# Aplicar con commit automático
git am patch-file.patch

# Aplicar múltiples patches
git am *.patch
```

**Enviar patch por email:**

```bash
# Configurar email
git config sendemail.smtpserver smtp.gmail.com
git config sendemail.smtpuser tu-email@gmail.com

# Enviar patch
git send-email patch-file.patch
```

### 9.3. Git protocol y transfer protocols

Git soporta múltiples protocolos para transferir datos:

**Protocolos disponibles:**

```bash
# HTTPS (más común, funciona detrás de firewalls)
git clone https://gitlab.com/user/repo.git

# SSH (más seguro, requiere keys)
git clone git@gitlab.com:user/repo.git

# Git protocol (rápido, pero sin autenticación)
git clone git://gitlab.com/user/repo.git

# File protocol (local)
git clone /path/to/repo.git
git clone file:///path/to/repo.git
```

**Configurar protocolo preferido:**

```bash
# Usar siempre HTTPS
git config --global url."https://".insteadOf git://

# Usar SSH para dominios específicos
git config --global url."git@gitlab.com:".insteadOf "https://gitlab.com/"
```

### 9.4. Hooks del servidor (pre-receive, update, post-receive)

Los hooks del servidor se ejecutan en el servidor Git (GitLab, Gitolite, etc.).

**Pre-receive hook:**

Se ejecuta antes de aceptar cualquier push. Puede rechazar el push.

```bash
#!/bin/bash
# .git/hooks/pre-receive (en el servidor)

while read oldrev newrev refname; do
    # Rechazar push si contiene archivos grandes
    git diff --name-only $oldrev $newrev | while read file; do
        size=$(git cat-file -s $newrev:$file 2>/dev/null || echo 0)
        if [ $size -gt 10485760 ]; then  # 10MB
            echo "Error: Archivo $file es muy grande (>10MB)"
            exit 1
        fi
    done
done
```

**Update hook:**

Se ejecuta por cada rama que se actualiza.

```bash
#!/bin/bash
# .git/hooks/update

refname=$1
oldrev=$2
newrev=$3

# Rechazar force push a main
if [ "$refname" = "refs/heads/main" ]; then
    if [ "$oldrev" != "0000000000000000000000000000000000000000" ]; then
        if ! git merge-base --is-ancestor $oldrev $newrev; then
            echo "Error: Force push a main no permitido"
            exit 1
        fi
    fi
fi
```

**Post-receive hook:**

Se ejecuta después de aceptar el push. Útil para CI/CD, notificaciones, etc.

```bash
#!/bin/bash
# .git/hooks/post-receive

while read oldrev newrev refname; do
    if [ "$refname" = "refs/heads/main" ]; then
        # Trigger deployment
        /usr/local/bin/deploy.sh
    fi
done
```

**En GitLab:**

Los hooks del servidor en GitLab se configuran en el servidor GitLab, no en el repositorio. Se pueden configurar en:
- `/opt/gitlab/embedded/service/gitlab-shell/hooks/` (GitLab self-managed)
- A través de la interfaz web de GitLab (Custom hooks)

---

## Resumen final

En esta sesión has aprendido:

1. **Git internals:** Estructura de .git, objetos (blobs, trees, commits), referencias, packfiles
2. **Limpieza masiva:** filter-branch y filter-repo para reescribir historial completo
3. **Subtrees:** Alternativa a submódulos para código compartido
4. **Workflows avanzados:** Git Flow, GitHub Flow, GitLab Flow, Trunk-based
5. **Optimización:** GC, maintenance, shallow/partial clones
6. **Técnicas expertas:** Múltiples remotos, patches, protocolos, hooks del servidor

**Próximos pasos:**
- Practicar los ejercicios hasta dominarlos
- Aplicar estos conceptos en proyectos reales
- Experimentar con diferentes workflows según el proyecto
- Optimizar repositorios existentes

**Recursos adicionales:**
- `git help <comando>` - Documentación integrada
- [Pro Git book](https://git-scm.com/book) - Libro completo y gratuito
- [Git internals](https://git-scm.com/book/en/v2/Git-Internals) - Capítulo sobre internals
- GitLab documentation - Guías oficiales

---

**¡Felicidades por completar esta sesión avanzada!** 🎉

Ahora tienes un conocimiento profundo de Git que te permitirá trabajar eficientemente en cualquier proyecto, sin importar su complejidad.

