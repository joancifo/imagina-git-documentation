# Cheatsheet de comandos Git

## Configuración global

Define tu identidad y preferencias antes de usar Git:

```bash
git config --global user.name "Tu Nombre"        # Nombre de usuario
git config --global user.email "tu@email.com"   # Email
git config --global core.editor vim              # Editor por defecto
git config --global alias.st status             # Alias: st = status
git config --global alias.co checkout           # Alias: co = checkout
git config --global alias.br branch             # Alias: br = branch
git config --global alias.ci commit             # Alias: ci = commit
```

## Flujo básico

Inicialización y ciclo de cambios:

```bash
git init                   # Crear un nuevo repo local
git clone <url>            # Clonar repo remoto
git status                 # Ver estado (archivos modificados)
git add <archivos>         # Preparar cambios (stage)
git commit -m "mensaje"    # Confirmar cambios con mensaje
git log                    # Ver historial de commits
git diff                   # Mostrar diferencias sin confirmar
```

## Ramas (branches)

```bash
git branch <nombre>        # Crear nueva rama
git checkout <nombre>      # Cambiar a otra rama
git checkout -b <nombre>   # Crear + cambiar en un paso
git switch <nombre>        # Cambiar a rama (comando moderno)
git switch -c <nombre>     # Crear + cambiar (comando moderno)
git branch                 # Listar ramas (la actual tiene *)
git branch -a              # Listar todas las ramas (locales y remotas)
git branch -d <nombre>     # Eliminar rama local (si está fusionada)
git branch -D <nombre>     # Forzar eliminación de rama local
git merge <rama>           # Fusionar <rama> en la actual
git rebase <rama>          # Rebase (reaplicar commits sobre <rama>)
git tag <etiqueta>         # Etiquetar un commit (ej. v1.0)
git tag -a <etiqueta> -m "mensaje"  # Crear tag anotado
```

## Estrategias de integración

```bash
# Fast-forward merge (por defecto si es posible)
git merge <rama>              # Fast-forward automático

# Merge commit (preserva historia de ramas)
git merge --no-ff <rama>      # Forzar commit de merge
git config merge.ff false     # Siempre crear merge commits

# Rebase (historial lineal)
git rebase <rama>             # Reaplicar commits sobre <rama>
git config pull.rebase true   # Pull usa rebase en lugar de merge

# Merge squash (aplastar commits en uno)
git merge --squash <rama>     # Combina todos los commits en uno solo
```

## Conflictos

- Git marca los archivos con conflictos entre `<<<<<<< HEAD`, `=======`, y `>>>>>>> rama-secundaria`. Edítalos para resolver.
- Después de resolver: `git add <archivo>` y luego `git commit` completa el merge.
- Para abortar un merge en conflicto: `git merge --abort`.
- Para abortar un rebase: `git rebase --abort`.
- Continuar rebase tras resolver: `git rebase --continue`.
- Las estrategias de merge pueden cambiarse: p.ej. `git merge -s ours <rama>` usa solo los cambios de la rama actual, etc.

## Reescritura de historial

```bash
git commit --amend -m "nuevo mensaje"   # Modificar el último commit
git rebase -i HEAD~<n>                  # Rebase interactivo de los últimos n commits
git reset --soft <hash>                  # Mover HEAD sin tocar el index (guardar cambios)
git reset --hard <hash>                  # Mover HEAD y descartar todos los cambios
git reflog                               # Mostrar registro de movimientos de HEAD
git reset HEAD@{<n>}                     # Restaurar a una entrada del reflog
git revert <hash>                        # Crear un commit inverso que deshace <hash>
```

## Colaboración (remotos)

```bash
git remote add origin <URL>            # Vincular repo local a remoto (ej. GitLab)
git remote -v                          # Ver remotos configurados
git remote show origin                 # Información detallada del remoto
git fetch                              # Descargar historial desde el remoto
git fetch --all                        # Descargar de todos los remotos
git pull origin <rama>                 # Traer y fusionar cambios de remoto
git pull --rebase origin <rama>        # Pull con rebase en lugar de merge
git push origin <rama>                 # Enviar commits locales a remoto
git push -u origin <rama>              # Lo mismo + definir upstream para la rama local
git push --force                       # Forzar push (reescribe remoto; usar con cuidado)
git push --force-with-lease            # Forzar push solo si remoto no cambió (más seguro)
git push --tags                        # Enviar todas las etiquetas locales al remoto
```

## Stash y limpieza

```bash
git stash                             # Guarda cambios no confirmados en un stash
git stash list                        # Ver lista de stashes guardados
git stash pop                         # Aplicar último stash y eliminarlo
git stash apply                       # Aplicar último stash sin borrarlo
git stash drop <stash@{n}>            # Eliminar un stash específico
git clean -fd                         # Borrar archivos no rastreados/directorios no vacíos
git clean -f                          # Borrar archivos no rastreados
git rm --cached <archivo>             # Quitar del próximo commit sin borrar archivo local
```

## Recuperación de errores

Si algo sale mal, las combinaciones de `git reflog` y `git reset` son las herramientas clave para volver a estados previos. Además, para deshacer un commit público, usa `git revert` en lugar de `reset`. Mantén copias de respaldo y no temas experimentar: Git está diseñado para revertir y recuperar cambios de manera segura.

## Técnicas avanzadas

### Cherry-pick

```bash
git cherry-pick <hash>              # Aplicar commit específico a rama actual
git cherry-pick <hash1> <hash2>     # Aplicar múltiples commits
git cherry-pick <inicio>^..<fin>    # Aplicar rango de commits
git cherry-pick -n <hash>           # Aplicar sin hacer commit
git cherry-pick --continue           # Continuar tras resolver conflictos
git cherry-pick --abort              # Abortar cherry-pick
```

### Git bisect

```bash
git bisect start                     # Iniciar sesión de bisect
git bisect bad                        # Marcar commit actual como malo
git bisect good <hash>               # Marcar commit como bueno
git bisect good                       # Marcar commit actual como bueno
git bisect bad                        # Marcar commit actual como malo
git bisect skip                       # Saltar commit (no se puede probar)
git bisect run <script>              # Ejecutar bisect automáticamente
git bisect reset                      # Finalizar bisect y volver al estado inicial
```

### Git worktree

```bash
git worktree add <ruta> <rama>       # Crear worktree con rama específica
git worktree add -b <rama> <ruta>    # Crear worktree con nueva rama
git worktree list                    # Listar todos los worktrees
git worktree remove <ruta>           # Eliminar worktree
git worktree remove --force <ruta>  # Eliminar worktree y directorio
```

### Git blame y annotate

```bash
git blame <archivo>                  # Ver autoría de cada línea
git blame -L 10,20 <archivo>        # Ver líneas específicas
git blame -C <archivo>               # Detectar código movido/copiado
git annotate <archivo>               # Formato alternativo de blame
```

### Submódulos

```bash
git submodule add <url> <ruta>       # Añadir submódulo
git submodule init                   # Inicializar submódulos
git submodule update                 # Actualizar submódulos
git submodule update --init --recursive  # Inicializar y actualizar recursivamente
git clone --recurse-submodules <url>  # Clonar con submódulos
git submodule status                 # Ver estado de submódulos
git submodule sync                   # Sincronizar submódulos con remoto
```

### Git LFS (Large File Storage)

```bash
git lfs install                      # Instalar Git LFS en repositorio
git lfs track "*.mp4"                # Rastrear tipo de archivo
git lfs track "*.{zip,tar}"          # Rastrear múltiples extensiones
git lfs ls-files                     # Ver archivos LFS rastreados
git lfs pull                          # Descargar archivos LFS
git lfs migrate import --include="*.mp4" --everything  # Migrar archivos existentes
```

### Configuraciones avanzadas

```bash
# Configuración de merge
git config merge.ff false            # Siempre crear merge commits
git config merge.ff only            # Solo permitir fast-forward
git config merge.conflictstyle diff3 # Mostrar versión común en conflictos

# Configuración de pull
git config pull.rebase true          # Pull usa rebase en lugar de merge
git config pull.rebase false         # Pull usa merge (por defecto)

# Rerere (reutilizar resoluciones)
git config rerere.enabled true       # Habilitar rerere

# Colores y UI
git config color.ui auto             # Colores automáticos
git config core.editor "code --wait" # Editor VSCode
git config help.autocorrect 1        # Autocorrección de comandos

# Credenciales
git config credential.helper store   # Guardar credenciales
git config credential.helper cache  # Cache temporal (15 min)
```

### Aliases útiles

```bash
# Crear alias
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit

# Alias avanzados
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.ll "log --stat --abbrev-commit"
git config --global alias.last "log -1 --stat"
git config --global alias.undo "reset HEAD~1"
git config --global alias.unstage "reset HEAD --"

# Ver todos los aliases
git config --global --get-regexp alias
```

### Otras herramientas

```bash
# Git bundle (repositorio portátil)
git bundle create repo.bundle --all  # Crear bundle con todo
git clone repo.bundle proyecto        # Clonar desde bundle

# Git notes
git notes add -m "nota" <hash>        # Añadir nota a commit
git notes show <hash>                 # Ver notas de commit

# Shallow clone
git clone --depth 1 <url>             # Clonar solo último commit
git clone --depth 10 <url>            # Clonar últimos 10 commits

# Sparse checkout
git sparse-checkout init --cone       # Habilitar sparse-checkout
git sparse-checkout set src/ tests/   # Especificar directorios
```

---

Cada comando aquí resumido aparece explicado con ejemplos en las sesiones anteriores. Úsala como referencia rápida al trabajar en la terminal.
