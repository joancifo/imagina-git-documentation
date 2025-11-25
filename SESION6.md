## Índice

- [Git bisect: búsqueda binaria de bugs](#git-bisect-búsqueda-binaria-de-bugs)
  - [¿Cómo funciona?](#cómo-funciona)
  - [Uso básico](#uso-básico)
  - [Ejemplo práctico](#ejemplo-práctico)
  - [Automatización](#automatización)
  - [Ventajas](#ventajas)
- [7. Ejercicios finales de repaso](#7-ejercicios-finales-de-repaso)
  - [7.1. Ejercicio A: El flujo completo (Individual)](#71-ejercicio-a-el-flujo-completo-individual)
  - [7.2. Ejercicio B: Depuración y limpieza (Parejas o Individual)](#72-ejercicio-b-depuración-y-limpieza-parejas-o-individual)
  - [7.3. Ejercicio C: Colaboración y conflictos (En clase / Parejas)](#73-ejercicio-c-colaboración-y-conflictos-en-clase--parejas)
- [Recursos finales](#recursos-finales)

---

## Git bisect: búsqueda binaria de bugs

`git bisect` es una herramienta muy útil para encontrar el commit exacto que introdujo un bug o un problema en tu código. Utiliza un algoritmo de búsqueda binaria para hacerlo de forma eficiente, reduciendo significativamente el número de commits que necesitas revisar manualmente.

### ¿Cómo funciona?

Git bisect funciona dividiendo el rango de commits a la mitad repetidamente. Tú le indicas:
- Un commit "malo" (donde el bug existe)
- Un commit "bueno" (donde el bug no existe)

Git te lleva a un commit intermedio y tú le indicas si ese commit es "bueno" o "malo". El proceso se repite hasta encontrar el commit exacto que introdujo el problema.

### Uso básico

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

### Ejemplo práctico

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

### Automatización

Puedes automatizar el proceso si tienes un script de tests:

```bash
git bisect start HEAD HEAD~50
git bisect run ./test-script.sh
```

Git ejecutará automáticamente el script en cada commit y marcará como "bad" si el script falla (código de salida != 0) o "good" si pasa.

### Ventajas

- **Eficiencia:** En lugar de revisar 100 commits uno por uno, bisect te permite encontrar el bug en aproximadamente log₂(100) ≈ 7 pasos.
- **Precisión:** Encuentra el commit exacto que introdujo el problema.
- **Seguro:** No modifica tu código, solo navega por el historial.

---

## 7. Ejercicios finales de repaso

Esta sección contiene ejercicios integradores para repasar todos los conceptos vistos durante el curso.

### 7.1. Ejercicio A: El flujo completo (Individual)

**Objetivo:** Simular el ciclo de vida completo de una funcionalidad, desde la creación hasta el despliegue, usando buenas prácticas.

**Instrucciones:**

1. **Configuración:** Crea un nuevo repositorio en GitLab llamado `proyecto-final-tunombre`. Clónalo localmente.
2. **Estructura:** Crea una rama `develop` y protégela en GitLab (nadie puede hacer push directo).
3. **Feature:**
   - Crea una rama `feature/login` desde `develop`.
   - Implementa un script de login ficticio (`login.py`).
   - Haz al menos 3 commits con mensajes descriptivos.
4. **Revisión:**
   - Sube tu rama a GitLab.
   - Crea un Merge Request hacia `develop`.
   - En el MR, simula una revisión: añade un comentario en una línea de código.
5. **Corrección:**
   - Haz un cambio en tu rama local para atender el comentario.
   - Usa `git commit --fixup` para el nuevo cambio.
   - Usa `git rebase -i --autosquash` para fusionarlo con el commit original.
   - Haz `git push --force-with-lease` para actualizar el MR.
6. **Merge:** Fusiona el MR en GitLab.
7. **Release:**
   - En local, actualiza `main` con los cambios de `develop` (simulando una release).
   - Crea un tag `v1.0.0` y súbelo.

**Puntos clave a verificar:**
- Historial limpio en `develop` (commits squashed o bien organizados).
- Uso correcto de ramas y protección.
- Manejo de rebase interactivo.

### 7.2. Ejercicio B: Depuración y limpieza (Parejas o Individual)

**Objetivo:** Encontrar un error introducido en el pasado y limpiar un historial desastroso.

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
3. **Limpieza:**
   - Una vez encontrado el bug, crea una rama `fix/bug-div-zero`.
   - Elimina la línea del bug.
   - Usa `git rebase -i` para limpiar los últimos 10 commits del historial (fusionar los "wip", renombrar mensajes).
4. **Entrega:**
   - El historial final debe ser legible y sin el bug.

### 7.3. Ejercicio C: Colaboración y conflictos (En clase / Parejas)

**Objetivo:** Resolver un "infierno de dependencias" y conflictos cruzados.

**Instrucciones:**

1. **Setup:** Crea un repo con un archivo `config.json` de 50 líneas.
2. **Desarrollo paralelo:**
   - **Alumno A:** Crea rama `feature/database`. Modifica las líneas 10-20 de `config.json` y cambia el formato de indentación de todo el archivo.
   - **Alumno B:** Crea rama `feature/ui`. Modifica las líneas 15-25 de `config.json` (solapándose con A) y renombra una clave que A también modificó.
3. **El conflicto:**
   - Alumno A hace push y merge a `main`.
   - Alumno B intenta hacer rebase de su rama sobre `main`.
4. **Resolución:**
   - Alumno B debe resolver los conflictos.
   - Debe decidir qué cambios conservar (lógica de ambos, indentación de A).
   - Usar `git checkout --ours` o `--theirs` para partes específicas si es útil.
   - Usar una herramienta de diff visual (VSCode) es recomendado.
5. **Finalización:**
   - Alumno B logra un rebase exitoso y hace push.
   - El archivo final debe ser JSON válido.

---

## Recursos finales

¡Felicidades por llegar al final del curso! Aquí tienes una lista de recursos para seguir aprendiendo:

- **Documentación oficial:** [git-scm.com/doc](https://git-scm.com/doc)
- **GitLab Docs:** [docs.gitlab.com](https://docs.gitlab.com)
- **Juego interactivo:** [Learn Git Branching](https://learngitbranching.js.org/)
- **Libro:** *Pro Git* (gratuito y excelente).

Recuerda: **Git es una herramienta poderosa pero compleja. La práctica constante es la única forma de dominarla.** ¡Mucha suerte!
