# Sesión 6: Git avanzado, CI/CD con GitLab CI y prácticas profesionales

## Bienvenida

¡Bienvenidos y bienvenidas a la sesión 6! Esta es la sesión final de nuestro curso de Git, donde consolidaremos todo lo aprendido y nos adentraremos en herramientas avanzadas que te convertirán en un desarrollador o desarrolladora más eficiente y profesional.

Hoy vamos a explorar:
- **Herramientas de depuración avanzadas** como `git bisect` para encontrar bugs de forma eficiente
- **Automatización local** con Git hooks para mejorar tu flujo de trabajo
- **CI/CD con GitLab** para entender cómo se integra Git con la integración y despliegue continuo
- **Ejercicios prácticos** que simulan situaciones reales del día a día

Esta sesión está diseñada para ser **100% práctica**. No solo aprenderás conceptos, sino que los aplicarás inmediatamente en ejercicios que reflejan problemas reales que encontrarás en tu carrera profesional.

**Objetivos de la sesión:**
- Dominar herramientas avanzadas de Git para depuración y mantenimiento
- Implementar automatizaciones locales con hooks
- Comprender cómo Git se integra con CI/CD
- Resolver problemas complejos de forma eficiente y profesional

¡Vamos a ello!

---

## Resumen de la sesión

Esta sesión de **6 horas** está estructurada en tres bloques principales:

### Bloque 1: Herramientas avanzadas de Git (1h 10min)
- **Git bisect (30 min):** Aprenderás a usar la búsqueda binaria para encontrar el commit exacto que introdujo un bug, reduciendo horas de búsqueda manual a minutos.
- **Git hooks (40 min):** Automatizarás tareas locales como validación de código, ejecución de tests y verificación de formato de commits antes de que lleguen al repositorio remoto.

### Bloque 2: CI/CD con GitLab (1h 25min)
- **Introducción a CI/CD (15 min):** Conceptos fundamentales de integración y despliegue continuo.
- **GitLab CI/CD fundamentos (20 min):** Configuración de pipelines con `.gitlab-ci.yml`, stages, jobs y artifacts.
- **Conceptos avanzados (10 min):** Docker, jobs paralelos, environments y variables protegidas.
- **Prácticas y patrones (10 min):** Mejores prácticas para pipelines eficientes y seguros.
- **Integración Git + CI/CD (10 min):** Cómo Git dispara pipelines y cómo trabajar con tags, branches y MRs.
- **Observación práctica (30 min):** Explorarás pipelines reales en GitLab para entender el flujo completo.

### Bloque 3: Ejercicios prácticos (5h 5min)
- **Ejercicios divertidos básicos (1h 45min):** 8 ejercicios progresivos que cubren desde corrección de commits hasta recuperación de código "perdido".
- **Ejercicios adicionales sencillos (1h 20min):** 8 ejercicios nuevos que cubren temas complementarios:
  - Comparar cambios con git diff (10 min)
  - Explorar historial con git log avanzado (10 min)
  - Limpieza de archivos (git rm, mv, clean) (10 min)
  - Restaurar archivos con git restore (10 min)
  - Gestión de remotos (10 min)
  - Resumen de contribuciones con shortlog (10 min)
  - Versiones con git describe (10 min)
  - Crear archivos comprimidos con git archive (10 min)
- **Ejercicios finales (1h 30min):** 4 ejercicios integradores que simulan escenarios reales:
  - Flujo completo de feature (30 min)
  - Depuración y limpieza de historial (25 min)
  - Resolución de conflictos complejos (20 min)
  - Migración compleja con cherry-pick y revert (45 min)
- **Ejercicios avanzados (30 min, opcional):** 5 desafíos adicionales para quienes quieran profundizar:
  - Git worktree para múltiples working directories (15 min)
  - Reescritura avanzada de historial (20 min)
  - Hooks avanzados con integración CI/CD (15 min)
  - Git submodules para gestión de dependencias (20 min)
  - Desafío del historial complejo (25 min)

**Material necesario:**
- Acceso a GitLab (cuenta gratuita es suficiente)
- Git instalado y configurado
- Editor de texto o IDE
- Terminal/consola

**Requisitos previos:**
- Conocimiento de comandos básicos de Git (commit, push, pull, branch, merge)
- Familiaridad con la terminal/consola
- Haber completado las sesiones anteriores del curso

---

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
   - 9.1-9.8: Ejercicios básicos (105 min)
   - 9.9-9.16: Ejercicios adicionales sencillos (80 minutos)
10. [Ejercicios finales de repaso](#10-ejercicios-finales-de-repaso) (90 minutos)
11. [Ejercicios avanzados (opcional)](#11-ejercicios-avanzados-opcional) (30 minutos)

**Duración total estimada: 6 horas** (7 horas con ejercicios adicionales y avanzados)

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

El archivo `.gitlab-ci.yml` en la raíz del repositorio define el pipeline. A continuación, un ejemplo moderno utilizando `rules` en lugar del obsoleto `only`:

```yaml
stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  image: node:18-alpine
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour

test-job:
  stage: test
  image: node:18-alpine
  script:
    - npm test
  needs: ["build-job"]

deploy-job:
  stage: deploy
  script:
    - ./deploy.sh
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

**Conceptos clave:**
- **Stages:** Etapas que se ejecutan en orden (build, test, deploy).
- **Jobs:** Tareas en un stage (se ejecutan en paralelo dentro del mismo stage).
- **Script:** Comandos a ejecutar.
- **Artifacts:** Archivos generados disponibles para otros jobs (ej: binarios compilados).
- **Needs/Dependencies:** Optimiza la ejecución indicando qué jobs previos son necesarios.
- **Rules:** Reemplaza a `only/except`. Controla lógicamente cuándo se añade el job al pipeline.
- **Variables:** `CI_COMMIT_SHA`, `CI_COMMIT_REF_NAME`, `CI_PROJECT_NAME`, etc.

---

## 5. GitLab CI/CD: conceptos avanzados

**Imágenes Docker:** Usa `image: node:18` para definir el entorno. Puedes usar `services:` para levantar bases de datos temporales (ej: Postgres, Redis) para los tests.

**Jobs paralelos:** `parallel: matrix` permite ejecutar el mismo job con diferentes variables (ej: testear en Node 16, 18 y 20).

**Jobs manuales:** `when: manual` requiere que un humano pulse "play" en la interfaz de GitLab. Útil para despliegues a producción.

**Environments:** Gestiona ambientes (staging, production) permitiendo ver el historial de despliegues y hacer rollbacks desde la UI.

**Variables protegidas:** Configura secrets (API Keys, contraseñas) en GitLab UI (Settings -> CI/CD -> Variables). Márcalas como "Masked" para que no salgan en los logs.

---

## 6. GitLab CI/CD: prácticas y patrones

**Mejores prácticas:**
- **Fail fast:** Ejecuta lo más rápido primero (linting, tests unitarios).
- **Cache:** Usa `cache` para dependencias (node_modules) y acelerar builds.
- **Docker-in-Docker (dind):** Necesario si tu pipeline construye imágenes Docker.
- **Templates:** Usa `include:` para reutilizar configuraciones de otros archivos o repositorios.
- **Idempotencia:** Asegúrate de que los scripts de deploy se puedan ejecutar múltiples veces sin romper nada.

---

## 7. Integración de Git con CI/CD

**Estrategias comunes:**
- **Tests en Feature Branches:** El pipeline debe pasar (verde) antes de permitir el Merge.
- **Pipelines de Merge Request:** Jobs específicos que solo corren cuando existe un MR abierto (ej: DangerJS para revisar estilo).
- **Git Tags como disparadores:** Crear un tag `v1.0.0` dispara automáticamente el pipeline de despliegue a producción.
- **Skipping CI:** Si un commit es solo documentación, puedes añadir `[skip ci]` en el mensaje del commit para ahorrar recursos.

---

## 8. Observación y comprensión de CI/CD en GitLab

### 8.1. Explorar pipelines existentes (15 minutos)

**Objetivo:** Entender cómo funcionan los pipelines de CI/CD observando ejemplos reales.

**Instrucciones:**

1. **Explorar un repositorio con CI/CD:**
   - Busca en GitLab un repositorio que tenga un archivo `.gitlab-ci.yml`.
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

### 9.9. El arte de comparar cambios (10 minutos)

**Objetivo:** Dominar `git diff` para comparar cambios entre commits, ramas y el working directory.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-diff
   cd ejercicio-diff
   git init
   
   echo "Línea 1" > archivo.txt
   git add archivo.txt
   git commit -m "feat: añadir línea 1"
   
   echo "Línea 2" >> archivo.txt
   git add archivo.txt
   git commit -m "feat: añadir línea 2"
   
   echo "Línea 3" >> archivo.txt
   # No hagas commit todavía
   ```

2. **Comparar working directory vs staging:**
   ```bash
   git diff
   # Muestra cambios no staged
   ```

3. **Comparar staging vs último commit:**
   ```bash
   git add archivo.txt
   git diff --staged
   # O también: git diff --cached
   ```

4. **Comparar dos commits:**
   ```bash
   git commit -m "feat: añadir línea 3"
   git diff HEAD~2 HEAD
   # Compara el commit de hace 2 posiciones con el actual
   ```

5. **Comparar ramas:**
   ```bash
   git checkout -b feature/nueva
   echo "Línea 4 (nueva rama)" >> archivo.txt
   git add archivo.txt
   git commit -m "feat: añadir línea 4"
   
   git diff main feature/nueva
   # Compara main con la nueva rama
   ```

6. **Ver estadísticas de cambios:**
   ```bash
   git diff --stat HEAD~2 HEAD
   # Muestra resumen de archivos modificados
   ```

### 9.10. El explorador de historial (10 minutos)

**Objetivo:** Usar `git log` con opciones avanzadas para visualizar y buscar en el historial.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-log
   cd ejercicio-log
   git init
   
   for i in {1..10}; do
     echo "Feature $i" > feature$i.txt
     git add feature$i.txt
     if [ $((i % 2)) -eq 0 ]; then
       git commit -m "feat: añadir feature $i"
     else
       git commit -m "fix: corregir feature $i"
     fi
   done
   ```

2. **Visualizar historial con gráfico:**
   ```bash
   git log --oneline --graph --all --decorate
   # Muestra un gráfico visual del historial
   ```

3. **Buscar commits por mensaje:**
   ```bash
   git log --grep="feat"
   # Busca commits que contengan "feat" en el mensaje
   ```

4. **Buscar cambios de contenido:**
   ```bash
   git log -S "Feature 5"
   # Busca commits que añadieron o eliminaron la cadena "Feature 5"
   ```

5. **Filtrar por autor:**
   ```bash
   git log --author="tu-nombre"
   # Muestra solo tus commits
   ```

6. **Ver commits en un rango de fechas:**
   ```bash
   git log --since="2 days ago"
   git log --until="1 week ago"
   ```

7. **Formato personalizado:**
   ```bash
   git log --pretty=format:"%h - %an, %ar : %s"
   # Formato: hash - autor, tiempo relativo : mensaje
   ```

### 9.11. Limpieza de archivos (10 minutos)

**Objetivo:** Aprender a eliminar y limpiar archivos con Git usando `git rm`, `git mv` y `git clean`.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-limpieza-archivos
   cd ejercicio-limpieza-archivos
   git init
   
   echo "Archivo 1" > archivo1.txt
   echo "Archivo 2" > archivo2.txt
   echo "Archivo 3" > archivo3.txt
   git add .
   git commit -m "feat: añadir archivos iniciales"
   ```

2. **Eliminar archivo del repositorio:**
   ```bash
   git rm archivo1.txt
   git status
   # El archivo está staged para eliminación
   git commit -m "chore: eliminar archivo1"
   ```

3. **Eliminar archivo pero mantenerlo localmente:**
   ```bash
   git rm --cached archivo2.txt
   git status
   # El archivo se elimina del tracking pero sigue existiendo
   # Útil para añadir archivos a .gitignore después de commitearlos
   ```

4. **Renombrar/mover archivos:**
   ```bash
   git mv archivo3.txt archivo-renombrado.txt
   git status
   # Git detecta el movimiento automáticamente
   git commit -m "refactor: renombrar archivo3"
   ```

5. **Limpiar archivos no rastreados:**
   ```bash
   echo "Archivo temporal" > temporal.txt
   echo "Otro temporal" > otro-temp.txt
   mkdir temp-dir
   echo "test" > temp-dir/test.txt
   
   # Ver qué se eliminaría (dry-run)
   git clean -n
   
   # Eliminar archivos no rastreados
   git clean -f
   
   # Eliminar también directorios
   git clean -fd
   ```

6. **Limpiar de forma interactiva:**
   ```bash
   echo "Archivo a eliminar?" > eliminar.txt
   git clean -i
   # Te pregunta qué eliminar
   ```

### 9.12. Restaurar archivos (10 minutos)

**Objetivo:** Usar `git restore` (comando moderno) para restaurar archivos a estados anteriores.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-restore
   cd ejercicio-restore
   git init
   
   echo "Versión 1" > archivo.txt
   git add archivo.txt
   git commit -m "feat: versión 1"
   
   echo "Versión 2" >> archivo.txt
   git add archivo.txt
   git commit -m "feat: versión 2"
   
   echo "Cambio sin commitear" >> archivo.txt
   ```

2. **Descartar cambios en working directory:**
   ```bash
   git restore archivo.txt
   # Restaura el archivo al estado del último commit
   cat archivo.txt
   # Debería mostrar solo "Versión 1" y "Versión 2"
   ```

3. **Descartar cambios staged:**
   ```bash
   echo "Cambio staged" >> archivo.txt
   git add archivo.txt
   git restore --staged archivo.txt
   git status
   # El archivo vuelve a estar unstaged
   ```

4. **Restaurar desde un commit específico:**
   ```bash
   git restore --source=HEAD~1 archivo.txt
   # Restaura el archivo al estado del commit anterior
   cat archivo.txt
   # Debería mostrar solo "Versión 1"
   ```

5. **Restaurar múltiples archivos:**
   ```bash
   echo "Archivo 2" > archivo2.txt
   git add archivo2.txt
   git commit -m "feat: añadir archivo2"
   
   echo "Cambio 1" >> archivo.txt
   echo "Cambio 2" >> archivo2.txt
   
   git restore archivo.txt archivo2.txt
   # Restaura ambos archivos
   ```

### 9.13. Gestión de remotos (10 minutos)

**Objetivo:** Aprender a gestionar repositorios remotos con `git remote`, `git fetch` y entender la diferencia con `git pull`.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-remotos
   cd ejercicio-remotos
   git init
   echo "Inicio" > archivo.txt
   git add archivo.txt
   git commit -m "init"
   
   # Si tienes un repositorio remoto, úsalo. Si no, simula uno:
   # git remote add origin <url-de-tu-repo>
   ```

2. **Ver remotos configurados:**
   ```bash
   git remote
   # Lista nombres de remotos
   git remote -v
   # Lista nombres y URLs
   ```

3. **Añadir un remoto:**
   ```bash
   # Ejemplo (ajusta la URL):
   # git remote add origin https://gitlab.com/usuario/repo.git
   # git remote add upstream https://gitlab.com/original/repo.git
   ```

4. **Ver información de un remoto:**
   ```bash
   git remote show origin
   # Muestra información detallada del remoto
   ```

5. **Renombrar remoto:**
   ```bash
   # git remote rename origin mi-origen
   ```

6. **Eliminar remoto:**
   ```bash
   # git remote remove mi-origen
   ```

7. **Fetch vs Pull:**
   ```bash
   # Fetch: descarga cambios pero NO los integra
   # git fetch origin
   # git log origin/main  # Ver cambios del remoto
   
   # Pull: fetch + merge automático
   # git pull origin main
   # Equivale a: git fetch origin && git merge origin/main
   ```

8. **Actualizar URLs de remotos:**
   ```bash
   # git remote set-url origin nueva-url
   ```

### 9.14. El resumen de contribuciones (10 minutos)

**Objetivo:** Usar `git shortlog` para ver un resumen de contribuciones por autor.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-shortlog
   cd ejercicio-shortlog
   git init
   
   git config user.name "Alice"
   git config user.email "alice@example.com"
   
   for i in {1..5}; do
     echo "Feature $i" > feature$i.txt
     git add feature$i.txt
     git commit -m "feat: feature $i"
   done
   
   git config user.name "Bob"
   git config user.email "bob@example.com"
   
   for i in {6..8}; do
     echo "Fix $i" > fix$i.txt
     git add fix$i.txt
     git commit -m "fix: corrección $i"
   done
   ```

2. **Ver resumen por autor:**
   ```bash
   git shortlog
   # Muestra número de commits y lista por autor
   ```

3. **Ver solo números:**
   ```bash
   git shortlog -sn
   # Muestra solo número de commits por autor
   ```

4. **Filtrar por rango:**
   ```bash
   git shortlog HEAD~5..HEAD
   # Solo últimos 5 commits
   ```

5. **Ver por email:**
   ```bash
   git shortlog -se
   # Muestra también emails
   ```

### 9.15. Versiones con describe (10 minutos)

**Objetivo:** Usar `git describe` para obtener versiones basadas en tags.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-describe
   cd ejercicio-describe
   git init
   
   for i in {1..10}; do
     echo "Commit $i" > commit$i.txt
     git add commit$i.txt
     git commit -m "feat: commit $i"
     
     if [ $i -eq 3 ]; then
       git tag v1.0.0
     elif [ $i -eq 7 ]; then
       git tag v2.0.0
     fi
   done
   ```

2. **Ver versión actual:**
   ```bash
   git describe
   # Muestra: v2.0.0-3-g<hash>
   # Significa: tag v2.0.0, 3 commits después, hash abreviado
   ```

3. **Ver versión de un commit específico:**
   ```bash
   git checkout HEAD~5
   git describe
   # Muestra la versión de ese commit
   git checkout main
   ```

4. **Solo tags exactos:**
   ```bash
   git checkout v1.0.0
   git describe --exact-match
   # Muestra solo si estás exactamente en un tag
   ```

5. **Con más contexto:**
   ```bash
   git describe --always --dirty
   # Siempre muestra algo, incluso si no hay tags
   # --dirty añade "-dirty" si hay cambios sin commitear
   ```

### 9.16. Crear archivos comprimidos (10 minutos)

**Objetivo:** Usar `git archive` para crear archivos comprimidos de versiones específicas.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-archive
   cd ejercicio-archive
   git init
   
   echo "Código fuente" > src/main.js
   echo "README" > README.md
   mkdir docs
   echo "Documentación" > docs/guide.txt
   
   git add .
   git commit -m "feat: proyecto inicial"
   
   git tag v1.0.0
   
   echo "Nueva feature" >> src/main.js
   git add src/main.js
   git commit -m "feat: nueva feature"
   ```

2. **Crear archivo tar del último commit:**
   ```bash
   git archive -o release.tar HEAD
   # Crea un archivo tar
   ```

3. **Crear archivo zip de un tag:**
   ```bash
   git archive -o v1.0.0.zip v1.0.0
   # Crea un zip de la versión 1.0.0
   ```

4. **Crear tar.gz comprimido:**
   ```bash
   git archive --format=tar.gz -o release.tar.gz HEAD
   # O simplemente:
   git archive -o release.tar.gz HEAD
   ```

5. **Incluir solo ciertos archivos:**
   ```bash
   git archive -o src-only.tar HEAD src/
   # Solo archivos en el directorio src/
   ```

6. **Verificar el contenido:**
   ```bash
   tar -tzf release.tar.gz
   # Lista contenido del archivo
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
   # NOTA: Si lo haces solo, simula dos remotos o carpetas, 
   # o usa git remote add origin <url-del-repo> si tienes compañero
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

### 10.4. Ejercicio D: La migración compleja (cherry-pick y revert) (45 minutos)

**Objetivo:** Dominar `cherry-pick` para mover commits entre ramas divergentes y `revert` para deshacer cambios de forma segura en ramas públicas.

**Escenario:**
Trabajas en un proyecto que tiene una versión "Legacy" (mantenimiento) y una versión "Next" (desarrollo activo).
1. Un desarrollador hizo commit de una feature nueva en la rama `legacy` por error.
2. Hay un hotfix crítico en `legacy` que también se necesita en `next`.
3. Se hizo un merge en `main` que rompió producción y hay que deshacerlo sin borrar el historial.

**Instrucciones:**

1. **Preparación del entorno:**
   Ejecuta este script para crear el escenario:
   ```bash
   mkdir ejercicio-migracion
   cd ejercicio-migracion
   git init
   
   # Crear base
   echo "Core v1" > core.txt
   git add core.txt
   git commit -m "init: core v1"
   
   # Crear rama legacy y avanzar
   git checkout -b legacy
   echo "Bug critico" > bug.txt
   git add bug.txt
   git commit -m "fix: bug critico seguridad"
   
   # SIMULACIÓN ERROR: Feature nueva en rama incorrecta
   echo "Feature Nueva" > feature.txt
   git add feature.txt
   git commit -m "feat: login social (equivocado de rama)"
   
   # Volver a main (next) y avanzar
   git checkout main
   echo "Core v2" > core.txt
   git commit -am "feat: core v2 upgrade"
   ```

2. **Misión 1: Mover la feature a la rama correcta:**
   - Estás en `main`. Necesitas la "Feature Nueva" que está en `legacy`, pero NO quieres el "Bug critico" ni mezclar el historial de legacy.
   - Busca el hash del commit "feat: login social" usando `git log legacy`.
   - Usa `cherry-pick` para traer solo ese commit:
     ```bash
     git cherry-pick <hash-del-commit-feature>
     ```
   - Verifica que ahora tienes el archivo `feature.txt` en main.

3. **Misión 2: Limpiar la rama legacy:**
   - Ahora debes quitar esa feature de `legacy` porque no es compatible.
   - Ve a legacy: `git checkout legacy`.
   - Como `legacy` es una rama compartida, NO debes usar reset. Usa `revert` para crear un "anti-commit".
   - `git revert <hash-del-commit-feature>`
   - Verifica que `feature.txt` ha desaparecido (o su contenido eliminado) pero el historial muestra el revert.

4. **Misión 3: Aplicar el hotfix en Main:**
   - El "fix: bug critico seguridad" de `legacy` también afecta a la v2.
   - Busca el hash de ese commit.
   - Estando en `main`, tráelo:
     ```bash
     git checkout main
     git cherry-pick <hash-del-commit-bug>
     ```
   - Si hay conflicto (porque core.txt cambió), resuélvelo aceptando los cambios de ambas partes si es necesario, o adaptando el fix a la v2.

5. **Misión 4: El pánico del Merge (Revert de un Merge):**
   - Simula un merge desastroso. Crea una rama `dev-inestable`:
     ```bash
     git checkout -b dev-inestable
     echo "CODIGO ROTO" >> core.txt
     git commit -am "feat: cambio experimental inestable"
     ```
   - Vuelve a main y haz merge (sin fast-forward para que haya commit de merge):
     ```bash
     git checkout main
     git merge --no-ff dev-inestable
     ```
   - ¡Alerta! Producción caída. Debes deshacer ese merge.
   - Intenta hacer `git revert HEAD`. Git se quejará porque es un merge commit.
   - Debes especificar a qué "padre" volver (normalmente -m 1 es volver a lo que había en main antes del merge):
     ```bash
     git revert -m 1 HEAD
     ```
   - Verifica que "CODIGO ROTO" desapareció de `core.txt`.

**Conceptos clave:**
- **Cherry-pick:** Cirugía de precisión para copiar commits.
- **Revert:** La forma ética de deshacer cambios en ramas públicas.
- **Revertir Merges:** Requiere especificar la línea base (`-m 1`).

---

## 11. Ejercicios avanzados (opcional)

Esta sección contiene ejercicios desafiantes para quienes quieran profundizar aún más. Estos ejercicios son **opcionales** y están diseñados para situaciones profesionales complejas.

### 11.1. Ejercicio E: Git worktree - múltiples working directories (15 minutos)

**Objetivo:** Aprender a trabajar con múltiples ramas simultáneamente usando `git worktree`.

**Escenario:** Necesitas trabajar en un hotfix urgente mientras tienes cambios sin commitear en tu feature actual. No puedes usar stash porque necesitas ambas versiones abiertas al mismo tiempo.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-worktree
   cd ejercicio-worktree
   git init
   echo "main" > app.js
   git add app.js
   git commit -m "init: aplicación base"
   
   # Crea una feature branch con cambios
   git checkout -b feature/nueva-funcionalidad
   echo "// Nueva funcionalidad" >> app.js
   git add app.js
   git commit -m "feat: añadir nueva funcionalidad"
   echo "// Cambios sin commitear" >> app.js
   ```

2. **Crear un worktree para el hotfix:**
   - Necesitas hacer un hotfix en `main` sin perder tus cambios actuales.
   - Crea un worktree adicional:
     ```bash
     git worktree add ../ejercicio-worktree-hotfix main
     ```
   - Esto crea un directorio separado con una copia limpia de `main`.

3. **Trabajar en ambos simultáneamente:**
   - En el directorio original (`ejercicio-worktree`), verifica que tus cambios siguen ahí:
     ```bash
     cat app.js
     ```
   - Ve al nuevo worktree:
     ```bash
     cd ../ejercicio-worktree-hotfix
     ```
   - Crea el hotfix:
     ```bash
     echo "// Hotfix crítico" >> app.js
     git add app.js
     git commit -m "fix: hotfix crítico"
     ```
   - Vuelve al directorio original y verifica que tus cambios siguen intactos:
     ```bash
     cd ../ejercicio-worktree
     cat app.js
     git status
     ```

4. **Limpiar:**
   - Cuando termines, elimina el worktree:
     ```bash
     git worktree remove ../ejercicio-worktree-hotfix
     ```

**Conceptos clave:**
- **Worktree:** Permite tener múltiples working directories del mismo repositorio.
- **Útil para:** Hotfixes urgentes, comparar versiones, builds paralelos.

### 11.2. Ejercicio F: Reescritura avanzada de historial (20 minutos)

**Objetivo:** Dominar técnicas avanzadas de reescritura de historial con `git filter-branch` o `git filter-repo`.

**Escenario:** Necesitas limpiar el historial de un repositorio antes de hacerlo público: eliminar archivos sensibles que fueron commitados por error y cambiar el autor de commits antiguos.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-reescritura
   cd ejercicio-reescritura
   git init
   
   # Crear historial con problemas
   echo "Código público" > public.js
   echo "API_KEY=secret123" > .env
   git add .
   git commit -m "feat: código inicial"
   
   git config user.name "Old Name"
   git config user.email "old@example.com"
   
   echo "Más código" >> public.js
   git add public.js
   git commit -m "feat: más funcionalidades"
   
   # Cambiar config para simular otro autor
   git config user.name "New Name"
   git config user.email "new@example.com"
   
   echo "Aún más código" >> public.js
   git add public.js
   git commit -m "feat: funcionalidades finales"
   ```

2. **Eliminar archivo sensible del historial:**
   - El archivo `.env` con la API key nunca debería haber sido commitado.
   - Usa `git filter-branch` para eliminarlo de todo el historial:
     ```bash
     git filter-branch --force --index-filter \
       "git rm --cached --ignore-unmatch .env" \
       --prune-empty --tag-name-filter cat -- --all
     ```
   - Verifica que `.env` ya no existe en ningún commit:
     ```bash
     git log --all --full-history -- .env
     # No debería mostrar nada
     ```

3. **Cambiar autor de commits antiguos:**
   - Necesitas cambiar el autor de los commits antiguos a tu nombre actual.
   - Primero, identifica los commits con el autor antiguo:
     ```bash
     git log --format="%H %an %ae"
     ```
   - Usa `git filter-branch` para cambiar el autor:
     ```bash
     git filter-branch --force --env-filter '
       if [ "$GIT_AUTHOR_EMAIL" = "old@example.com" ]; then
         export GIT_AUTHOR_NAME="New Name"
         export GIT_AUTHOR_EMAIL="new@example.com"
       fi
     ' --tag-name-filter cat -- --all
     ```

4. **Limpiar referencias:**
   - Después de reescribir el historial, limpia las referencias de respaldo:
     ```bash
     rm -rf .git/refs/original/
     git reflog expire --expire=now --all
     git gc --prune=now --aggressive
     ```

**⚠️ Advertencia importante:**
- `git filter-branch` reescribe el historial. **NUNCA** lo uses en ramas compartidas sin coordinación.
- Si ya hiciste push, necesitarás `git push --force`, lo cual puede romper el trabajo de otros.
- Considera usar `git filter-repo` (más moderno y eficiente) en lugar de `filter-branch`.

### 11.3. Ejercicio G: Hooks avanzados con integración CI/CD (15 minutos)

**Objetivo:** Crear hooks que se integren con tu pipeline de CI/CD y validen código antes de push.

**Escenario:** Quieres asegurar que el código que se sube cumple con estándares de calidad antes de que se ejecute el pipeline costoso en GitLab.

**Instrucciones:**

1. **Preparación:**
   ```bash
   mkdir ejercicio-hooks-avanzados
   cd ejercicio-hooks-avanzados
   git init
   
   # Crear estructura de proyecto simple
   mkdir src
   echo "function hello() { console.log('Hello'); }" > src/app.js
   git add .
   git commit -m "init: proyecto base"
   ```

2. **Crear hook pre-push avanzado:**
   - Crea un hook que valide el código antes de hacer push:
     ```bash
     cat > .git/hooks/pre-push << 'EOF'
     #!/bin/bash
     
     echo "🔍 Ejecutando validaciones pre-push..."
     
     # Validar que no hay console.log en producción
     if git diff --cached --name-only | xargs grep -l "console.log" 2>/dev/null; then
         echo "❌ Error: Se encontraron console.log en archivos staged"
         echo "   Por favor, elimínalos antes de hacer push"
         exit 1
     fi
     
     # Validar formato de commits (conventional commits)
     while read local_ref local_sha remote_ref remote_sha
     do
         if [ "$local_sha" != "0000000000000000000000000000000000000000" ]; then
             commit_msg=$(git log --format=%B -n 1 $local_sha)
             if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"; then
                 echo "❌ Error: El commit no sigue el formato conventional commits"
                 echo "   Formato: tipo(scope): descripción"
                 echo "   Commit: $commit_msg"
                 exit 1
             fi
         fi
     done
     
     echo "✅ Validaciones pasadas. Procediendo con push..."
     exit 0
     EOF
     
     chmod +x .git/hooks/pre-push
     ```

3. **Probar el hook:**
   - Intenta hacer un commit con console.log:
     ```bash
     echo "console.log('test');" >> src/app.js
     git add src/app.js
     git commit -m "feat: añadir logging"
     ```
   - Intenta hacer push (aunque no tengas remote, el hook se ejecutará):
     ```bash
     git push origin main 2>&1 || echo "Hook ejecutado (esperado si no hay remote)"
     ```

4. **Crear hook post-merge para actualizar dependencias:**
   - Crea un hook que actualice dependencias después de un merge:
     ```bash
     cat > .git/hooks/post-merge << 'EOF'
     #!/bin/bash
     
     echo "🔄 Actualizando dependencias después del merge..."
     
     # Simular actualización de dependencias
     if [ -f "package.json" ]; then
         echo "   Ejecutando npm install..."
         # npm install (comentado para el ejercicio)
     fi
     
     echo "✅ Dependencias actualizadas"
     exit 0
     EOF
     
     chmod +x .git/hooks/post-merge
     ```

**Conceptos clave:**
- **Pre-push hooks:** Útiles para validaciones costosas antes de subir código.
- **Post-merge hooks:** Automatizan tareas después de actualizar código.
- **Integración CI/CD:** Los hooks locales complementan (no reemplazan) la validación en CI/CD.

### 11.4. Ejercicio H: Git submodules - gestión de dependencias (20 minutos)

**Objetivo:** Aprender a usar submodules para incluir repositorios dentro de otros repositorios.

**Escenario:** Tienes una librería compartida que usan múltiples proyectos. Quieres incluirla como dependencia pero mantenerla como repositorio independiente.

**Instrucciones:**

1. **Preparación - crear el repositorio de la librería:**
   ```bash
   # Terminal 1: Crear librería
   mkdir mi-libreria
   cd mi-libreria
   git init
   echo "export function saludar() { return 'Hola'; }" > index.js
   git add index.js
   git commit -m "feat: librería base"
   ```

2. **Crear el proyecto principal:**
   ```bash
   # Terminal 2: Crear proyecto
   mkdir mi-proyecto
   cd mi-proyecto
   git init
   echo "import { saludar } from './libs/mi-libreria';" > app.js
   git add app.js
   git commit -m "init: proyecto principal"
   ```

3. **Añadir la librería como submodule:**
   - Desde `mi-proyecto`, añade la librería como submodule:
     ```bash
     # Usa la ruta absoluta o relativa de mi-libreria
     git submodule add ../mi-libreria libs/mi-libreria
     ```
   - Esto crea:
     - Un directorio `libs/mi-libreria` con el código de la librería
     - Un archivo `.gitmodules` que describe el submodule
   - Haz commit de estos cambios:
     ```bash
     git add .gitmodules libs/mi-libreria
     git commit -m "feat: añadir librería como submodule"
     ```

4. **Trabajar con submodules:**
   - Ver el estado de los submodules:
     ```bash
     git submodule status
     ```
   - Actualizar submodules a la última versión:
     ```bash
     git submodule update --remote
     ```
   - Clonar un proyecto con submodules:
     ```bash
     # Si clonas el proyecto desde otro lugar:
     git clone <url> proyecto-clonado
     cd proyecto-clonado
     git submodule init
     git submodule update
     # O en un solo paso:
     git clone --recurse-submodules <url> proyecto-clonado
     ```

5. **Actualizar la librería:**
   - Ve a la librería y haz cambios:
     ```bash
     cd libs/mi-libreria
     echo "export function despedir() { return 'Adiós'; }" >> index.js
     git add index.js
     git commit -m "feat: añadir función despedir"
     ```
   - Vuelve al proyecto principal y actualiza la referencia:
     ```bash
     cd ../..
     git submodule update --remote libs/mi-libreria
     git add libs/mi-libreria
     git commit -m "chore: actualizar submodule a nueva versión"
     ```

**Conceptos clave:**
- **Submodules:** Permiten incluir repositorios como dependencias.
- **Ventajas:** Mantiene repositorios separados, versionado independiente.
- **Desventajas:** Puede ser complejo de gestionar, requiere comandos especiales.
- **Alternativas modernas:** Git subtrees, monorepos con herramientas como Lerna/Nx.

### 11.5. Ejercicio I: El desafío del historial complejo (25 minutos)

**Objetivo:** Resolver un escenario complejo combinando múltiples técnicas avanzadas.

**Escenario:** Tienes un repositorio con un historial desastroso:
- Commits con archivos sensibles
- Múltiples ramas con historial divergente
- Un merge que introdujo un bug crítico
- Necesitas limpiar todo y crear un historial limpio

**Instrucciones:**

1. **Preparación del caos:**
   ```bash
   mkdir ejercicio-caos
   cd ejercicio-caos
   git init
   
   # Crear historial problemático
   echo "Código v1" > app.js
   echo "SECRET_KEY=abc123" > .env
   git add .
   git commit -m "init"
   
   git checkout -b feature/a
   echo "Feature A" >> app.js
   git commit -am "feat: feature a"
   
   git checkout main
   git checkout -b feature/b
   echo "Feature B" >> app.js
   git commit -am "feat: feature b"
   
   git checkout main
   git merge --no-ff feature/a
   git merge --no-ff feature/b
   
   # Introducir bug
   echo "BUG = true" >> app.js
   git commit -am "feat: introducir bug"
   ```

2. **Misión: Limpiar el historial**
   - **Paso 1:** Elimina `.env` de todo el historial usando `git filter-branch`.
   - **Paso 2:** Encuentra el commit que introdujo el bug usando `git bisect`.
   - **Paso 3:** Crea una rama `fix/bug` desde antes del merge problemático.
   - **Paso 4:** Aplica el fix usando `cherry-pick` de los commits buenos de `feature/a` y `feature/b`.
   - **Paso 5:** Haz rebase interactivo para limpiar mensajes de commit.
   - **Paso 6:** Crea un tag `v1.0.0-clean` en el commit limpio.

3. **Verificación:**
   - El historial debe estar limpio, sin `.env`, sin el bug, y con mensajes descriptivos.
   - Usa `git log --oneline --graph --all` para visualizar el resultado.

**Pistas:**
- Combina las técnicas aprendidas en los ejercicios anteriores.
- Usa `git reflog` si te pierdes.
- Considera crear una rama de respaldo antes de empezar: `git branch backup`.

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
