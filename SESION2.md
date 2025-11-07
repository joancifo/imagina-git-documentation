# Sesión 2: Flujo de trabajo y VSCode

## Flujo de trabajo colaborativo

En proyectos reales, cada desarrollador crea ramas de trabajo (por ejemplo `feature-login`, `bugfix-xyz`) desde `main`. Trabajan allí, confirman y luego integran su rama de vuelta. Una manera común es realizar Pull Requests o Merge Requests en GitLab: cada cambio es revisado y luego fusionado (merge) a `main`.

Se puede optar por fusiones con commit de merge o fusiones fast-forward sin commit adicional. Atlassian explica que merge normal genera un commit de fusión mientras que la fusión mediante rebase reescribe el historial para mantenerlo lineal. Por ejemplo:

```bash
git merge --no-ff  # fuerza un merge explícito
git merge --squash # aplasta varios commits en uno
```

## Resolución de conflictos

Cuando dos ramas modifican un mismo archivo incompatible, al fusionar Git detendrá el merge y marcará conflictos. En ese caso, los archivos afectados incluirán indicadores especiales como `<<<<<<< HEAD`, `=======`, `>>>>>>> rama-secundaria`. Debes editar el archivo para resolver el conflicto, dejando solo el contenido correcto y borrando esos marcadores. Luego haces:

```bash
git add <archivo-resuelto>
git commit
```

Con lo cual se crea el commit de merge final. Por ejemplo, al intentar:

```bash
git checkout main
git merge otra-rama
```

Git podría responder:

```
Auto-merging archivo.txt
CONFLICT (content): Merge conflict in archivo.txt
Automatic merge failed; fix conflicts and then commit the result.
```

En tu editor o VSCode, abre `archivo.txt`, busca las marcas `<<<<<<<`, etc., elige la versión adecuada (o combina partes), guarda, y sigue los pasos anteriores.

## VSCode y extensiones Git

Visual Studio Code integra Git en su panel de Source Control. Desde allí puedes ver archivos modificados, hacer stage/unstage, y commit con botones. También puedes ejecutar comandos en la terminal integrada. Se recomienda instalar GitLens y Git Graph:

- **GitLens** añade información contextual (quién modificó qué línea y cuándo), historial de archivos, comparación de ramas, etc.
- **Git Graph** muestra un diagrama visual de ramas y commits en tiempo real.

### Ejemplo con VSCode

Abre tu proyecto Git en VSCode. Realiza cambios en un archivo `.js`. En el panel de Git verás tu archivo listado. Haz stage clickeando el símbolo `+`, escribe un mensaje y confirma. Verás los cambios reflejados en el historial de GitLens o Git Graph. Esto facilita el entendimiento del flujo.

## Ejemplos de comandos (terminal)

```bash
git checkout -b feature-login
echo "Nueva funcionalidad" > login.txt
git add login.txt
git commit -m "Inicia feature de login"
```

Salida esperada:

```
[feature-login abc345] Inicia feature de login
 1 file changed, 1 insertion(+)
```

Luego fusiona la rama:

```bash
git checkout main
git merge feature-login
```

Salida esperada:

```
Fast-forward
 login.txt | 1 +
 1 file changed, 1 insertion(+)
```

En este flujo simple no hay conflictos y la rama `feature-login` se fusiona automáticamente (fast-forward) a `main`. Luego puedes borrar la rama local:

```bash
git branch -d feature-login
```

## Ejercicios prácticos (Sesión 2)

- Repite el flujo anterior: crea una rama de característica, haz cambios, confirma, vuelve a main y fusiona. Observa el historial con Git Graph para ver los commits.
- Crea un conflicto intencionado editando el mismo archivo en `main` y en otra rama, luego resuélvelo con VSCode.
- Usa VSCode para clonar un repositorio, explorar archivos, comparar cambios (diff), hacer stage/unstage y commits.
- Explora GitLens para ver historial de un archivo y anotaciones (blame).
