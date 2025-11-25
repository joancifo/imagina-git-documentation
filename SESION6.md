# Sesión 6: Git avanzado, CI/CD con GitLab CI y prácticas profesionales

## Índice de la sesión

1. [Git bisect: búsqueda binaria de bugs](#1-git-bisect-búsqueda-binaria-de-bugs) (30 minutos)
2. [Git hooks: automatización local](#2-git-hooks-automatización-local) (40 minutos)
3. [Introducción a CI/CD](#3-introducción-a-cicd) (30 minutos)
4. [GitLab CI/CD: fundamentos](#4-gitlab-cicd-fundamentos) (60 minutos)
5. [GitLab CI/CD: conceptos avanzados](#5-gitlab-cicd-conceptos-avanzados) (50 minutos)
6. [GitLab CI/CD: prácticas y patrones](#6-gitlab-cicd-prácticas-y-patrones) (45 minutos)
7. [Integración de Git con CI/CD](#7-integración-de-git-con-cicd) (30 minutos)
8. [Observación y comprensión de CI/CD en GitLab](#8-observación-y-comprensión-de-cicd-en-gitlab) (30 minutos)
9. [Ejercicios finales de repaso](#9-ejercicios-finales-de-repaso) (75 minutos)

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

CI/CD (Continuous Integration / Continuous Deployment) es una práctica de desarrollo de software que automatiza la integración, pruebas y despliegue de código.

### 3.1. ¿Qué es CI/CD?

**Continuous Integration (CI):**
- Integración continua del código de múltiples desarrolladores
- Ejecución automática de tests en cada cambio
- Detección temprana de problemas
- Construcción automática de artefactos

**Continuous Deployment/Delivery (CD):**
- **Continuous Delivery:** El código está siempre listo para desplegarse
- **Continuous Deployment:** El código se despliega automáticamente a producción

### 3.2. Beneficios de CI/CD

1. **Detección temprana de bugs:** Los problemas se encuentran antes de llegar a producción
2. **Despliegues más rápidos:** Automatización reduce el tiempo de despliegue
3. **Mayor confianza:** Tests automatizados dan seguridad al equipo
4. **Mejor colaboración:** Integración continua facilita el trabajo en equipo
5. **Rollback rápido:** Si algo falla, se puede revertir rápidamente

### 3.3. Pipeline de CI/CD

Un pipeline es una serie de pasos automatizados que se ejecutan en secuencia:

```
Código → Build → Test → Deploy → Monitoreo
```

**Etapas típicas:**
1. **Build:** Compilar el código
2. **Test:** Ejecutar tests unitarios, de integración, etc.
3. **Lint:** Verificar calidad de código
4. **Security scan:** Buscar vulnerabilidades
5. **Deploy:** Desplegar a diferentes ambientes (dev, staging, prod)

### 3.4. GitLab CI/CD

GitLab incluye un sistema completo de CI/CD integrado:
- **GitLab CI:** Motor de CI/CD integrado
- **GitLab Runner:** Ejecutor de jobs
- **GitLab Pages:** Hosting estático
- **Container Registry:** Registro de imágenes Docker

### 3.5. Conceptos clave

- **Pipeline:** Conjunto de jobs que se ejecutan en un commit
- **Stage:** Fase del pipeline (build, test, deploy)
- **Job:** Tarea individual que se ejecuta en un stage
- **Runner:** Máquina que ejecuta los jobs
- **Artifact:** Archivo generado por un job y disponible para otros jobs

---

## 4. GitLab CI/CD: fundamentos

### 4.1. Archivo `.gitlab-ci.yml`

El archivo `.gitlab-ci.yml` define la configuración del pipeline. Debe estar en la raíz del repositorio.

**Estructura básica:**

```yaml
# .gitlab-ci.yml

stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script:
    - echo "Building the application..."
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/

test-job:
  stage: test
  script:
    - echo "Running tests..."
    - npm test
  dependencies:
    - build-job

deploy-job:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./deploy.sh
  only:
    - main
```

### 4.2. Stages (etapas)

Los stages definen el orden de ejecución de los jobs:

```yaml
stages:
  - build      # Primera etapa
  - test       # Segunda etapa
  - deploy     # Tercera etapa
```

Los jobs en el mismo stage se ejecutan en paralelo (si hay runners disponibles).

### 4.3. Jobs

Un job es una tarea que se ejecuta en un stage específico:

```yaml
job-name:
  stage: build
  script:
    - echo "Hello from GitLab CI"
```

**Elementos de un job:**
- `stage`: Etapa en la que se ejecuta
- `script`: Comandos a ejecutar
- `image`: Imagen Docker a usar
- `before_script`: Comandos antes del script principal
- `after_script`: Comandos después del script principal
- `artifacts`: Archivos a guardar
- `dependencies`: Jobs de los que depende
- `only/except`: Cuándo ejecutar el job
- `when`: Cuándo ejecutar (on_success, on_failure, always, manual)

### 4.4. Variables de entorno

Puedes definir variables en varios lugares:

**En el archivo `.gitlab-ci.yml`:**
```yaml
variables:
  NODE_VERSION: "18"
  DATABASE_URL: "postgresql://localhost/mydb"

build-job:
  script:
    - echo "Using Node $NODE_VERSION"
```

**En GitLab UI:**
Settings → CI/CD → Variables

**Variables predefinidas:**
- `CI_COMMIT_SHA`: Hash del commit
- `CI_COMMIT_REF_NAME`: Nombre de la rama
- `CI_PROJECT_NAME`: Nombre del proyecto
- `CI_JOB_NAME`: Nombre del job actual
- `CI_PIPELINE_ID`: ID del pipeline

### 4.5. Ejemplo completo: Pipeline para aplicación Node.js

```yaml
# .gitlab-ci.yml

image: node:18

stages:
  - install
  - lint
  - test
  - build
  - deploy

variables:
  NODE_ENV: "test"

cache:
  paths:
    - node_modules/

install-dependencies:
  stage: install
  script:
    - npm ci
  artifacts:
    paths:
      - node_modules/
    expire_in: 1 hour

lint-code:
  stage: lint
  script:
    - npm run lint
  dependencies:
    - install-dependencies

unit-tests:
  stage: test
  script:
    - npm run test:unit
  coverage: '/Coverage: \d+\.\d+%/'
  dependencies:
    - install-dependencies

integration-tests:
  stage: test
  script:
    - npm run test:integration
  dependencies:
    - install-dependencies

build-application:
  stage: build
  script:
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 week
  dependencies:
    - install-dependencies

deploy-staging:
  stage: deploy
  script:
    - echo "Deploying to staging..."
    - ./scripts/deploy.sh staging
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy-production:
  stage: deploy
  script:
    - echo "Deploying to production..."
    - ./scripts/deploy.sh production
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

### 4.6. Condiciones: only y except

Controla cuándo se ejecuta un job:

```yaml
# Solo en la rama main
deploy-production:
  only:
    - main

# Solo en tags
release:
  only:
    - tags

# Solo en merge requests
review:
  only:
    - merge_requests

# Excluir ciertas ramas
test:
  except:
    - main
    - develop

# Múltiples condiciones
deploy:
  only:
    - main
    - tags
  except:
    - schedules
```

### 4.7. Artifacts

Los artifacts son archivos generados por un job y disponibles para otros jobs:

```yaml
build:
  script:
    - npm run build
  artifacts:
    paths:
      - dist/
      - build/
    exclude:
      - "*.log"
    expire_in: 1 week
    reports:
      junit: test-results.xml
```

**Tipos de artifacts:**
- `paths`: Archivos y directorios a guardar
- `exclude`: Archivos a excluir
- `expire_in`: Tiempo de expiración
- `reports`: Reportes especiales (junit, coverage, etc.)

### 4.8. Cache

El cache acelera builds subsecuentes:

```yaml
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .cache/

build:
  script:
    - npm ci
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
    policy: pull-push
```

---

## 5. GitLab CI/CD: conceptos avanzados

### 5.1. Imágenes Docker

Puedes usar imágenes Docker para ejecutar tus jobs:

```yaml
# Imagen global
image: node:18

# Imagen por job
test-job:
  image: node:18-alpine
  script:
    - npm test

# Múltiples servicios
integration-test:
  image: node:18
  services:
    - postgres:14
    - redis:7
  variables:
    POSTGRES_DB: testdb
    POSTGRES_USER: testuser
    POSTGRES_PASSWORD: testpass
  script:
    - npm run test:integration
```

### 5.2. Jobs paralelos

Ejecuta múltiples instancias del mismo job:

```yaml
test:
  stage: test
  parallel: 5
  script:
    - npm run test -- --shard=$CI_NODE_INDEX/$CI_NODE_TOTAL
```

### 5.3. Matrices (matrix jobs)

Ejecuta un job con múltiples combinaciones:

```yaml
test:
  stage: test
  parallel:
    matrix:
      - NODE_VERSION: ["16", "18", "20"]
        OS: ["ubuntu-latest", "alpine"]
  image: node:$NODE_VERSION-$OS
  script:
    - npm test
```

### 5.4. Jobs manuales

Jobs que requieren intervención manual:

```yaml
deploy-production:
  stage: deploy
  script:
    - ./deploy.sh
  when: manual
  only:
    - main
```

### 5.5. Jobs condicionales

```yaml
deploy:
  script:
    - ./deploy.sh
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
      when: manual
    - if: '$CI_COMMIT_BRANCH == "develop"'
      when: on_success
    - when: never
```

### 5.6. Incluir archivos externos

Puedes incluir configuraciones de otros archivos:

```yaml
include:
  - local: '/templates/.gitlab-ci-template.yml'
  - remote: 'https://example.com/ci-template.yml'
  - template: Security/SAST.gitlab-ci.yml
```

### 5.7. Templates y extends

Reutiliza configuraciones:

```yaml
.tests:
  script:
    - npm test
  only:
    - merge_requests

unit-tests:
  extends: .tests
  script:
    - npm run test:unit

integration-tests:
  extends: .tests
  script:
    - npm run test:integration
```

### 5.8. Environments

Gestiona diferentes ambientes:

```yaml
deploy-staging:
  stage: deploy
  script:
    - ./deploy.sh staging
  environment:
    name: staging
    url: https://staging.example.com
    deployment_tier: testing

deploy-production:
  stage: deploy
  script:
    - ./deploy.sh production
  environment:
    name: production
    url: https://example.com
    deployment_tier: production
    on_stop: stop-production

stop-production:
  stage: deploy
  script:
    - ./stop.sh
  environment:
    name: production
    action: stop
  when: manual
  only:
    - main
```

### 5.9. Secrets y variables protegidas

```yaml
deploy:
  script:
    - echo $SECRET_KEY
  variables:
    SECRET_KEY:
      value: $CI_SECRET_KEY
      protected: true
      masked: true
```

### 5.10. Retry y timeout

```yaml
test:
  script:
    - npm test
  retry:
    max: 2
    when:
      - runner_system_failure
      - stuck_or_timeout_failure
  timeout: 30m
```

---

## 6. GitLab CI/CD: prácticas y patrones

### 6.1. Mejores prácticas

**1. Mantén los pipelines rápidos:**
- Usa cache para dependencias
- Ejecuta jobs en paralelo cuando sea posible
- Usa artifacts para compartir resultados

**2. Organiza los stages lógicamente:**
```yaml
stages:
  - validate    # Lint, formato
  - build       # Compilar
  - test        # Tests unitarios, integración
  - security    # Scans de seguridad
  - deploy      # Despliegue
```

**3. Usa templates para reutilizar:**
```yaml
.node-template:
  image: node:18
  before_script:
    - npm ci
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
```

**4. Documenta tus pipelines:**
```yaml
# Este job valida el formato del código
lint:
  stage: validate
  script:
    - npm run lint
```

**5. Maneja errores apropiadamente:**
```yaml
deploy:
  script:
    - ./deploy.sh || exit 1
  after_script:
    - ./cleanup.sh
  allow_failure: false
```

### 6.2. Patrones comunes

**Patrón: Build una vez, despliega múltiples veces**

```yaml
build:
  stage: build
  script:
    - npm run build
  artifacts:
    paths:
      - dist/

deploy-staging:
  stage: deploy
  script:
    - ./deploy.sh staging
  dependencies:
    - build
  only:
    - develop

deploy-production:
  stage: deploy
  script:
    - ./deploy.sh production
  dependencies:
    - build
  only:
    - main
```

**Patrón: Tests en paralelo**

```yaml
test-unit:
  stage: test
  script:
    - npm run test:unit

test-integration:
  stage: test
  script:
    - npm run test:integration

test-e2e:
  stage: test
  script:
    - npm run test:e2e
```

**Patrón: Deploy condicional por rama**

```yaml
deploy:
  stage: deploy
  script:
    - |
      if [ "$CI_COMMIT_REF_NAME" == "main" ]; then
        ./deploy.sh production
      elif [ "$CI_COMMIT_REF_NAME" == "develop" ]; then
        ./deploy.sh staging
      else
        ./deploy.sh preview
      fi
```

### 6.3. Optimización de pipelines

**1. Cache inteligente:**
```yaml
cache:
  key:
    files:
      - package-lock.json
  paths:
    - node_modules/
```

**2. Jobs paralelos:**
```yaml
test:
  parallel: 4
  script:
    - npm run test -- --shard=$CI_NODE_INDEX/$CI_NODE_TOTAL
```

**3. Early exit:**
```yaml
lint:
  stage: validate
  script:
    - npm run lint
  allow_failure: false

# Si lint falla, no se ejecutan los siguientes stages
```

### 6.4. Seguridad en CI/CD

**1. No exponer secrets:**
```yaml
# ❌ MAL
deploy:
  script:
    - echo "Password: mypassword123"

# ✅ BIEN
deploy:
  script:
    - echo "Password: $DEPLOY_PASSWORD"
  variables:
    DEPLOY_PASSWORD:
      value: $CI_DEPLOY_PASSWORD
      masked: true
```

**2. Usa variables protegidas:**
- Configura variables en GitLab UI
- Marca como "protected" para que solo se usen en ramas protegidas
- Marca como "masked" para ocultar en logs

**3. Scans de seguridad:**
```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Dependency-Scanning.gitlab-ci.yml
  - template: Security/Container-Scanning.gitlab-ci.yml
```

### 6.5. Monitoreo y notificaciones

```yaml
deploy:
  script:
    - ./deploy.sh
  after_script:
    - |
      if [ $CI_JOB_STATUS == "success" ]; then
        curl -X POST $SLACK_WEBHOOK -d "Deploy exitoso"
      else
        curl -X POST $SLACK_WEBHOOK -d "Deploy falló"
      fi
```

---

## 7. Integración de Git con CI/CD

### 7.1. Estrategias de ramas y CI/CD

**Git Flow con CI/CD:**

```yaml
stages:
  - test
  - build
  - deploy

# Tests en todas las ramas
test:
  stage: test
  script:
    - npm test
  except:
    - main

# Build en develop y feature branches
build:
  stage: build
  script:
    - npm run build
  only:
    - develop
    - /^feature\/.*$/

# Deploy automático a staging desde develop
deploy-staging:
  stage: deploy
  script:
    - ./deploy.sh staging
  only:
    - develop

# Deploy manual a production desde main
deploy-production:
  stage: deploy
  script:
    - ./deploy.sh production
  only:
    - main
  when: manual
```

### 7.2. Tags y releases

**Pipeline para releases:**

```yaml
release:
  stage: deploy
  script:
    - echo "Creating release $CI_COMMIT_TAG"
    - ./create-release.sh $CI_COMMIT_TAG
  only:
    - tags
  when: manual

build-release:
  stage: build
  script:
    - npm run build
    - tar -czf release-$CI_COMMIT_TAG.tar.gz dist/
  artifacts:
    paths:
      - release-*.tar.gz
    expire_in: 1 year
  only:
    - tags
```

### 7.3. Merge Requests y CI/CD

**Pipeline para MRs:**

```yaml
# Solo ejecutar en MRs
review:
  stage: test
  script:
    - npm run lint
    - npm test
    - npm run test:coverage
  coverage: '/Coverage: \d+\.\d+%/'
  only:
    - merge_requests

# Comparar coverage con la rama base
coverage-diff:
  stage: test
  script:
    - npm run test:coverage
    - ./compare-coverage.sh
  only:
    - merge_requests
```

### 7.4. Hooks de Git y CI/CD

Puedes combinar hooks locales con CI/CD:

```bash
#!/bin/bash
# .git/hooks/pre-push

# Ejecutar tests locales antes de push
npm test

if [ $? -ne 0 ]; then
    echo "Tests fallaron. Push abortado."
    exit 1
fi

# CI/CD ejecutará tests más completos en el servidor
echo "Tests locales pasados. CI/CD ejecutará tests completos."
```

### 7.5. Git bisect con CI/CD

Usa CI/CD para automatizar git bisect:

```yaml
bisect-test:
  script:
    - npm test
  rules:
    - if: '$CI_PIPELINE_SOURCE == "push" && $BISECT_MODE == "true"'
```

Luego en local:
```bash
git bisect start HEAD HEAD~50
git bisect run git push origin HEAD --force
```

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

## 9. Ejercicios finales de repaso

Esta sección contiene ejercicios integradores para repasar todos los conceptos de Git vistos durante el curso.

### 9.1. Ejercicio A: El flujo completo (Individual) (30 minutos)

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

### 9.2. Ejercicio B: Depuración y limpieza (Parejas o Individual) (25 minutos)

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

### 9.3. Ejercicio C: Colaboración y conflictos (En clase / Parejas) (20 minutos)

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
