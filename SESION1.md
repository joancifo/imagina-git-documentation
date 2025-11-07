# Sesión 1: Introducción a Git y GitLab

## ¿Qué es Git?

Git es un sistema de control de versiones distribuido que registra cambios en archivos y coordina el trabajo de varias personas en proyectos de software. Su función más básica es permitir que un equipo añada y fusione código simultáneamente, lo que hace el desarrollo más eficiente y escalable. Git permite revertir cambios, crear nuevas ramas para características, resolver conflictos de fusión, y mucho más.

## Diferencias entre Git y SVN

Git y SVN (Subversion) son sistemas de control de versiones, pero tienen arquitecturas y filosofías muy diferentes:

### Arquitectura

- **Git (distribuido):** Cada desarrollador tiene una copia completa del repositorio con todo el historial. Puedes trabajar sin conexión y sincronizar después.
- **SVN (centralizado):** Existe un único servidor central que contiene el repositorio. Los desarrolladores trabajan con copias locales (working copies) pero necesitan conexión al servidor para muchas operaciones.

### Operaciones y rendimiento

- **Git:** Las operaciones son locales y muy rápidas (commits, ramas, historial). Solo necesitas conexión para `push` y `pull`.
- **SVN:** Muchas operaciones requieren comunicación con el servidor, lo que puede ser más lento dependiendo de la red.

### Ramas y fusiones

- **Git:** Crear y cambiar de rama es instantáneo. Las fusiones son más simples y Git está diseñado para manejar múltiples ramas eficientemente.
- **SVN:** Las ramas se crean copiando directorios en el servidor. Las fusiones pueden ser más complejas y propensas a conflictos.

### Historial y versionado

- **Git:** Guarda snapshots completos del proyecto en cada commit. El historial es inmutable y se basa en hashes SHA-1.
- **SVN:** Guarda diferencias (deltas) entre versiones. El historial puede ser modificado con herramientas administrativas.

### Flujo de trabajo

- **Git:** Flujo típico: `add` → `commit` (local) → `push` (remoto). Los commits locales permiten revisar antes de compartir.
- **SVN:** Flujo típico: `add` → `commit` (directo al servidor). Los cambios se comparten inmediatamente.

### Ventajas de Git

- Trabajo offline completo
- Ramas y fusiones más eficientes
- Mejor para proyectos grandes y distribuidos
- Historial completo en cada copia local
- Mejor rendimiento en operaciones locales

### Ventajas de SVN

- Modelo más simple para equipos pequeños
- Control de acceso más directo (centralizado)
- Mejor integración con algunos sistemas legacy
- Curva de aprendizaje más suave para usuarios básicos

> **Nota:** Git se ha convertido en el estándar de la industria, especialmente en desarrollo de software moderno, mientras que SVN sigue siendo usado en algunos entornos corporativos tradicionales.

## ¿Qué es GitLab Self-Managed?

GitLab es una plataforma web de gestión de repositorios Git que incluye control de versiones, CI/CD y gestión de proyectos. En la modalidad Self-Managed, GitLab se instala en servidores propios (on-premise), lo que da control total al equipo sobre la instancia. Es análogo a GitHub o Bitbucket pero bajo tu propia administración.

> **Regla de oro:** Git es la herramienta de control de versiones, y plataformas como GitLab son servicios donde alojamos los repositorios.

## Instalación y configuración inicial

Después de instalar Git, configura tu identidad global para que Git firme los commits con tu nombre y email. Por ejemplo:

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu-email@ejemplo.com"
```

Esto crea un archivo `~/.gitconfig`. También puedes establecer tu editor por defecto:

```bash
git config --global core.editor vim
```

Estos son pasos típicos iniciales.

## Primer repositorio local

Crea un directorio para tu proyecto y conviértelo en repositorio Git con `git init`:

```bash
mkdir mi-proyecto
cd mi-proyecto
git init
```

Salida esperada:

```
Initialized empty Git repository in /ruta/a/mi-proyecto/.git/
```

El comando `git init` crea el subdirectorio oculto `.git` donde Git guarda todo el historial. Ahora tu código será rastreado. Puedes verificar con `git status`:

```bash
git status
```

Salida esperada:

```
On branch main
No commits yet
nothing to commit (create/copy files and use "git add" to track)
```

Git indica el estado actual: muestra la rama activa (ej. `main`) y si hay archivos preparados para confirmar.

## Añadir archivos y confirmar (commit)

Crea un archivo y añádelo al staging area:

```bash
echo "# Proyecto Demo" > README.md
git add README.md
git status
```

Salida esperada:

```
On branch main
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
  new file:   README.md
```

El comando `git add <archivo>` prepara cambios nuevos/modificados para confirmar. Luego realiza la confirmación con un mensaje:

```bash
git commit -m "Primera versión de README"
```

Salida esperada:

```
[main (root-commit) abc1234] Primera versión de README
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

`git commit -m "mensaje"` crea un nuevo commit en la rama actual con los cambios preparados. El mensaje explica el propósito de la versión. Al confirmar, Git genera un ID único (SHA) para el commit.

## Explorando el historial

Usa `git log` para ver el historial de commits en la rama actual:

```bash
git log --oneline
```

Salida esperada:

```
abc1234 (HEAD -> main) Primera versión de README
```

`git log` lista las confirmaciones (cada una con ID y mensaje). Así puedes navegar la historia del proyecto.

## Trabajando con ramas

Las ramas permiten desarrollar características aisladas. Para crear una rama nueva:

```bash
git branch desarrollo
```

Esto crea una rama llamada `desarrollo`. Para cambiarte a ella:

```bash
git checkout desarrollo
```

Salida esperada:

```
Switched to branch 'desarrollo'
```

O crea y cambia en un paso:

```bash
git checkout -b desarrollo
# o bien
git switch -c desarrollo
```

(`checkout -b` equivale a `git branch desarrollo && git checkout desarrollo`). Listar ramas te muestra en cuál estás (*):

```bash
git branch
```

Salida esperada:

```
  desarrollo
* main
```

### Ejemplo de flujo local

Imagina que agregas un archivo en la rama `desarrollo`:

```bash
touch feature.txt
git add feature.txt
git commit -m "Agregar nueva característica"
```

Luego cambia de vuelta a `main` y fusiona `desarrollo`:

```bash
git switch main
git merge desarrollo
```

Salida esperada:

```
Merge made by the 'recursive' strategy.
 feature.txt | 1 +
 1 file changed, 1 insertion(+)
```

Este merge crea un commit de fusión que une ambas historias (no creamos conflictos en este ejemplo).

## GitLab (repositorio remoto)

Para colaborar con GitLab, primero crea un proyecto vacío allí. Luego enlaza tu repo local con la URL remota, por ejemplo usando `origin`:

```bash
git remote add origin https://gitlab.miempresa.com/mi-usuario/mi-proyecto.git
```

Esto asigna la URL remota al nombre `origin`. Después, envía tus commits locales:

```bash
git push -u origin main
```

El comando `git push` transfiere los commits locales a la rama `main` en el servidor remoto. La opción `-u` establece la rama por defecto para futuros pushes/pulls. De ahora en adelante, basta con `git push` o `git pull` para sincronizar.

## Ejercicios prácticos (Sesión 1)

- Crea un directorio nuevo, inicialízalo como repositorio Git, añade un par de archivos, haz varios commits con mensajes descriptivos.
- Crea un repositorio vacío en GitLab Self-Managed, configúralo como remoto (ej. `origin`) de tu repo local, y haz `git push` para subir el historial.
- Comprueba el historial con `git log` y revisa el proyecto en la interfaz web de GitLab.
- Prueba crear y cambiar de ramas (`git branch`, `git checkout -b`), realizando commits en cada rama.

## Consejo clave

Sigue siempre las buenas prácticas:

- **Regla 1:** un repositorio Git por proyecto
- **Regla 2:** una rama nueva por cada característica
Así mantienes el desarrollo organizado.
