# Sesión 3: Reescritura de historia y recuperación

## Reescribir commits

Git ofrece herramientas para modificar commits ya hechos. Por ejemplo, `git commit --amend` permite editar el último commit: cambia su mensaje o agrega cambios no incluidos. Por ejemplo:

```bash
git commit --amend -m "Mensaje corregido"
```

Esto reemplaza el commit anterior por uno nuevo con el mensaje actualizado. Úsalo solo antes de hacer push para corregir pequeños errores.

Para reordenar o combinar varios commits se usa `git rebase -i`. Esto lanza un editor donde decides acciones sobre commits recientes (pick, squash, fixup, etc.). La rebase puede simplificar el historial. Atlassian describe que la fusión mediante rebase mueve tus commits sobre una base diferente creando nuevos commits[18]. Esto mantiene el historial lineal y limpio.

Ejemplo sencillo de rebase interactivo:

```bash
git rebase -i HEAD~3
```

En el editor que aparece, puedes indicar `pick` o `squash`. Con `squash` combinas commits. Tras guardar, Git reescribe esos commits según tus instrucciones. ¡Ten cuidado: rebase cambia los IDs de commit anteriores!

## Reflog y reset

Git registra todos los cambios de HEAD en el reflog (registro de referencias)[19]. Esto incluye resets, checkouts, commits, rebases, etc. Por ejemplo, `git reflog` muestra un listado de posiciones previas de HEAD:

```bash
git reflog
```

Salida esperada:

```
f2d3a4b (HEAD -> main) HEAD@{0}: commit: Ajuste final
ab12c34 HEAD@{1}: commit: Bugfix en login
3e4f5a6 HEAD@{2}: checkout: moving from feature to main
...
```

Si borraste o perdiste un commit, puedes recuperar su estado mediante `git reset HEAD@{N}`, usando una de las entradas del reflog[20]. Por ejemplo, en [6] se muestra cómo, tras un rebase interactivo, se usó `git reset HEAD@{2}` para restaurar commits eliminados inadvertidamente[20]. En resumen: `git reflog` + `git reset` es la herramienta principal para recuperar commits borrados o cambios antiguos.

## Recuperación con revert

A diferencia de reset (que mueve punteros), `git revert <id>` crea un nuevo commit que deshace los cambios de uno anterior[21]. Esto es útil cuando ya has compartido commits: revert mantiene íntegro el historial. Atlassian explica que revert invierte los cambios introducidos por el commit y genera una confirmación inversa[21]. Así no se borra el historial, evitando inconsistencias. Por ejemplo:

```bash
git revert abc1234
```

crea un nuevo commit que anula `abc1234`. Este enfoque es ideal para deshacer errores en commits públicos sin reescribir la historia compartida.

## Manejo avanzado de merge

Git soporta varias estrategias de fusión. Por defecto usa la estrategia `recursive`, adecuada para la mayoría de merges[22]. Otras estrategias incluyen `ours` (mantiene siempre los cambios de la rama actual, ignorando los de la otra[23]), `theirs` (lo opuesto a ours), o `octopus` (para fusionar múltiples ramas a la vez[24]). Por ejemplo, `git merge -s ours rama1` dejará el contenido de HEAD prevalente sobre `rama1`. También se puede usar `--no-ff` para forzar un commit de merge explícito, o `--squash` para aplastar commits. Estas opciones avanzadas permiten controlar cómo se integra la historia al fusionar.

## Stash (cambios en espera)

Si necesitas cambiar de contexto sin confirmar tus cambios actuales, Git ofrece el stash. El comando `git stash` guarda temporalmente tus cambios (tanto staged como unstaged) y limpia el directorio de trabajo[25]. Por ejemplo:

```bash
git status
```

Salida esperada:

```
On branch main
Changes to be committed:
  modified: index.html
```

```bash
git stash
```

Salida esperada:

```
Saved working directory and index state WIP on main:  abc1234 Nuevo diseño
HEAD is now at abc1234 Nuevo diseño
```

```bash
git status
```

Salida esperada:

```
On branch main
nothing to commit, working tree clean
```

Luego puedes cambiar de rama o hacer otras operaciones. Para recuperar los cambios stasheados se usa `git stash pop` (lo aplica y lo elimina del stash) o `git stash apply` (lo aplica pero mantiene copia en stash). Por ejemplo:

```bash
git stash pop
```

Salida esperada:

```
On branch main
Changes to be committed:
  modified: index.html
Dropped refs/stash@{0} (abc1234)
```

Esto restituye los cambios en tu workspace. (Nota: por defecto no guarda archivos nuevos no rastreados a menos de usar `git stash -u`.)

## Submódulos

Los submódulos permiten incluir un repositorio Git dentro de otro[26]. Esto es útil si dependes de otro proyecto independiente. Un submódulo apunta a un commit específico del repo externo. Para añadir un submódulo:

```bash
git submodule add https://gitlab.miempresa.com/libexterna.git libs/libexterna
```

Salida esperada:

```
Cloning into '/.../libs/libexterna'...
...
```

Git clonará el repo externo dentro de `libs/libexterna` y generará el archivo `.gitmodules`[27]. Al ver `git status` verás nuevos archivos `.gitmodules` y el directorio del submódulo. Ejemplo de `.gitmodules`:

```
[submodule "libs/libexterna"]
    path = libs/libexterna
    url = https://gitlab.miempresa.com/libexterna.git
```

Después `git add .gitmodules libs/libexterna/` y commit.

Al clonar un repositorio con submódulos, se requiere inicializarlos y actualizarlos:

```bash
git clone https://.../proyecto-con-submodulos.git
cd proyecto-con-submodulos
git submodule init
git submodule update
```

Los comandos `git submodule init` y `git submodule update` leen `.gitmodules` y traen los contenidos de cada submódulo[28]. Desde entonces, puedes trabajar en el submódulo como en un repo normal (branches, commits) y luego, desde el repo principal, hacer `git add` y commit para actualizar la referencia al nuevo commit del submódulo. Recuerda: siempre haz push del submódulo (repo externo) y luego push del repo principal, para que los demás vean los cambios correctamente[29].

## Ejercicios prácticos (Sesión 3)

- Crea un repositorio de prueba con varios commits. Usa `git commit --amend` para modificar el mensaje del último commit.
- Ejecuta `git rebase -i HEAD~3` y practica combinar o reordenar commits. Comprueba el historial antes y después con `git log`.
- Introduce un conflicto (dos ediciones incompatibles) y resuélvelo manualmente siguiendo los pasos: editar, `git add`, `git commit`.
- Trabaja sobre cambios no confirmados: modifica archivos y usa `git stash`. Cambia de rama, haz algo y luego `git stash pop` para recuperar tus cambios.
- Crea un submódulo apuntando a otro repo (incluso un repo vacío en GitLab). Añádelo y empújalo a GitLab. Después clona tu repo, inicializa y actualiza el submódulo.
- Borra accidentalmente el último commit con `git reset --hard HEAD~1`, luego usa `git reflog` y `git reset HEAD@{1}` para recuperarlo. Verifica con `git log`.
