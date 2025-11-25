# Sesión 6: Git avanzado, CI/CD con GitLab CI y prácticas profesionales

## Índice de la sesión

1. [Git bisect: búsqueda binaria de bugs](#1-git-bisect-búsqueda-binaria-de-bugs) (30 minutos)
2. [Git hooks: automatización local](#2-git-hooks-automatización-local) (40 minutos)
3. [Introducción a CI/CD](#3-introducción-a-cicd) (15 minutos)
4. [GitLab CI/CD: fundamentos](#4-gitlab-cicd-fundamentos) (20 minutos)
5. [GitLab CI/CD: conceptos avanzados](#5-gitlab-cicd-conceptos-avanzados) (10 minutos)
6. [GitLab CI/CD: prácticas y patrones](#6-gitlab-cicd-prácticas-y-patrones) (10 minutos)
7. [Integración de Git con CI/CD](#7-integración-de-git-con-cicd) (10 minutos)
8. [Observación y comprensión de CI/CD en GitLab](#8-observación-y-comprensión-de-cicd-en-gitlab) (30 minutos)
9. [Ejercicios divertidos de Git (nivel junior)](#9-ejercicios-divertidos-de-git-nivel-junior) (105 minutos)
10. [Ejercicios finales de repaso](#10-ejercicios-finales-de-repaso) (75 minutos)

**Duración total estimada: 5 horas**

---

## 1. Git bisect: búsqueda binaria de bugs

`git bisect` es una herramienta muy útil para encontrar el commit exacto que introdujo un bug o un problema en tu código. Utiliza un algoritmo de búsqueda binaria para hacerlo de forma eficiente, reduciendo significativamente el número de commits que necesitas revisar manualmente.

### 1.1. ¿Cómo funciona?

Git bisect funciona dividiendo el rango de commits a la mitad repetidamente. Tú le indicas:
- Un commit "malo" (donde el bug existe)
- Un commit "bueno" (donde el bug no existe)

Git te lleva a un commit intermedio y tú le indicas si ese commit es "bueno" o "malo". El proceso se repite hasta encontrar el commit exacto que introdujo el problema.

### 1.2. Uso básico

1. **Iniciar bisect:**
   ```bash
   git bisect start
   ```

2. **Marcar el commit actual como "malo" (donde está el bug):**
   ```bash
   git bisect bad
   ```
   O si quieres especificar un commit específico:
   ```bash
   git bisect bad <commit-hash>
   ```

3. **Marcar un commit anterior como "bueno" (donde no había bug):**
   ```bash
   git bisect good <commit-hash>
   ```
   Por ejemplo, si sabes que hace 20 commits todo funcionaba:
   ```bash
   git bisect good HEAD~20
   ```

4. **Probar el commit actual:**
   Git te llevará automáticamente a un commit intermedio. Prueba tu aplicación o ejecuta tus tests para ver si el bug existe en ese commit.

5. **Marcar como bueno o malo:**
   - Si el bug existe: `git bisect bad`
   - Si el bug no existe: `git bisect good`

6. **Repetir:** Git seguirá llevándote a commits intermedios hasta encontrar el commit exacto que introdujo el bug.

7. **Finalizar:**
   ```bash
   git bisect reset
   ```
   Esto te devuelve a la rama donde estabas antes de iniciar bisect.

### 1.3. Ejemplo práctico

```bash
# Iniciar bisect
git bisect start

# El commit actual tiene el bug
git bisect bad

# Hace 50 commits todo funcionaba bien
git bisect good HEAD~50

# Git te lleva a un commit intermedio (por ejemplo, HEAD~25)
# Ejecutas tus tests o pruebas manuales
./run-tests.sh

# Si los tests fallan (el bug existe):
git bisect bad

# Si los tests pasan (no hay bug):
git bisect good

# Git seguirá reduciendo el rango hasta encontrar el commit culpable
# Cuando lo encuentre, te mostrará algo como:
# "abc1234 is the first bad commit"

# Finalizar y volver a tu rama original
git bisect reset
```

### 1.4. Automatización

Puedes automatizar el proceso si tienes un script de tests:

```bash
git bisect start HEAD HEAD~50
git bisect run ./test-script.sh
```

Git ejecutará automáticamente el script en cada commit y marcará como "bad" si el script falla (código de salida != 0) o "good" si pasa.

### 1.5. Comandos adicionales

- **Ver el estado actual:**
  ```bash
  git bisect log
  ```

- **Visualizar el rango:**
  ```bash
  git bisect visualize
  ```

- **Saltar un commit (si no puedes probarlo):**
  ```bash
  git bisect skip
  ```

- **Reiniciar desde el log:**
  ```bash
  git bisect replay <log-file>
  ```

### 1.6. Ventajas

- **Eficiencia:** En lugar de revisar 100 commits uno por uno, bisect te permite encontrar el bug en aproximadamente log₂(100) ≈ 7 pasos.
- **Precisión:** Encuentra el commit exacto que introdujo el problema.
- **Seguro:** No modifica tu código, solo navega por el historial.

---

## 2. Git hooks: automatización local

Los Git hooks son scripts que se ejecutan automáticamente en puntos específicos del flujo de trabajo de Git. Te permiten automatizar tareas, validar código, ejecutar tests, y mucho más.

### 2.1. ¿Qué son los hooks?

Los hooks son scripts ejecutables que Git ejecuta automáticamente en ciertos eventos. Están ubicados en el directorio `.git/hooks/` de tu repositorio.

### 2.2. Tipos de hooks

#### Hooks del lado del cliente (local)

**Hooks de commit:**
- `pre-commit`: Se ejecuta antes de crear un commit. Puede abortar el commit si falla.
- `prepare-commit-msg`: Se ejecuta antes de abrir el editor de mensaje de commit.
- `commit-msg`: Se ejecuta después de escribir el mensaje de commit. Puede validar el formato.
- `post-commit`: Se ejecuta después de crear el commit. No puede abortar nada.

**Hooks de merge:**
- `pre-merge-commit`: Se ejecuta antes de crear un commit de merge.
- `post-merge`: Se ejecuta después de un merge exitoso.

**Hooks de rebase:**
- `pre-rebase`: Se ejecuta antes de iniciar un rebase.

**Hooks de push:**
- `pre-push`: Se ejecuta antes de hacer push. Puede abortar el push.

#### Hooks del lado del servidor (GitLab)

- `pre-receive`: Se ejecuta antes de aceptar cualquier push.
- `update`: Similar a pre-receive, pero se ejecuta por cada rama.
- `post-receive`: Se ejecuta después de aceptar un push.

### 2.3. Crear un hook

Los hooks son simplemente archivos ejecutables en `.git/hooks/`. Pueden estar escritos en cualquier lenguaje (bash, Python, Ruby, etc.).

**Ejemplo: pre-commit para validar mensajes de commit:**

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Obtener el mensaje de commit
commit_msg=$(cat "$1")

# Verificar que el mensaje no esté vacío
if [ -z "$commit_msg" ]; then
    echo "Error: El mensaje de commit no puede estar vacío"
    exit 1
fi

# Verificar formato (ejemplo: debe empezar con mayúscula)
if ! [[ "$commit_msg" =~ ^[A-Z] ]]; then
    echo "Error: El mensaje de commit debe empezar con mayúscula"
    exit 1
fi

exit 0
```

**Hacer el hook ejecutable:**
```bash
chmod +x .git/hooks/pre-commit
```

### 2.4. Ejemplo: pre-commit para ejecutar tests

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Ejecutando tests antes del commit..."

# Ejecutar tests
npm test

# Si los tests fallan, abortar el commit
if [ $? -ne 0 ]; then
    echo "Los tests fallaron. Commit abortado."
    exit 1
fi

echo "Tests pasados. Continuando con el commit..."
exit 0
```

### 2.5. Ejemplo: commit-msg para validar formato

```bash
#!/bin/bash
# .git/hooks/commit-msg

commit_msg_file=$1
commit_msg=$(cat "$commit_msg_file")

# Patrón: tipo(scope): descripción
# Ejemplo: feat(auth): añadir login
pattern="^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"

if ! echo "$commit_msg" | grep -qE "$pattern"; then
    echo "Error: El mensaje de commit no sigue el formato convencional"
    echo "Formato esperado: tipo(scope): descripción"
    echo "Tipos válidos: feat, fix, docs, style, refactor, test, chore"
    exit 1
fi

exit 0
```

### 2.6. Compartir hooks con el equipo

Los hooks en `.git/hooks/` no se versionan. Para compartirlos:

1. **Crear un directorio para hooks versionados:**
   ```bash
   mkdir -p .githooks
   # Copiar tus hooks aquí
   cp .git/hooks/pre-commit .githooks/pre-commit
   git add .githooks/pre-commit
   git commit -m "chore: añadir hooks compartidos"
   ```

2. **Configurar Git para usar ese directorio:**
   ```bash
   git config core.hooksPath .githooks
   ```

3. **O crear un script de instalación:**
   ```bash
   # install-hooks.sh
   cp .githooks/* .git/hooks/
   chmod +x .git/hooks/*
   ```

### 2.7. Herramientas para gestionar hooks

- **pre-commit (Python):** Framework para gestionar hooks de pre-commit
- **husky (Node.js):** Gestiona hooks de Git para proyectos Node.js
- **git-hooks (Bash):** Gestor simple de hooks

### 2.8. Mejores prácticas

- **Mantén los hooks rápidos:** No bloquees el flujo de trabajo con operaciones lentas.
- **Haz hooks opcionales:** Permite saltarse hooks con `--no-verify` cuando sea necesario.
- **Documenta tus hooks:** Explica qué hace cada hook y cómo configurarlo.
- **Versiona hooks compartidos:** Usa `.githooks` o herramientas como pre-commit.

---

## 3. Introducción a CI/CD

CI/CD (Continuous Integration / Continuous Deployment) automatiza la integración, pruebas y despliegue de código.

### 3.1. ¿Qué es CI/CD?

**Continuous Integration (CI):** Integración continua del código, ejecución automática de tests en cada cambio, detección temprana de problemas.

**Continuous Deployment/Delivery (CD):** El código está siempre listo para desplegarse (Delivery) o se despliega automáticamente (Deployment).

### 3.2. Pipeline de CI/CD

Un pipeline ejecuta pasos automatizados en secuencia: `Código → Build → Test → Deploy → Monitoreo`

**Etapas típicas:** Build (compilar), Test (tests unitarios/integración), Lint (calidad), Security scan, Deploy (dev/staging/prod).

### 3.3. GitLab CI/CD

GitLab incluye CI/CD integrado con:
- **GitLab CI:** Motor de CI/CD
- **GitLab Runner:** Ejecutor de jobs
- **Pipeline:** Conjunto de jobs que se ejecutan en un commit
- **Stage:** Fase del pipeline (build, test, deploy)
- **Job:** Tarea individual en un stage
- **Artifact:** Archivo generado por un job

---

## 4. GitLab CI/CD: fundamentos

### 4.1. Archivo `.gitlab-ci.yml`

El archivo `.gitlab-ci.yml` en la raíz del repositorio define el pipeline:

```yaml
stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/

test-job:
  stage: test
  script:
    - npm test
  dependencies:
    - build-job

deploy-job:
  stage: deploy
  script:
    - ./deploy.sh
  only:
    - main
```

**Conceptos clave:**
- **Stages:** Etapas que se ejecutan en orden (build, test, deploy)
- **Jobs:** Tareas en un stage (se ejecutan en paralelo dentro del mismo stage)
- **Script:** Comandos a ejecutar
- **Artifacts:** Archivos generados disponibles para otros jobs
- **Dependencies:** Jobs de los que depende
- **Only/except:** Controla cuándo se ejecuta (ramas, tags, MRs)
- **Variables:** `CI_COMMIT_SHA`, `CI_COMMIT_REF_NAME`, `CI_PROJECT_NAME`, etc.

---

## 5. GitLab CI/CD: conceptos avanzados

**Imágenes Docker:** Usa `image: node:18` para definir el entorno. Puedes usar `services:` para bases de datos, redis, etc.

**Jobs paralelos:** `parallel: 5` ejecuta 5 instancias del mismo job.

**Jobs manuales:** `when: manual` requiere intervención manual.

**Environments:** Gestiona ambientes (staging, production) con URLs y deployment tiers.

**Variables protegidas:** Configura secrets en GitLab UI como "protected" y "masked" para ocultarlos en logs.

---

## 6. GitLab CI/CD: prácticas y patrones

**Mejores prácticas:**
- Mantén pipelines rápidos (cache, jobs paralelos)
- Organiza stages lógicamente: validate → build → test → security → deploy
- Usa templates para reutilizar configuraciones
- No expongas secrets (usa variables protegidas y masked)
- Build una vez, despliega múltiples veces usando artifacts

---

## 7. Integración de Git con CI/CD

**Estrategias comunes:**
- **Tests en todas las ramas:** Ejecuta tests en feature branches, build en develop, deploy manual en main
- **Tags y releases:** Los tags disparan pipelines de release automáticamente
- **Merge Requests:** Pipelines se ejecutan en MRs para validar antes de merge
- **Hooks locales + CI/CD:** Hooks locales ejecutan tests rápidos, CI/CD ejecuta tests completos

---

## 8. Observación y comprensión de CI/CD en GitLab

### 8.1. Explorar pipelines existentes (15 minutos)

**Objetivo:** Entender cómo funcionan los pipelines de CI/CD observando ejemplos reales.

**Instrucciones:**

1. **Explorar un repositorio con CI/CD:**
   - Busca en GitLab un repositorio que tenga un archivo `.gitlab-ci.yml` (puedes usar un proyecto de ejemplo o uno proporcionado por el instructor).
   - Abre el archivo `.gitlab-ci.yml` y analiza su estructura.

2. **Observar la ejecución de un pipeline:**
   - Ve a CI/CD → Pipelines en GitLab.
   - Observa cómo se ejecutan los stages y jobs.
   - Revisa los logs de un job para entender qué comandos se ejecutan.

3. **Entender el flujo:**
   - Identifica los diferentes stages del pipeline.
   - Observa cómo los jobs se ejecutan en paralelo o secuencia.
   - Verifica cómo se comparten artifacts entre jobs.

**Preguntas para reflexionar:**
- ¿Qué stages tiene el pipeline?
- ¿Qué jobs se ejecutan en paralelo?
- ¿Cuándo se ejecuta el pipeline? (push, merge request, tag, etc.)
- ¿Qué artifacts se generan?

### 8.2. Relación entre Git y CI/CD (15 minutos)

**Objetivo:** Comprender cómo Git y CI/CD trabajan juntos.

**Instrucciones:**

1. **Observar triggers de pipeline:**
   - Haz un commit y push a una rama.
   - Observa cómo se dispara automáticamente un pipeline.
   - Verifica qué información de Git está disponible en el pipeline (commit SHA, rama, autor, etc.).

2. **Merge Requests y CI/CD:**
   - Crea un Merge Request.
   - Observa cómo el pipeline se ejecuta para el MR.
   - Verifica cómo el estado del pipeline afecta la capacidad de merge.

3. **Tags y releases:**
   - Si hay tags en el repositorio, observa cómo se comportan los pipelines.
   - Verifica si hay jobs específicos que se ejecutan solo con tags.

**Conceptos clave a entender:**
- Los pipelines se ejecutan automáticamente en cada push.
- Los MRs muestran el estado del pipeline antes de permitir el merge.
- Los tags pueden disparar pipelines de release.
- Las variables de CI/CD incluyen información del commit y rama.

---

## 9. Ejercicios divertidos de Git (nivel junior)

### 9.1. El commit del "oops" (10 minutos)

**Objetivo:** Aprender a corregir mensajes de commit y usar `git commit --amend`.

**Escenario:** Acabas de hacer un commit pero te diste cuenta de que el mensaje tiene un error tipográfico o quieres añadir algo.

**Instrucciones:**

1. Crea un repositorio y haz un commit con un mensaje mal escrito:
   ```bash
   mkdir ejercicio-oops
   cd ejercicio-oops
   git init
   echo "Hola mundo" > saludo.txt
   git add saludo.txt
   git commit -m "feat: añadir saludo (con typo)"
   ```

2. Corrige el mensaje del commit usando `git commit --amend`:
   ```bash
   git commit --amend -m "feat: añadir saludo"
   ```

3. Verifica que el mensaje cambió:
   ```bash
   git log --oneline
   ```

4. **Bonus:** Añade un archivo más al commit anterior:
   ```bash
   echo "Adiós mundo" > despedida.txt
   git add despedida.txt
   git commit --amend --no-edit
   ```

### 9.2. El detective de commits (15 minutos)

**Objetivo:** Usar `git blame` y `git log` para investigar el historial.

**Escenario:** Encontraste una línea de código extraña y quieres saber quién la escribió y cuándo.

**Instrucciones:**

1. Crea un archivo con varios commits de diferentes "autores":
   ```bash
   mkdir ejercicio-detective
   cd ejercicio-detective
   git init
   git config user.name "Alice"
   git config user.email "alice@example.com"
   
   echo "Línea 1" > archivo.txt
   git add archivo.txt
   git commit -m "feat: añadir línea 1"
   
   echo "Línea 2" >> archivo.txt
   git add archivo.txt
   git commit -m "feat: añadir línea 2"
   
   git config user.name "Bob"
   git config user.email "bob@example.com"
   
   echo "Línea 3 (sospechosa)" >> archivo.txt
   git add archivo.txt
   git commit -m "feat: añadir línea 3"
   
   echo "Línea 4" >> archivo.txt
   git add archivo.txt
   git commit -m "feat: añadir línea 4"
   ```

2. Usa `git blame` para ver quién escribió cada línea:
   ```bash
   git blame archivo.txt
   ```

3. Usa `git log` para ver el historial completo:
   ```bash
   git log --oneline archivo.txt
   ```

4. Investiga un commit específico:
   ```bash
   git show <commit-hash>
   ```

### 9.3. El juego de las ramas (15 minutos)

**Objetivo:** Practicar creación, navegación y eliminación de ramas.

**Instrucciones:**

1. Crea un repositorio con varias ramas:
   ```bash
   mkdir ejercicio-ramas
   cd ejercicio-ramas
   git init
   echo "main" > archivo.txt
   git add archivo.txt
   git commit -m "Init"
   
   # Crea 5 ramas diferentes
   for rama in feature/login feature/logout feature/profile bugfix/crash hotfix/security; do
     git checkout -b $rama
     echo "$rama" >> archivo.txt
     git add archivo.txt
     git commit -m "feat: añadir $rama"
   done
   ```

2. Lista todas las ramas:
   ```bash
   git branch -a
   ```

3. Navega entre ramas y observa cómo cambia el archivo:
   ```bash
   git checkout feature/login
   cat archivo.txt
   git checkout bugfix/crash
   cat archivo.txt
   ```

4. Elimina una rama que ya no necesitas:
   ```bash
   git checkout main
   git branch -d feature/logout
   ```

5. **Bonus:** Intenta eliminar una rama sin hacer merge primero. ¿Qué pasa?

### 9.4. El stash mágico (10 minutos)

**Objetivo:** Aprender a guardar cambios temporalmente con `git stash`.

**Escenario:** Estás trabajando en algo, pero necesitas cambiar de rama urgentemente sin perder tus cambios.

**Instrucciones:**

1. Crea un repositorio y haz algunos cambios:
   ```bash
   mkdir ejercicio-stash
   cd ejercicio-stash
   git init
   echo "Línea 1" > archivo.txt
   git add archivo.txt
   git commit -m "Init"
   
   # Haz cambios sin commitear
   echo "Línea 2 (sin commit)" >> archivo.txt
   echo "Archivo nuevo" > nuevo.txt
   ```

2. Guarda los cambios en el stash:
   ```bash
   git stash push -m "Cambios temporales"
   ```

3. Verifica que el working directory está limpio:
   ```bash
   git status
   ```

4. Crea una rama urgente y haz un cambio:
   ```bash
   git checkout -b hotfix/urgente
   echo "Fix urgente" > fix.txt
   git add fix.txt
   git commit -m "fix: corrección urgente"
   git checkout main
   git merge hotfix/urgente
   ```

5. Recupera tus cambios del stash:
   ```bash
   git stash list
   git stash pop
   ```

6. Verifica que tus cambios volvieron:
   ```bash
   git status
   cat archivo.txt
   ```

### 9.5. El historial desordenado (20 minutos)

**Objetivo:** Limpiar un historial desordenado usando `git rebase -i`.

**Escenario:** Tienes un historial con commits como "wip", "fix", "a", "otro fix", etc. y quieres limpiarlo.

**Instrucciones:**

1. Crea un historial desordenado:
   ```bash
   mkdir ejercicio-limpieza
   cd ejercicio-limpieza
   git init
   
   echo "feature 1" > feature1.txt
   git add feature1.txt
   git commit -m "wip"
   
   echo "feature 2" > feature2.txt
   git add feature2.txt
   git commit -m "a"
   
   echo "fix" > fix.txt
   git add fix.txt
   git commit -m "fix"
   
   echo "otro fix" > fix2.txt
   git add fix2.txt
   git commit -m "otro fix"
   
   echo "wip" > wip.txt
   git add wip.txt
   git commit -m "wip"
   ```

2. Usa `git rebase -i` para limpiar los últimos 5 commits:
   ```bash
   git rebase -i HEAD~5
   ```

3. En el editor, cambia los commits:
   - Cambia "pick" por "reword" para renombrar mensajes
   - Cambia "pick" por "squash" o "fixup" para fusionar commits
   - Reordena commits si es necesario

4. Guarda y cierra. Git te pedirá que reescribas los mensajes.

5. Verifica el historial limpio:
   ```bash
   git log --oneline
   ```

### 9.6. El juego del tag (10 minutos)

**Objetivo:** Aprender a crear, listar y eliminar tags.

**Instrucciones:**

1. Crea un repositorio con algunos commits:
   ```bash
   mkdir ejercicio-tags
   cd ejercicio-tags
   git init
   
   for i in {1..5}; do
     echo "Versión $i" > version.txt
     git add version.txt
     git commit -m "feat: versión $i"
   done
   ```

2. Crea tags para diferentes versiones:
   ```bash
   git tag v1.0.0 HEAD~4
   git tag v1.1.0 HEAD~3
   git tag v1.2.0 HEAD~2
   git tag v2.0.0 HEAD~1
   git tag v2.1.0 HEAD
   ```

3. Lista todos los tags:
   ```bash
   git tag
   git tag -l "v1.*"
   ```

4. Ve a un tag específico:
   ```bash
   git checkout v1.0.0
   cat version.txt
   git checkout main
   ```

5. Crea un tag anotado con mensaje:
   ```bash
   git tag -a v3.0.0 -m "Release mayor: versión 3.0.0"
   ```

6. Ver información de un tag:
   ```bash
   git show v3.0.0
   ```

7. Elimina un tag:
   ```bash
   git tag -d v1.0.0
   ```

### 9.7. El desafío del reflog (15 minutos)

**Objetivo:** Recuperar commits "perdidos" usando `git reflog`.

**Escenario:** Hiciste un `git reset --hard` por error y perdiste commits importantes. ¡No te preocupes, Git los guarda!

**Instrucciones:**

1. Crea un repositorio con varios commits:
   ```bash
   mkdir ejercicio-reflog
   cd ejercicio-reflog
   git init
   
   for i in {1..5}; do
     echo "Commit importante $i" > commit$i.txt
     git add commit$i.txt
     git commit -m "feat: commit importante $i"
   done
   ```

2. Verifica el historial:
   ```bash
   git log --oneline
   ```

3. **¡Oh no!** Hiciste un reset por error:
   ```bash
   git reset --hard HEAD~3
   ```

4. Verifica que los commits desaparecieron:
   ```bash
   git log --oneline
   ```

5. Usa `git reflog` para encontrar los commits perdidos:
   ```bash
   git reflog
   ```

6. Recupera los commits usando el hash del reflog:
   ```bash
   # Encuentra el hash del commit que quieres recuperar en el reflog
   git reset --hard <hash-del-reflog>
   ```

7. Verifica que los commits volvieron:
   ```bash
   git log --oneline
   ```

### 9.8. El juego de los aliases (10 minutos)

**Objetivo:** Crear aliases personalizados para comandos de Git que usas frecuentemente.

**Instrucciones:**

1. Crea algunos aliases útiles:
   ```bash
   git config --global alias.st status
   git config --global alias.co checkout
   git config --global alias.br branch
   git config --global alias.ci commit
   git config --global alias.unstage 'reset HEAD --'
   git config --global alias.last 'log -1 HEAD'
   git config --global alias.visual '!gitk'
   git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
   ```

2. Prueba tus nuevos aliases:
   ```bash
   git st
   git last
   git lg
   ```

3. Lista todos tus aliases:
   ```bash
   git config --global --get-regexp alias
   ```

4. **Bonus:** Crea un alias divertido:
   ```bash
   git config --global alias.whoops 'reset --hard HEAD~1'
   git config --global alias.yolo 'push --force'
   ```

---

## 10. Ejercicios finales de repaso

Esta sección contiene ejercicios integradores para repasar todos los conceptos avanzados de Git vistos durante el curso.

### 10.1. Ejercicio A: El flujo completo (Individual) (30 minutos)

**Objetivo:** Simular el ciclo de vida completo de una funcionalidad, desde la creación hasta el despliegue, usando buenas prácticas de Git.

**Instrucciones:**

1. **Configuración:** Crea un nuevo repositorio en GitLab llamado `proyecto-final-tunombre`. Clónalo localmente.

2. **Estructura:** Crea una rama `develop` y protégela en GitLab (nadie puede hacer push directo).

3. **Feature:**
   - Crea una rama `feature/login` desde `develop`.
   - Implementa código simple (puede ser un archivo de texto o código mínimo).
   - Haz al menos 3 commits con mensajes descriptivos siguiendo conventional commits (feat, fix, docs, etc.).

4. **Revisión:**
   - Sube tu rama a GitLab.
   - Crea un Merge Request hacia `develop`.
   - En el MR, simula una revisión: añade un comentario en una línea de código.
   - Observa si hay un pipeline ejecutándose (si el repositorio tiene CI/CD configurado).

5. **Corrección:**
   - Haz un cambio en tu rama local para atender el comentario.
   - Usa `git commit --fixup` para el nuevo cambio.
   - Usa `git rebase -i --autosquash` para fusionarlo con el commit original.
   - Haz `git push --force-with-lease` para actualizar el MR.
   - Observa cómo el MR se actualiza y si el pipeline se vuelve a ejecutar.

6. **Merge:** Fusiona el MR en GitLab.

7. **Release:**
   - En local, actualiza `main` con los cambios de `develop` (simulando una release).
   - Crea un tag `v1.0.0` y súbelo.
   - Si hay CI/CD configurado, observa si se ejecuta algún pipeline relacionado con el tag.

**Puntos clave a verificar:**
- Historial limpio en `develop` (commits squashed o bien organizados).
- Uso correcto de ramas y protección.
- Manejo de rebase interactivo.
- Uso correcto de `--force-with-lease`.
- Tags creados y subidos correctamente.

### 10.2. Ejercicio B: Depuración y limpieza (Parejas o Individual) (25 minutos)

**Objetivo:** Encontrar un error introducido en el pasado y limpiar un historial desastroso usando git bisect y rebase interactivo.

**Escenario:** Tienes un repositorio con un historial de 20 commits. En algún punto, se introdujo un bug que hace que la aplicación falle, y el historial está lleno de mensajes como "wip", "fix", "a".

**Instrucciones:**

1. **Preparación:**
   - Ejecuta el siguiente script para generar el escenario:
     ```bash
     mkdir ejercicio-debug
     cd ejercicio-debug
     git init
     echo "print('Inicio')" > app.py
     git add app.py
     git commit -m "Init"
     
     for i in {1..20}; do
         echo "print('Paso $i')" >> app.py
         if [ $i -eq 12 ]; then
             echo "division = 1 / 0  # BUG" >> app.py
         fi
         git add app.py
         msg="Commit $i"
         if [ $((i % 3)) -eq 0 ]; then msg="wip"; fi
         git commit -m "$msg"
     done
     ```

2. **Búsqueda del bug:**
   - Usa `git bisect` para encontrar exactamente qué commit introdujo la división por cero.
   - Crea un script de test simple (`test.sh`) que puedas usar con `git bisect run`:
     ```bash
     #!/bin/bash
     # test.sh
     python3 app.py 2>&1 | grep -q "ZeroDivisionError" && exit 1 || exit 0
     ```
   - Ejecuta: `chmod +x test.sh`
   - Usa: `git bisect start HEAD HEAD~20`
   - Marca el commit actual como bad: `git bisect bad`
   - Marca el commit inicial como good: `git bisect good HEAD~20`
   - Ejecuta: `git bisect run ./test.sh`
   - Anota el commit que introdujo el bug.

3. **Limpieza:**
   - Una vez encontrado el bug, crea una rama `fix/bug-div-zero` desde el commit donde está el bug.
   - Elimina la línea del bug: `sed -i '/division = 1 \/ 0/d' app.py`
   - Haz commit del fix: `git commit -am "fix: eliminar división por cero"`
   - Vuelve a la rama principal y usa `git rebase -i HEAD~10` para limpiar los últimos 10 commits:
     - Fusiona los commits con mensaje "wip"
     - Renombra mensajes poco descriptivos
     - Organiza el historial de forma lógica

4. **Verificación:**
   - El historial final debe ser legible y sin el bug.
   - Usa `git log --oneline` para verificar que el historial está limpio.
   - Verifica que el bug esté corregido ejecutando `python3 app.py`.

### 10.3. Ejercicio C: Colaboración y conflictos (En clase / Parejas) (20 minutos)

**Objetivo:** Resolver un "infierno de dependencias" y conflictos cruzados usando técnicas avanzadas de Git.

**Instrucciones:**

1. **Setup:** Crea un repo compartido con un archivo `config.json` de 50 líneas. Puedes usar este script para generarlo:
   ```bash
   mkdir ejercicio-conflictos
   cd ejercicio-conflictos
   git init
   
   # Crear config.json con 50 líneas
   echo "{" > config.json
   for i in {1..50}; do
     echo "  \"key$i\": \"value$i\"," >> config.json
   done
   echo "  \"key51\": \"value51\"" >> config.json
   echo "}" >> config.json
   
   git add config.json
   git commit -m "feat: añadir config.json inicial"
   git remote add origin <url-del-repo>
   git push -u origin main
   ```

2. **Desarrollo paralelo:**
   - **Alumno A:** 
     - Crea rama `feature/database` desde `main`.
     - Modifica las líneas 10-20 de `config.json` (cambia valores).
     - Cambia el formato de indentación de TODO el archivo (de 2 espacios a 4 espacios, o viceversa).
     - Haz commit: `git commit -am "feat(database): actualizar configuración de BD"`
     - Haz push: `git push origin feature/database`
   
   - **Alumno B:**
     - Crea rama `feature/ui` desde `main`.
     - Modifica las líneas 15-25 de `config.json` (solapándose con A).
     - Renombra una clave que A también modificó (por ejemplo, `key15` → `ui_key15`).
     - Haz commit: `git commit -am "feat(ui): actualizar configuración de UI"`
     - Haz push: `git push origin feature/ui`

3. **El conflicto:**
   - Alumno A hace merge de su rama a `main` y push.
   - Alumno B intenta hacer rebase de su rama sobre `main`:
     ```bash
     git fetch origin
     git rebase origin/main
     ```

4. **Resolución avanzada:**
   - Alumno B debe resolver los conflictos usando técnicas avanzadas:
     - Usa `git status` para ver qué archivos tienen conflictos.
     - Abre `config.json` y resuelve los conflictos manualmente.
     - Para partes específicas, puedes usar:
       - `git checkout --ours <archivo>` para tomar tu versión completa
       - `git checkout --theirs <archivo>` para tomar la versión de main
       - O resolver manualmente línea por línea
     - Debe decidir qué cambios conservar:
       - Lógica de ambos (valores de ambos)
       - Indentación de A (formato consistente)
     - Usa una herramienta de diff visual (VSCode) para facilitar la resolución.

5. **Finalización:**
   - Después de resolver todos los conflictos:
     ```bash
     git add config.json
     git rebase --continue
     ```
   - Si hay más conflictos, repite el proceso.
   - Una vez completado el rebase, valida que `config.json` sea JSON válido:
     ```bash
     python3 -m json.tool config.json > /dev/null && echo "JSON válido" || echo "JSON inválido"
     ```
   - Haz push con force-with-lease: `git push --force-with-lease origin feature/ui`
   - Crea un Merge Request hacia `main`.

**Puntos clave a verificar:**
- Resolución correcta de conflictos complejos.
- Uso de `--ours` y `--theirs` cuando sea apropiado.
- Archivo final válido y funcional.
- Rebase exitoso sin perder cambios importantes.

---

## Recursos finales

¡Felicidades por llegar al final del curso! Aquí tienes una lista de recursos para seguir aprendiendo:

### Git

- **Documentación oficial:** [git-scm.com/doc](https://git-scm.com/doc)
- **Libro Pro Git:** [git-scm.com/book](https://git-scm.com/book) (gratuito y excelente)
- **Juego interactivo:** [Learn Git Branching](https://learngitbranching.js.org/)
- **GitHub Learning Lab:** [lab.github.com](https://lab.github.com/)

### GitLab

- **GitLab Docs:** [docs.gitlab.com](https://docs.gitlab.com)
- **GitLab CI/CD Docs:** [docs.gitlab.com/ee/ci/](https://docs.gitlab.com/ee/ci/)
- **GitLab CI/CD Examples:** [docs.gitlab.com/ee/ci/examples/](https://docs.gitlab.com/ee/ci/examples/)

### CI/CD

- **CI/CD Best Practices:** [docs.gitlab.com/ee/ci/pipelines/pipeline_efficiency.html](https://docs.gitlab.com/ee/ci/pipelines/pipeline_efficiency.html)
- **GitLab CI/CD Variables:** [docs.gitlab.com/ee/ci/variables/](https://docs.gitlab.com/ee/ci/variables/)
- **GitLab Environments:** [docs.gitlab.com/ee/ci/environments/](https://docs.gitlab.com/ee/ci/environments/)

### Hooks y automatización

- **Git Hooks:** [git-scm.com/book/en/v2/Customizing-Git-Git-Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)
- **pre-commit framework:** [pre-commit.com](https://pre-commit.com/)
- **Husky:** [typicode.github.io/husky/](https://typicode.github.io/husky/)

### Prácticas y workflows

- **Git Flow:** [nvie.com/posts/a-successful-git-branching-model/](https://nvie.com/posts/a-successful-git-branching-model/)
- **Conventional Commits:** [conventionalcommits.org](https://www.conventionalcommits.org/)
- **Semantic Versioning:** [semver.org](https://semver.org/)

Recuerda: **Git y CI/CD son herramientas poderosas pero complejas. La práctica constante es la única forma de dominarlas.** ¡Mucha suerte!
