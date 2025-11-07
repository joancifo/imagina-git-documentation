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
git branch                 # Listar ramas (la actual tiene *)
git merge <rama>           # Fusionar <rama> en la actual
git rebase <rama>          # Rebase (reaplicar commits sobre <rama>)
git tag <etiqueta>         # Etiquetar un commit (ej. v1.0)
```

## Conflictos

- Git marca los archivos con conflictos entre `<<<<<<< HEAD`, `=======`, y `>>>>>>> rama-secundaria`. Edítalos para resolver.
- Después de resolver: `git add <archivo>` y luego `git commit` completa el merge.
- Para abortar un merge en conflicto: `git merge --abort`.
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
git fetch                              # Descargar historial desde el remoto
git pull origin <rama>                 # Traer y fusionar cambios de remoto
git push origin <rama>                 # Enviar commits locales a remoto
git push -u origin <rama>              # Lo mismo + definir upstream para la rama local
git push --force                       # Forzar push (reescribe remoto; usar con cuidado)
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

---

Cada comando aquí resumido aparece explicado con ejemplos en las sesiones anteriores. Úsala como referencia rápida al trabajar en la terminal.
