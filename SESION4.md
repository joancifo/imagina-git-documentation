# Sesión 4: Repaso integral y ejercicios avanzados

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
   - 2.4. Submódulos

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

4. [Ejercicios prácticos avanzados](#4-ejercicios-prácticos-avanzados) (115 minutos)
   - 4.1. Ejercicio 1: Recuperación de commits perdidos (20 minutos)
   - 4.2. Ejercicio 2: Reorganización completa de historial (25 minutos)
   - 4.3. Ejercicio 3: Resolución de conflictos complejos (20 minutos)
   - 4.4. Ejercicio 4: Flujo colaborativo completo (25 minutos)
   - 4.5. Ejercicio 5: Cherry-pick y bisect (25 minutos)

5. [Casos de uso reales](#5-casos-de-uso-reales) (15 minutos)
   - 5.1. Escenario: Migración de feature branch
   - 5.2. Escenario: Limpieza de historial antes de merge
   - 5.3. Escenario: Recuperación tras error crítico

6. [Checklist de buenas prácticas](#6-checklist-de-buenas-prácticas) (10 minutos)

**Duración total estimada: 3 horas 40 minutos**

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

---

## 2. Repaso de técnicas avanzadas

### 2.1. Reescritura de historial

**Modificar el último commit:**

```bash
# Cambiar mensaje del último commit
git commit --amend -m "Nuevo mensaje"

# Añadir cambios al último commit
git add <archivo-olvidado>
git commit --amend --no-edit     # Mantiene el mensaje anterior

# Cambiar autor del último commit
git commit --amend --author="Nombre <email>"
```

**⚠️ Importante:** Solo modificar commits que aún no se han hecho `push`. Si ya están en el remoto, usar `--force` puede causar problemas a otros desarrolladores.

**Rebase interactivo:**

Permite reordenar, combinar, editar o eliminar commits:

```bash
git rebase -i HEAD~3              # Últimos 3 commits
git rebase -i <hash>              # Desde un commit específico
```

**Comandos en el editor interactivo:**

- `pick`: Usar el commit tal cual
- `reword`: Cambiar mensaje del commit
- `edit`: Pausar para editar el commit
- `squash`: Combinar con el commit anterior
- `fixup`: Como squash pero descarta el mensaje
- `drop`: Eliminar el commit

**Ejemplo de flujo:**

```bash
# Iniciar rebase interactivo
git rebase -i HEAD~3

# En el editor aparece:
# pick abc1234 Commit 1
# pick def5678 Commit 2
# pick ghi9012 Commit 3

# Cambiar a:
# pick abc1234 Commit 1
# squash def5678 Commit 2
# reword ghi9012 Commit 3

# Guardar y cerrar. Git abrirá otro editor para:
# - Combinar mensajes de commits 1 y 2
# - Editar mensaje del commit 3
```

### 2.2. Recuperación de cambios

**Reflog - El salvavidas de Git:**

El reflog registra todos los movimientos de HEAD (commits, checkouts, resets, etc.):

```bash
git reflog                      # Ver todo el historial de HEAD
git reflog show <rama>          # Ver reflog de una rama específica
```

**Salida típica:**

```
f2d3a4b (HEAD -> main) HEAD@{0}: commit: Ajuste final
ab12c34 HEAD@{1}: commit: Bugfix en login
3e4f5a6 HEAD@{2}: checkout: moving from feature to main
c7d8e9f HEAD@{3}: commit: Nueva feature
```

**Recuperar commits perdidos:**

```bash
# Ver qué había antes
git reflog

# Restaurar a un estado anterior
git reset --hard HEAD@{2}       # Volver a la posición 2 del reflog
git checkout -b recuperado HEAD@{3}  # Crear rama desde estado anterior
```

**Reset - Mover HEAD:**

```bash
# Soft: Mueve HEAD pero mantiene cambios en staging
git reset --soft HEAD~1         # Deshace último commit, cambios quedan staged

# Mixed (por defecto): Mueve HEAD y limpia staging, mantiene cambios en working
git reset HEAD~1                # Deshace último commit, cambios quedan unstaged
git reset --mixed HEAD~1        # Equivalente al anterior

# Hard: Mueve HEAD y descarta TODOS los cambios
git reset --hard HEAD~1         # ⚠️ PELIGROSO: Deshace commit y descarta cambios
```

**Revert - Deshacer sin reescribir:**

Crea un nuevo commit que invierte los cambios de otro:

```bash
git revert <hash>               # Crear commit que deshace <hash>
git revert HEAD                 # Deshacer último commit
git revert --no-commit <hash>   # Revertir sin hacer commit (útil para múltiples)
```

**Diferencia clave:**

- `reset`: Mueve punteros, reescribe historial (para commits locales)
- `revert`: Crea nuevo commit inverso, mantiene historial (para commits públicos)

### 2.3. Gestión de cambios temporales (stash)

**Guardar cambios temporalmente:**

```bash
# Guardar cambios actuales
git stash                       # Guarda cambios staged y unstaged
git stash -u                    # Incluye archivos no rastreados (untracked)
git stash -m "Mensaje"          # Guardar con mensaje descriptivo

# Ver stashes guardados
git stash list                  # Lista todos los stashes

# Aplicar stashes
git stash pop                   # Aplicar último stash y eliminarlo
git stash apply                 # Aplicar último stash sin eliminarlo
git stash apply stash@{2}       # Aplicar stash específico

# Gestionar stashes
git stash drop stash@{1}        # Eliminar stash específico
git stash clear                 # Eliminar todos los stashes
```

**Casos de uso:**

- Cambiar de rama con cambios sin commitear
- Guardar trabajo en progreso para atender urgencia
- Aplicar cambios de una rama a otra

**Ejemplo práctico:**

```bash
# Estás trabajando en feature-login
git status
# On branch feature-login
# Changes not staged for commit:
#   modified:   login.js

# Necesitas cambiar urgentemente a main para un hotfix
git stash -m "WIP: login feature"
git checkout main
# ... hacer hotfix ...
git checkout feature-login
git stash pop                   # Recuperar cambios
```

### 2.4. Submódulos

**Concepto:**

Los submódulos permiten incluir un repositorio Git dentro de otro, manteniendo historiales independientes.

**Añadir submódulo:**

```bash
git submodule add <url> <ruta>  # Añadir submódulo
# Ejemplo:
git submodule add https://gitlab.com/libexterna.git libs/libexterna
```

Esto:
- Clona el repo externo en la ruta especificada
- Crea archivo `.gitmodules` con la configuración
- Añade el submódulo al staging area

**Clonar repositorio con submódulos:**

```bash
# Opción 1: Clonar y luego inicializar submódulos
git clone <url-repo-principal>
cd repo-principal
git submodule init              # Inicializar submódulos
git submodule update            # Descargar contenido de submódulos

# Opción 2: Todo en un paso
git clone --recurse-submodules <url-repo-principal>
```

**Trabajar con submódulos:**

```bash
# Actualizar submódulo a última versión
cd libs/libexterna
git pull origin main
cd ../..
git add libs/libexterna         # Actualizar referencia en repo principal
git commit -m "Actualizar submódulo"

# Ver estado de submódulos
git submodule status            # Ver estado de todos los submódulos

# Actualizar todos los submódulos
git submodule update --remote   # Traer últimos cambios de cada submódulo
```

**Eliminar submódulo:**

```bash
# 1. Eliminar entrada de .gitmodules
# 2. Eliminar sección de .git/config
git submodule deinit -f <ruta-submodulo>
# 3. Eliminar directorio del índice
git rm -f <ruta-submodulo>
# 4. Eliminar directorio .git/modules/<submodulo>
rm -rf .git/modules/<submodulo>
# 5. Commit
git commit -m "Eliminar submódulo"
```

---

## 3. Técnicas avanzadas de Git

### 3.1. Cherry-pick: Aplicar commits selectivos

**Concepto:**

`git cherry-pick` permite aplicar commits específicos de una rama a otra, sin fusionar toda la rama. Es útil cuando solo necesitas algunos commits de una feature branch en otra rama.

**Uso básico:**

```bash
# Aplicar un commit específico a la rama actual
git cherry-pick <hash-commit>

# Aplicar múltiples commits
git cherry-pick <hash1> <hash2> <hash3>

# Aplicar un rango de commits
git cherry-pick <hash-inicio>..<hash-fin>    # No incluye hash-inicio
git cherry-pick <hash-inicio>^..<hash-fin>   # Incluye hash-inicio
```

**Ejemplo práctico:**

```bash
# Situación: Tienes un bugfix en feature-A que necesitas en main
git checkout main
git cherry-pick abc1234        # Aplicar solo el commit del bugfix

# Si hay conflictos, resolverlos y continuar
git cherry-pick --continue

# O abortar si cambias de opinión
git cherry-pick --abort
```

**Opciones útiles:**

```bash
# Cherry-pick sin hacer commit automático (útil para modificar)
git cherry-pick -n <hash>      # -n o --no-commit
git cherry-pick --no-commit <hash>

# Editar el mensaje del commit
git cherry-pick -e <hash>      # -e o --edit

# Aplicar cambios pero mantener el autor original
git cherry-pick -x <hash>      # Añade "cherry picked from commit..." al mensaje

# Aplicar cambios sin commit (solo cambios en working directory)
git cherry-pick -n <hash>
```

**Casos de uso:**

- Portar un bugfix de una rama a otra sin fusionar toda la feature
- Aplicar commits específicos de una rama experimental
- Recuperar commits perdidos de otra rama
- Aplicar hotfixes a múltiples ramas de release

**⚠️ Consideraciones:**

- Cherry-pick crea nuevos commits (nuevos hashes) aunque el contenido sea igual
- Puede causar commits duplicados si luego fusionas la rama original
- Resolver conflictos puede ser necesario si el contexto cambió

### 3.2. Git bisect: Encontrar commits problemáticos

**Concepto:**

`git bisect` es una herramienta de búsqueda binaria que ayuda a encontrar el commit que introdujo un bug. Git te guía a través del historial, probando commits hasta encontrar el problemático.

**Uso básico:**

```bash
# Iniciar sesión de bisect
git bisect start

# Marcar el commit actual como "malo" (tiene el bug)
git bisect bad                 # O git bisect bad <hash>

# Marcar un commit anterior como "bueno" (sin el bug)
git bisect good <hash>         # O git bisect good HEAD~10

# Git te llevará a un commit intermedio
# Probar el código y marcar como good o bad
git bisect good                # Si este commit está bien
git bisect bad                 # Si este commit tiene el bug

# Continuar hasta encontrar el commit problemático
# Git mostrará el hash del commit culpable

# Finalizar bisect
git bisect reset               # Volver al estado inicial
```

**Ejemplo completo:**

```bash
# Situación: El código funciona en v1.0 pero no en HEAD
git bisect start
git bisect bad                 # HEAD tiene el bug
git bisect good v1.0           # v1.0 funciona bien

# Git te lleva a un commit intermedio
# Ejecutar tests o probar manualmente
./run-tests.sh

# Si los tests fallan:
git bisect bad

# Si los tests pasan:
git bisect good

# Repetir hasta que Git identifique el commit problemático
# Salida: "abc1234 is the first bad commit"

# Ver detalles del commit problemático
git show abc1234

# Finalizar
git bisect reset
```

**Automatización con scripts:**

```bash
# Crear script de prueba (test-bug.sh)
#!/bin/bash
./run-tests.sh && exit 0 || exit 1

# Ejecutar bisect automáticamente
git bisect start HEAD v1.0
git bisect run ./test-bug.sh

# Git ejecutará el script en cada commit y marcará automáticamente
```

**Comandos adicionales:**

```bash
# Ver estado actual del bisect
git bisect log                 # Ver historial de la sesión

# Saltar un commit (si no se puede probar)
git bisect skip

# Visualizar el rango actual
git bisect visualize          # Abre gitk o herramienta visual
```

### 3.3. Git worktree: Múltiples working directories

**Concepto:**

`git worktree` permite tener múltiples directorios de trabajo (working trees) del mismo repositorio. Cada worktree tiene su propia rama activa, útil para trabajar en múltiples features simultáneamente.

**Uso básico:**

```bash
# Crear nuevo worktree
git worktree add <ruta> <rama>     # Crear worktree en ruta con rama específica
git worktree add ../proyecto-feature feature-login

# Crear worktree con nueva rama
git worktree add -b nueva-rama <ruta>
git worktree add -b hotfix ../proyecto-hotfix

# Listar worktrees
git worktree list

# Eliminar worktree
git worktree remove <ruta>
git worktree remove ../proyecto-feature

# Eliminar worktree y su directorio
git worktree remove --force <ruta>
```

**Ejemplo práctico:**

```bash
# Situación: Necesitas trabajar en main y en una feature simultáneamente
cd /ruta/proyecto

# Worktree principal (main)
git checkout main

# Crear worktree para feature
git worktree add ../proyecto-feature-login feature-login

# Ahora puedes trabajar en ambos:
# - /ruta/proyecto (main)
# - /ruta/proyecto-feature-login (feature-login)

# Cada uno tiene su propio .git (comparten el mismo repositorio)
# Puedes hacer commits, push, pull independientemente

# Cuando termines
git worktree remove ../proyecto-feature-login
```

**Ventajas:**

- No necesitas clonar múltiples veces el repositorio
- Puedes tener múltiples ramas "activas" simultáneamente
- Útil para comparar versiones o hacer builds en paralelo
- Comparten el mismo repositorio Git (ahorro de espacio)

**Limitaciones:**

- No puedes tener el mismo commit en múltiples worktrees
- Algunas operaciones afectan a todos los worktrees (como `git gc`)

### 3.4. Git hooks: Automatización de tareas

**Concepto:**

Los hooks son scripts que Git ejecuta automáticamente en eventos específicos (pre-commit, post-commit, pre-push, etc.). Permiten automatizar validaciones, tests, formateo, etc.

**Ubicación:**

Los hooks se encuentran en `.git/hooks/` del repositorio. Git incluye ejemplos (con extensión `.sample`) que puedes renombrar y editar.

**Hooks del lado del cliente (local):**

```bash
# Pre-commit: Antes de crear un commit
.git/hooks/pre-commit
# Útil para: Linters, formateo, tests rápidos

# Prepare-commit-msg: Antes de abrir el editor de mensaje
.git/hooks/prepare-commit-msg
# Útil para: Generar mensajes automáticos, añadir información

# Commit-msg: Después de escribir el mensaje
.git/hooks/commit-msg
# Útil para: Validar formato de mensajes (conventional commits)

# Post-commit: Después de crear commit
.git/hooks/post-commit
# Útil para: Notificaciones, integraciones

# Pre-push: Antes de hacer push
.git/hooks/pre-push
# Útil para: Ejecutar tests completos, validaciones
```

**Ejemplo: Pre-commit hook para Python**

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Ejecutar linter
if ! flake8 --exclude=.git,__pycache__ .; then
    echo "❌ Flake8 encontró errores. Commit abortado."
    exit 1
fi

# Ejecutar tests rápidos
if ! python -m pytest tests/unit -q; then
    echo "❌ Tests fallaron. Commit abortado."
    exit 1
fi

echo "✅ Pre-commit checks pasaron"
exit 0
```

**Ejemplo: Commit-msg hook para validar formato**

```bash
#!/bin/bash
# .git/hooks/commit-msg

commit_msg=$(cat "$1")

# Validar que el mensaje sigue conventional commits
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .{10,}"; then
    echo "❌ El mensaje debe seguir el formato: tipo(scope): descripción"
    echo "Ejemplo: feat(auth): añadir login con OAuth"
    exit 1
fi

exit 0
```

**Hooks del lado del servidor (GitLab):**

En GitLab, los hooks del servidor se configuran en el servidor GitLab, no en el repositorio local. Se ejecutan en eventos como `pre-receive`, `update`, `post-receive`.

**Habilitar hooks:**

```bash
# Los hooks deben ser ejecutables
chmod +x .git/hooks/pre-commit

# Git ejecutará automáticamente los hooks en los eventos correspondientes
```

**Herramientas para gestionar hooks:**

- **Husky** (Node.js): Facilita gestión de hooks en proyectos JavaScript
- **pre-commit** (Python): Framework para gestionar hooks multi-lenguaje
- **Git hooks manager**: Herramientas para versionar hooks en el repositorio

### 3.5. Git rerere: Reutilizar resoluciones de conflictos

**Concepto:**

`rerere` (Reuse Recorded Resolution) guarda cómo resolviste conflictos anteriormente y los reutiliza automáticamente si el mismo conflicto aparece de nuevo. Muy útil en rebases repetitivos o cuando actualizas feature branches frecuentemente.

**Habilitar rerere:**

```bash
# Habilitar globalmente
git config --global rerere.enabled true

# Habilitar solo en este repositorio
git config rerere.enabled true
```

**Cómo funciona:**

1. Resuelves un conflicto manualmente
2. Git guarda la resolución
3. Si el mismo conflicto aparece de nuevo, Git lo resuelve automáticamente

**Uso práctico:**

```bash
# Situación: Actualizas tu feature branch frecuentemente con rebase
git checkout feature-login
git rebase main

# Si hay conflictos, resuélvelos manualmente
# ... editar archivos ...
git add <archivos-resueltos>
git rebase --continue

# La próxima vez que hagas rebase y aparezca el mismo conflicto,
# Git lo resolverá automáticamente usando la resolución guardada
```

**Ver resoluciones guardadas:**

```bash
# Ver todas las resoluciones guardadas
git rerere diff

# Limpiar resoluciones guardadas
git rerere clear
```

**Casos de uso:**

- Feature branches que se actualizan frecuentemente con rebase
- Mantener múltiples ramas de release actualizadas
- Rebases interactivos donde los mismos conflictos aparecen

### 3.6. Git blame y annotate: Rastrear autoría

**Concepto:**

`git blame` muestra qué commit y autor es responsable de cada línea de un archivo. `git annotate` es similar pero con formato diferente.

**Uso básico:**

```bash
# Ver blame de un archivo
git blame archivo.py

# Ver blame de líneas específicas
git blame -L 10,20 archivo.py    # Líneas 10 a 20
git blame -L 10,+5 archivo.py   # Línea 10 y siguientes 5

# Ver blame ignorando whitespace
git blame -w archivo.py

# Ver blame con más contexto del commit
git blame -C archivo.py         # Detecta código movido/copiado
```

**Salida típica:**

```
^abc1234 (Juan Pérez   2024-01-15 10:30:15 +0100  1) def funcion():
^abc1234 (Juan Pérez   2024-01-15 10:30:15 +0100  2)     return True
def5678  (María García 2024-01-20 14:22:10 +0100  3) 
def5678  (María García 2024-01-20 14:22:10 +0100  4) def otra_funcion():
ghi9012  (Pedro López  2024-01-25 09:15:30 +0100  5)     pass
```

**Annotate (formato alternativo):**

```bash
git annotate archivo.py
```

**Integración con editores:**

- **VSCode:** Extensión GitLens muestra blame inline
- **Vim:** Plugin vim-fugitive
- **IDE:** La mayoría de IDEs modernos tienen integración de blame

**Casos de uso:**

- Encontrar quién escribió código problemático
- Entender el contexto histórico de una línea
- Revisar cambios antes de refactorizar
- Atribución de código en revisiones

### 3.7. Configuraciones avanzadas y aliases

**Aliases personalizados:**

Los aliases permiten crear atajos para comandos frecuentes:

```bash
# Crear alias simple
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit

# Ahora puedes usar:
git st    # en lugar de git status
git co main    # en lugar de git checkout main

# Alias con parámetros (usando ! para shell)
git config --global alias.unstage '!git reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'

# Alias complejos
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# Ver todos los aliases
git config --global --get-regexp alias
```

**Aliases útiles recomendados:**

```bash
# Historial visual mejorado
git config --global alias.lg "log --oneline --graph --decorate --all --graph"

# Historial con estadísticas
git config --global alias.ll "log --stat --abbrev-commit"

# Ver cambios no staged
git config --global alias.dc "diff --cached"

# Ver último commit
git config --global alias.last "log -1 --stat"

# Deshacer último commit pero mantener cambios
git config --global alias.undo "reset HEAD~1"

# Ver ramas con última actividad
git config --global alias.recent "branch --sort=-committerdate"
```

**Configuraciones avanzadas:**

```bash
# Colores en output
git config --global color.ui auto
git config --global color.branch auto
git config --global color.diff auto

# Editor por defecto
git config --global core.editor "code --wait"    # VSCode
git config --global core.editor vim
git config --global core.editor "nano -w"

# Pager personalizado
git config --global core.pager "less -FRX"

# Autocorrección de comandos
git config --global help.autocorrect 1

# Configuración de merge
git config --global merge.tool vimdiff
git config --global merge.conflictstyle diff3    # Muestra versión común

# Configuración de push
git config --global push.default simple         # Push solo a upstream
git config --global push.followTags true        # Push tags automáticamente

# Configuración de pull
git config --global pull.rebase false           # Usar merge por defecto
git config --global pull.rebase true            # Usar rebase por defecto

# Autocrlf (line endings)
git config --global core.autocrlf input         # Linux/Mac
git config --global core.autocrlf true          # Windows

# Ignorar cambios de permisos
git config --global core.fileMode false

# Configuración de credenciales
git config --global credential.helper store     # Guardar credenciales
git config --global credential.helper cache     # Cache temporal (15 min)
```

**Ver y editar configuración:**

```bash
# Ver toda la configuración
git config --list
git config --global --list

# Ver valor específico
git config user.name
git config --global user.email

# Editar configuración manualmente
git config --global --edit    # Abre editor con .gitconfig
```

### 3.8. Git attributes y filtros personalizados

**Concepto:**

`.gitattributes` permite especificar atributos especiales para archivos o patrones. Útil para controlar cómo Git maneja archivos específicos.

**Ubicación:**

`.gitattributes` en la raíz del repositorio (similar a `.gitignore`).

**Atributos comunes:**

```gitattributes
# Forzar line endings
*.py text eol=lf
*.sh text eol=lf
*.bat text eol=crlf

# Archivos binarios
*.png binary
*.jpg binary
*.pdf binary

# Diferencias para archivos específicos
*.java diff=java
*.sql diff=sql

# Filtrar archivos (comprimir, normalizar, etc.)
*.ps1 filter=lf-to-crlf

# Merge strategy
database.xml merge=ours          # Siempre mantener nuestra versión
config.local merge=ours

# Export ignore (no incluir en archivos exportados)
docs/internal/ export-ignore
```

**Filtros personalizados:**

Los filtros permiten transformar archivos automáticamente al hacer commit/checkout:

```bash
# Configurar filtro (en .git/config o global)
git config filter.lf-to-crlf.clean "sed 's/$/\\r/'"
git config filter.lf-to-crlf.smudge "sed 's/\\r$//'"

# Usar en .gitattributes
*.txt filter=lf-to-crlf
```

**Ejemplo: Normalizar whitespace**

```gitattributes
# .gitattributes
*.py text
*.js text
*.md text
*.txt text

# Normalizar todos los archivos de texto
* text=auto
```

**Casos de uso:**

- Normalizar line endings entre sistemas
- Marcar archivos como binarios para evitar diffs inútiles
- Aplicar filtros de formateo automático
- Controlar estrategias de merge por archivo

### 3.9. Git LFS: Archivos grandes

**Concepto:**

Git LFS (Large File Storage) permite versionar archivos grandes (binarios, videos, modelos ML, etc.) sin que Git los almacene directamente en el repositorio. En su lugar, almacena punteros y los archivos reales en un servidor LFS.

**Instalación:**

```bash
# Linux
sudo apt-get install git-lfs    # Debian/Ubuntu
sudo yum install git-lfs        # RHEL/CentOS

# macOS
brew install git-lfs

# Windows
# Descargar desde https://git-lfs.github.com/
```

**Configuración inicial:**

```bash
# Inicializar Git LFS en el repositorio
git lfs install

# Esto modifica .git/config y añade hooks
```

**Usar Git LFS:**

```bash
# Rastrear tipos de archivo específicos
git lfs track "*.psd"           # Archivos Photoshop
git lfs track "*.mp4"           # Videos
git lfs track "*.zip"           # Archivos comprimidos
git lfs track "*.model"         # Modelos de ML

# Rastrear por tamaño (archivos > 100MB)
git lfs track "*.{zip,tar,gz}" --lockable

# Ver qué se está rastreando
git lfs track

# Añadir .gitattributes al repositorio
git add .gitattributes
git commit -m "Configurar Git LFS"
```

**Trabajar con archivos LFS:**

```bash
# Los comandos normales de Git funcionan igual
git add video.mp4
git commit -m "Añadir video"
git push origin main

# Git LFS maneja automáticamente la subida de archivos grandes
```

**Verificar archivos LFS:**

```bash
# Ver archivos LFS en el repositorio
git lfs ls-files

# Ver detalles de un archivo LFS
git lfs pointer --file=video.mp4

# Migrar archivos existentes a LFS
git lfs migrate import --include="*.mp4" --everything
```

**GitLab y LFS:**

GitLab tiene soporte nativo para Git LFS. Los archivos se almacenan en el servidor GitLab.

**Limitaciones y consideraciones:**

- Los archivos LFS no se clonan por defecto (solo punteros)
- Usar `git lfs pull` para descargar archivos LFS
- Puede tener costos adicionales en servicios cloud
- No todos los servidores Git soportan LFS

### 3.10. Otras herramientas avanzadas

**Git bundle: Crear repositorio portátil**

```bash
# Crear bundle con todo el historial
git bundle create repo.bundle --all

# Crear bundle de una rama específica
git bundle create feature.bundle feature-branch

# Clonar desde bundle
git clone repo.bundle proyecto

# Útil para: Transferir repos sin servidor, backups, trabajo offline
```

**Git notes: Añadir metadatos a commits**

```bash
# Añadir nota a un commit
git notes add -m "Revisado por QA" <hash>

# Ver notas
git notes show <hash>
git log --show-notes

# Útil para: Comentarios de revisión, metadatos, información adicional
```

**Git replace: Reemplazar objetos**

```bash
# Reemplazar un commit (útil para historial alternativo)
git replace <hash-viejo> <hash-nuevo>

# Ver reemplazos
git replace -l
```

**Git shallow clone: Clonar sin historial completo**

```bash
# Clonar solo los últimos N commits
git clone --depth 1 <url>        # Solo último commit
git clone --depth 10 <url>       # Últimos 10 commits

# Útil para: Repositorios muy grandes, CI/CD, builds rápidos
```

**Git sparse-checkout: Checkout parcial**

```bash
# Habilitar sparse-checkout
git sparse-checkout init --cone

# Especificar directorios a incluir
git sparse-checkout set src/ tests/

# Útil para: Repositorios monorepo, trabajar solo con partes específicas
```

**Git submodule avanzado:**

```bash
# Actualizar todos los submódulos recursivamente
git submodule update --init --recursive

# Ejecutar comando en todos los submódulos
git submodule foreach 'git pull origin main'

# Sincronizar submódulos con remoto
git submodule sync
```

**Git credential helpers:**

```bash
# Configurar helper para diferentes protocolos
git config --global credential.https://gitlab.com.helper store
git config --global credential.helper 'cache --timeout=3600'

# Usar tokens de acceso personal (PAT) en lugar de contraseñas
```

---

## 4. Ejercicios prácticos avanzados

### 4.1. Ejercicio 1: Recuperación de commits perdidos (20 minutos)

**Objetivo:** Practicar el uso de `reflog` y `reset` para recuperar trabajo perdido.

**Escenario:** Has eliminado accidentalmente varios commits importantes usando `git reset --hard HEAD~3`. Necesitas recuperarlos.

**Pasos:**

1. **Preparación inicial:**

```bash
# Crear directorio de trabajo
mkdir ejercicio-recuperacion
cd ejercicio-recuperacion
git init

# Crear varios commits importantes
echo "# Proyecto importante" > README.md
git add README.md
git commit -m "Commit 1: README inicial"

echo "def funcion_importante():" > codigo.py
echo "    return 'resultado'" >> codigo.py
git add codigo.py
git commit -m "Commit 2: Función importante"

echo "# Documentación" > docs.md
git add docs.md
git commit -m "Commit 3: Documentación"

echo "print('Hola')" >> codigo.py
git add codigo.py
git commit -m "Commit 4: Mejora código"

# Verificar historial
git log --oneline
```

2. **Simular pérdida accidental:**

```bash
# ⚠️ Esto elimina los últimos 3 commits
git reset --hard HEAD~3

# Verificar que se perdieron
git log --oneline
# Solo deberías ver el commit 1
```

3. **Recuperar usando reflog:**

```bash
# Ver el historial de movimientos
git reflog

# Identificar el hash del commit que quieres recuperar
# Busca algo como: "HEAD@{1}: commit: Commit 4: Mejora código"

# Opción A: Restaurar a ese punto exacto
git reset --hard HEAD@{1}       # Ajusta el número según tu reflog

# Opción B: Crear nueva rama desde ese punto (más seguro)
git checkout -b recuperado HEAD@{1}

# Verificar que se recuperó
git log --oneline
ls -la                          # Verificar que los archivos están de vuelta
```

4. **Verificación y limpieza:**

```bash
# Si usaste la opción B, fusionar la rama recuperada
git checkout main
git merge recuperado
git branch -d recuperado

# Verificar estado final
git log --oneline --graph
git status
```

**Preguntas de reflexión:**

- ¿Qué información te da `git reflog` que no ves en `git log`?
- ¿Por qué es importante el reflog para la recuperación?
- ¿Cuál es la diferencia entre `git reset --hard` y `git reset --soft` en este contexto?

---

### 4.2. Ejercicio 2: Reorganización completa de historial (25 minutos)

**Objetivo:** Dominar `rebase interactivo` para limpiar y reorganizar commits antes de hacer push.

**Escenario:** Has hecho varios commits con mensajes pobres y algunos commits duplicados. Quieres limpiar el historial antes de compartirlo.

**Pasos:**

1. **Preparación - Crear historial desordenado:**

```bash
mkdir ejercicio-rebase
cd ejercicio-rebase
git init

# Crear commits con mensajes pobres y cambios mezclados
echo "print('hola')" > app.py
git add app.py
git commit -m "cambios"

echo "def suma(a, b):" >> app.py
echo "    return a + b" >> app.py
git add app.py
git commit -m "mas cambios"

echo "# Tests" > test.py
git add test.py
git commit -m "test"

echo "def test_suma():" >> test.py
echo "    assert suma(2, 3) == 5" >> test.py
git add test.py
git commit -m "test2"

echo "print('mundo')" >> app.py
git add app.py
git commit -m "fix"

# Ver historial actual
git log --oneline
```

2. **Rebase interactivo - Limpiar historial:**

```bash
# Iniciar rebase interactivo de los últimos 5 commits
git rebase -i HEAD~5
```

3. **En el editor que se abre, reorganizar:**

**Estado inicial (malo):**
```
pick abc1234 cambios
pick def5678 mas cambios
pick ghi9012 test
pick jkl3456 test2
pick mno7890 fix
```

**Cambiar a (bueno):**
```
reword abc1234 cambios              # Cambiar mensaje
squash def5678 mas cambios          # Combinar con el anterior
pick ghi9012 test                   # Mantener pero cambiar mensaje
squash jkl3456 test2                # Combinar con test
reword mno7890 fix                  # Cambiar mensaje
```

4. **Guardar y seguir las instrucciones:**

- Git abrirá editores para:
  - Cambiar mensaje del commit 1
  - Combinar mensajes de commits 1 y 2 (dejar solo uno bueno)
  - Cambiar mensaje del commit de test
  - Combinar mensajes de commits de test
  - Cambiar mensaje del último commit

**Mensajes sugeridos:**
- Commit 1+2: "Implementar función suma en app.py"
- Commit test: "Añadir tests para función suma"
- Commit fix: "Añadir print de saludo en app.py"

5. **Verificar resultado:**

```bash
# Ver historial limpio
git log --oneline

# Verificar que el código sigue igual
cat app.py
cat test.py

# Ver diferencias
git log --stat
```

6. **Ejercicio adicional - Reordenar commits:**

```bash
# Si quieres mover el commit de test antes del de app
git rebase -i HEAD~3

# En el editor, simplemente reordena las líneas:
# pick <hash-test> Añadir tests...
# pick <hash-app> Implementar función...
# pick <hash-print> Añadir print...
```

**Preguntas de reflexión:**

- ¿Cuándo es apropiado usar `squash` vs `fixup`?
- ¿Por qué es importante limpiar el historial antes de hacer push?
- ¿Qué pasa si haces rebase de commits que ya están en el remoto?

---

### 4.3. Ejercicio 3: Resolución de conflictos complejos (20 minutos)

**Objetivo:** Practicar resolución de conflictos en múltiples archivos y situaciones complejas.

**Escenario:** Dos desarrolladores han trabajado en la misma funcionalidad en ramas diferentes, causando conflictos en varios archivos.

**Pasos:**

1. **Preparación - Crear ramas con conflictos:**

```bash
mkdir ejercicio-conflictos
cd ejercicio-conflictos
git init

# Commit inicial
echo "# Mi Proyecto" > README.md
echo "def main():" > app.py
echo "    pass" >> app.py
git add .
git commit -m "Commit inicial"

# Crear rama feature-A
git checkout -b feature-A

echo "def funcion_a():" >> app.py
echo "    return 'A'" >> app.py
echo "" >> app.py
echo "def helper():" >> app.py
echo "    return 'helper A'" >> app.py
git add app.py
git commit -m "Feature A: añadir función A"

echo "# Documentación A" > docs.md
git add docs.md
git commit -m "Feature A: documentación"

# Volver a main y crear feature-B
git checkout main
git checkout -b feature-B

echo "def funcion_b():" >> app.py
echo "    return 'B'" >> app.py
echo "" >> app.py
echo "def helper():" >> app.py
echo "    return 'helper B'" >> app.py
git add app.py
git commit -m "Feature B: añadir función B"

echo "# Documentación B" > docs.md
git add docs.md
git commit -m "Feature B: documentación"
```

2. **Intentar merge - Generar conflictos:**

```bash
# Fusionar feature-A en feature-B
git checkout feature-B
git merge feature-A

# Git debería indicar conflictos
git status
```

3. **Resolver conflictos manualmente:**

**Archivo app.py tendrá conflictos:**

```python
def main():
    pass
<<<<<<< HEAD
def funcion_b():
    return 'B'

def helper():
    return 'helper B'
=======
def funcion_a():
    return 'A'

def helper():
    return 'helper A'
>>>>>>> feature-A
```

**Resolver combinando ambas funciones:**

```python
def main():
    pass

def funcion_a():
    return 'A'

def funcion_b():
    return 'B'

def helper():
    return 'helper común'  # Decidir versión o combinar
```

**Archivo docs.md también tendrá conflictos:**

```
<<<<<<< HEAD
# Documentación B
=======
# Documentación A
>>>>>>> feature-A
```

**Resolver combinando:**

```
# Documentación

## Feature A
Contenido de A

## Feature B
Contenido de B
```

4. **Completar la resolución:**

```bash
# Marcar archivos como resueltos
git add app.py
git add docs.md

# Completar el merge
git commit

# O usar el mensaje por defecto
git commit -m "Merge feature-A into feature-B: resolver conflictos"
```

5. **Verificar resultado:**

```bash
# Ver historial
git log --oneline --graph --all

# Ver contenido final
cat app.py
cat docs.md

# Verificar que no hay conflictos pendientes
git status
```

6. **Ejercicio adicional - Resolver conflictos durante rebase:**

```bash
# Crear otra situación
git checkout -b feature-C
echo "nueva_linea = 42" >> app.py
git add app.py
git commit -m "Feature C: nueva línea"

git checkout feature-B
git rebase feature-C

# Si hay conflictos durante rebase:
# 1. Resolver manualmente
# 2. git add <archivos>
# 3. git rebase --continue
```

**Preguntas de reflexión:**

- ¿Cuál es la diferencia entre conflictos en merge vs rebase?
- ¿Cómo decides qué código mantener cuando hay conflictos?
- ¿Qué herramientas de VSCode te ayudan a resolver conflictos?

---

### 4.4. Ejercicio 4: Flujo colaborativo completo (25 minutos)

**Objetivo:** Simular un flujo de trabajo colaborativo completo con múltiples desarrolladores.

**Escenario:** Trabajas en un equipo. Necesitas crear una feature, trabajar en paralelo con otros, resolver conflictos y hacer merge request.

**Pasos:**

1. **Configurar repositorio "remoto" local (simular GitLab):**

```bash
# Crear repositorio "servidor" (simula GitLab)
mkdir proyecto-server
cd proyecto-server
git init --bare
cd ..

# Clonar como desarrollador 1
git clone proyecto-server proyecto-dev1
cd proyecto-dev1

# Commit inicial
echo "# Proyecto Colaborativo" > README.md
echo "version = '1.0.0'" > config.py
git add .
git commit -m "Commit inicial del proyecto"
git push origin main
```

2. **Desarrollador 1 - Crear feature:**

```bash
# En proyecto-dev1
git checkout -b feature-login

echo "def login(username, password):" > auth.py
echo "    # Implementación login" >> auth.py
echo "    return True" >> auth.py
git add auth.py
git commit -m "Implementar función de login"

echo "def logout():" >> auth.py
echo "    # Implementación logout" >> auth.py
git add auth.py
git commit -m "Añadir función de logout"

# Simular que otro dev ya actualizó main
cd ../proyecto-server
# (no hacer nada, el "servidor" ya tiene main)

cd ../proyecto-dev1
```

3. **Desarrollador 2 - Trabajar en paralelo:**

```bash
# Clonar como desarrollador 2 (en otra terminal/directorio)
cd ..
git clone proyecto-server proyecto-dev2
cd proyecto-dev2

# Crear otra feature
git checkout -b feature-dashboard

echo "def get_stats():" > dashboard.py
echo "    return {'users': 100}" >> dashboard.py
git add dashboard.py
git commit -m "Crear dashboard básico"

# Actualizar main local
git checkout main
git pull origin main

# Volver a feature y continuar
git checkout feature-dashboard
echo "def render_chart():" >> dashboard.py
echo "    pass" >> dashboard.py
git add dashboard.py
git commit -m "Añadir renderizado de gráficos"
```

4. **Desarrollador 1 - Actualizar y resolver conflictos:**

```bash
# Volver a proyecto-dev1
cd ../proyecto-dev1

# Antes de hacer push, actualizar main
git checkout main
git pull origin main

# Actualizar feature desde main (rebase para mantener historial limpio)
git checkout feature-login
git rebase main

# Si hay conflictos, resolverlos
# (en este caso no debería haber, pero es buena práctica)

# Push de la feature
git push -u origin feature-login
```

5. **Simular merge en GitLab (fusionar manualmente):**

```bash
# Simular que se aprueba el merge request
cd ../proyecto-server
# (en GitLab esto se haría desde la interfaz web)

# Desde proyecto-dev1, fusionar a main
cd ../proyecto-dev1
git checkout main
git merge feature-login
git push origin main
git branch -d feature-login
```

6. **Desarrollador 2 - Actualizar y resolver conflictos:**

```bash
# Desarrollador 2 necesita actualizar su main
cd ../proyecto-dev2
git checkout main
git pull origin main

# Ahora su feature está desactualizada
git checkout feature-dashboard

# Opción A: Rebase sobre main actualizado
git rebase main

# Opción B: Merge main en feature
# git merge main

# Si hay conflictos (por ejemplo, si ambos modificaron README):
# 1. Resolver conflictos
# 2. git add <archivos>
# 3. git rebase --continue (si usaste rebase)
#    o git commit (si usaste merge)

# Continuar trabajando
echo "# Actualización" >> README.md
git add README.md
git commit -m "Actualizar README con nuevas features"
```

7. **Stash - Guardar trabajo temporal:**

```bash
# Simular que necesitas cambiar de contexto urgentemente
# Tienes cambios sin commitear en feature-dashboard
echo "cambio_temporal = True" >> dashboard.py

# Guardar temporalmente
git stash -m "WIP: cambios temporales en dashboard"

# Cambiar a main para hotfix
git checkout main
echo "hotfix = True" > hotfix.py
git add hotfix.py
git commit -m "Hotfix crítico"
git push origin main

# Volver a feature y recuperar cambios
git checkout feature-dashboard
git stash pop
```

8. **Verificación final:**

```bash
# Ver estado de todas las ramas
cd ../proyecto-dev1
git fetch --all
git log --oneline --graph --all

cd ../proyecto-dev2
git fetch --all
git log --oneline --graph --all
```

**Preguntas de reflexión:**

- ¿Cuándo usar `rebase` vs `merge` al actualizar una feature branch?
- ¿Por qué es importante hacer `pull` antes de crear nuevas features?
- ¿Qué ventajas tiene usar `stash` en lugar de hacer commits temporales?

---

### 4.5. Ejercicio 5: Cherry-pick y bisect (25 minutos)

**Objetivo:** Dominar cherry-pick para aplicar commits selectivos y usar bisect para encontrar bugs.

**Parte A: Cherry-pick (15 minutos)**

**Escenario:** Tienes un bugfix en una rama de feature que necesitas aplicar urgentemente a main sin fusionar toda la feature.

**Pasos:**

1. **Preparación - Crear escenario:**

```bash
mkdir ejercicio-cherry-pick
cd ejercicio-cherry-pick
git init

# Crear commit inicial en main
echo "# Proyecto" > README.md
git add README.md
git commit -m "Commit inicial"

# Crear feature branch con varios commits
git checkout -b feature-completa

echo "def funcion_a():" > app.py
echo "    return 'A'" >> app.py
git add app.py
git commit -m "Feature: añadir función A"

echo "def funcion_b():" >> app.py
echo "    return 'B'" >> app.py
git add app.py
git commit -m "Feature: añadir función B"

# Este es el bugfix que necesitamos en main
echo "# Bugfix crítico" > fix.txt
echo "correccion = True" >> fix.txt
git add fix.txt
git commit -m "Bugfix: corrección crítica de seguridad"

echo "def funcion_c():" >> app.py
echo "    return 'C'" >> app.py
git add app.py
git commit -m "Feature: añadir función C"

# Ver historial
git log --oneline
```

2. **Identificar el commit del bugfix:**

```bash
# Ver el hash del commit del bugfix
git log --oneline

# Anotar el hash del commit "Bugfix: corrección crítica"
# Ejemplo: abc1234
```

3. **Aplicar cherry-pick a main:**

```bash
# Volver a main
git checkout main

# Aplicar solo el commit del bugfix
git cherry-pick <hash-bugfix>

# Verificar que se aplicó
git log --oneline
cat fix.txt
```

4. **Cherry-pick con conflictos (simular):**

```bash
# Modificar main para crear conflicto
echo "modificacion = True" >> fix.txt
git add fix.txt
git commit -m "Modificar fix en main"

# Intentar cherry-pick de nuevo (simular)
# git cherry-pick <hash-bugfix>  # Esto fallaría si el commit ya está aplicado

# En caso real de conflicto:
# 1. Resolver conflictos manualmente
# 2. git add <archivos-resueltos>
# 3. git cherry-pick --continue
```

5. **Cherry-pick múltiples commits:**

```bash
# Aplicar rango de commits
git cherry-pick <hash-inicio>^..<hash-fin>

# O commits específicos
git cherry-pick <hash1> <hash2> <hash3>
```

**Parte B: Git bisect (10 minutos)**

**Escenario:** El código funciona en v1.0 pero no en HEAD. Necesitas encontrar qué commit introdujo el bug.

**Pasos:**

1. **Preparar escenario con bug:**

```bash
# Crear nuevo repositorio
cd ..
mkdir ejercicio-bisect
cd ejercicio-bisect
git init

# Crear versión funcional
echo "def suma(a, b):" > calc.py
echo "    return a + b" >> calc.py
git add calc.py
git commit -m "v1.0: función suma funcional"
git tag v1.0

# Crear varios commits
echo "def resta(a, b):" >> calc.py
echo "    return a - b" >> calc.py
git add calc.py
git commit -m "Añadir función resta"

echo "def multiplica(a, b):" >> calc.py
echo "    return a * b" >> calc.py
git add calc.py
git commit -m "Añadir función multiplica"

# Este commit introduce el bug
echo "def divide(a, b):" >> calc.py
echo "    return a / 0  # BUG: división por cero" >> calc.py
git add calc.py
git commit -m "Añadir función divide"

echo "def potencia(a, b):" >> calc.py
echo "    return a ** b" >> calc.py
git add calc.py
git commit -m "Añadir función potencia"
```

2. **Usar bisect para encontrar el bug:**

```bash
# Iniciar bisect
git bisect start

# Marcar HEAD como malo (tiene el bug)
git bisect bad

# Marcar v1.0 como bueno (funciona)
git bisect good v1.0

# Git te llevará a un commit intermedio
# Crear script de prueba
cat > test_calc.sh << 'EOF'
#!/bin/bash
python3 -c "from calc import divide; divide(10, 2)" 2>/dev/null
if [ $? -eq 0 ]; then
    exit 0  # good
else
    exit 1  # bad
fi
EOF
chmod +x test_calc.sh

# Probar manualmente o usar el script
./test_calc.sh

# Si falla (tiene el bug):
git bisect bad

# Si funciona:
git bisect good

# Continuar hasta que Git identifique el commit problemático
# Git mostrará: "<hash> is the first bad commit"
```

3. **Bisect automático:**

```bash
# Reiniciar bisect
git bisect reset
git bisect start HEAD v1.0

# Ejecutar bisect automáticamente
git bisect run ./test_calc.sh

# Git ejecutará el script en cada commit y encontrará el bug automáticamente
```

4. **Verificar y finalizar:**

```bash
# Ver el commit problemático
git show

# Ver detalles
git log --oneline

# Finalizar bisect
git bisect reset
```

**Preguntas de reflexión:**

- ¿Cuándo es mejor usar cherry-pick vs merge de una rama completa?
- ¿Qué ventajas tiene bisect sobre revisar commits manualmente?
- ¿Cómo puedes automatizar bisect para bugs complejos?

---

## 5. Casos de uso reales

### 5.1. Escenario: Migración de feature branch

**Situación:** Has estado trabajando en una feature branch durante una semana. Mientras tanto, `main` ha avanzado significativamente. Necesitas integrar los cambios de `main` en tu feature antes de hacer el merge request.

**Solución recomendada:**

```bash
# 1. Asegurar que main está actualizado
git checkout main
git pull origin main

# 2. Volver a tu feature
git checkout mi-feature

# 3. Rebase sobre main (mantiene historial limpio)
git rebase main

# 4. Si hay conflictos, resolverlos
# ... editar archivos ...
git add <archivos-resueltos>
git rebase --continue

# 5. Force push (porque rebase reescribe historial)
git push --force-with-lease origin mi-feature
```

**⚠️ Nota:** `--force-with-lease` es más seguro que `--force` porque falla si el remoto ha cambiado desde tu último fetch.

### 5.2. Escenario: Limpieza de historial antes de merge

**Situación:** Tu feature branch tiene 15 commits con mensajes como "fix", "otro fix", "cambios", etc. Quieres limpiar el historial antes de crear el merge request.

**Solución:**

```bash
# 1. Rebase interactivo de todos los commits de la feature
git rebase -i main
# O si quieres los últimos N commits:
git rebase -i HEAD~15

# 2. En el editor:
# - Cambiar "pick" a "reword" para mejorar mensajes
# - Usar "squash" o "fixup" para combinar commits relacionados
# - Reordenar si es necesario

# 3. Seguir las instrucciones de Git para cada cambio

# 4. Verificar resultado
git log --oneline

# 5. Force push
git push --force-with-lease origin mi-feature
```

**Resultado:** Un historial limpio con commits lógicos y mensajes descriptivos.

### 5.3. Escenario: Recuperación tras error crítico

**Situación:** Has hecho `git reset --hard` por error y perdiste commits importantes. O peor aún, has hecho `git push --force` y reescrito el historial remoto incorrectamente.

**Solución - Para commits locales:**

```bash
# 1. NO hacer nada más (no hacer más commits)
# 2. Ver reflog
git reflog

# 3. Identificar el commit perdido
# Buscar algo como: "HEAD@{5}: commit: Feature importante"

# 4. Recuperar
git checkout -b recuperacion HEAD@{5}

# 5. Verificar que está todo
git log
ls -la

# 6. Fusionar de vuelta
git checkout main
git merge recuperacion
```

**Solución - Para historial remoto reescrito:**

```bash
# 1. Si otros ya han clonado, avisarles
# 2. Intentar recuperar desde reflog local
git reflog

# 3. Si no tienes reflog, intentar desde otros desarrolladores
# Ellos pueden hacer:
git fetch origin
git log origin/main
git checkout -b recuperacion <hash-perdido>

# 4. Una vez recuperado, hacer push cuidadoso
git push origin recuperacion:main --force-with-lease
```

**Prevención:** Siempre usar `--force-with-lease` en lugar de `--force`.

---

## 5. Checklist de buenas prácticas

### Antes de hacer commit

- [ ] Revisar cambios con `git status` y `git diff`
- [ ] Asegurar que los mensajes de commit son descriptivos
- [ ] No commitear archivos temporales, logs, o archivos de configuración local
- [ ] Verificar que no hay código comentado innecesario

### Antes de hacer push

- [ ] Actualizar rama local desde remoto (`git pull` o `git fetch + merge`)
- [ ] Resolver conflictos si los hay
- [ ] Ejecutar tests localmente
- [ ] Revisar historial con `git log --oneline`
- [ ] Si usas `--force`, preferir `--force-with-lease`

### Gestión de ramas

- [ ] Una rama por feature/bugfix
- [ ] Nombres descriptivos: `feature-login`, `bugfix-crash`, `hotfix-security`
- [ ] Eliminar ramas fusionadas: `git branch -d <rama>`
- [ ] Mantener `main`/`master` siempre estable

### Trabajo colaborativo

- [ ] Comunicar con el equipo antes de hacer `--force` push
- [ ] Hacer merge requests pequeños y enfocados
- [ ] Revisar código de otros antes de aprobar merge requests
- [ ] Actualizar documentación si cambias funcionalidades importantes

### Resolución de problemas

- [ ] Usar `git reflog` antes de desesperar
- [ ] Crear ramas de recuperación antes de hacer cambios destructivos
- [ ] Documentar pasos si resuelves un problema complejo
- [ ] Pedir ayuda si no estás seguro de un comando destructivo

### Comandos peligrosos (usar con precaución)

```bash
git reset --hard          # Descarta cambios permanentemente
git push --force          # Reescribe historial remoto
git clean -fd             # Elimina archivos no rastreados
git rebase <rama-publica> # Reescribe historial compartido
```

**Regla de oro:** Si un comando puede perder trabajo, verifica con `git status` y `git log` antes y después.

---

## Resumen final

En esta sesión has repasado:

1. **Fundamentos:** Git vs SVN, flujo básico, ramas y fusiones, trabajo colaborativo
2. **Técnicas avanzadas:** Reescritura de historial, recuperación, stash, submódulos
3. **Ejercicios prácticos:** Recuperación de commits, reorganización de historial, conflictos complejos, flujo colaborativo
4. **Casos reales:** Migración de features, limpieza de historial, recuperación de errores
5. **Buenas prácticas:** Checklist para trabajo diario seguro y eficiente

**Próximos pasos:**

- Practicar los ejercicios hasta sentirte cómodo
- Experimentar en repositorios de prueba
- Aplicar estas técnicas en proyectos reales
- Continuar con sesiones avanzadas (cherry-pick, Git Flow, CI/CD)

**Recursos adicionales:**

- `git help <comando>` - Ayuda integrada de Git
- `git log --help` - Documentación detallada de cada comando
- GitLab documentation - Guías oficiales de GitLab
- Pro Git book - Libro gratuito y completo sobre Git

---

**¡Felicidades por completar esta sesión de repaso!** 🎉

