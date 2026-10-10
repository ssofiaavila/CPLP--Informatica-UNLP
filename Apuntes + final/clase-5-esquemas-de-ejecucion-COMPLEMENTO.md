# Clase 5 — Esquemas de ejecución basados en pila y dinámico (COMPLEMENTO)

> ⚠️ **Atención**: el PDF "clase 5 – unidades de programas" que subiste es idéntico al de la clase 4, así que **no tengo las diapositivas de esta clase**. Lo de abajo **no sale de tus apuntes**: lo armé a partir de la bibliografía de la cátedra (Ghezzi–Jazayeri) porque varias preguntas de final (30, 16, 17, 42, 43, 47, 56) apuntan a estos temas. Verificalo contra la clase real cuando la consigas.

## 1. Por qué hace falta la pila

En C1/C2 (estático) hay un RA fijo por rutina → no hay recursión. Con recursión no se sabe cuántas activaciones habrá, pero sí que terminan en orden inverso (LIFO) → **pila de registros de activación**.

## 2. C3 = C2 + recursión + valor de retorno

Cada llamada crea un RA nuevo en el tope de la pila; al retornar se elimina.

**Registro de activación en pila**

| Elemento | ¿Estaba en el esquema estático? |
|---|---|
| Punto de retorno | Sí |
| Variables locales | Sí |
| **Enlace dinámico** (puntero al RA de la unidad llamadora) | Se agrega |
| **Valor de retorno** (lugar para el resultado de la función) | Se agrega |
| Parámetros | Se agregan al pasar a rutinas con parámetros |

**Elementos para controlar la zona de datos** (pregunta 47b):
- **current**: dirección base del RA de la unidad en ejecución.
- **free**: próxima celda libre (tope de la pila).
- **Cadena dinámica**: los enlaces dinámicos encadenados = secuencia de llamadas.

Las variables locales se acceden por **desplazamiento (offset)** respecto de `current`: el offset se conoce en compilación, la dirección real recién en ejecución.

## 3. C4 = estructura de bloque (anidamiento)

- C4': bloques anidados dentro de una rutina. C4'': rutinas anidadas.
- Para acceder a variables **no locales** con alcance estático se agrega el **enlace estático**: puntero al RA de la unidad que la contiene textualmente. Encadenados forman la **cadena estática**.

## 4. C5 = datos de tamaño variable

- **C5' – semidinámicas**: arreglos cuyo tamaño se conoce recién **al activar la unidad** (ej.: `a: array(1..n)`). En el RA va un **descriptor** de tamaño fijo (puntero + límites) y los datos se alocan en la pila sobre el RA.
- **C5'' – dinámicas**: el tamaño cambia durante la ejecución o se crean con `new`. No siguen LIFO → van a la **heap**; en la pila queda solo el puntero.

## 5. C6 = lenguajes dinámicos

Tipado dinámico y/o **alcance dinámico** (APL, Lisp, Snobol). Las no locales se buscan por la **cadena dinámica**. El uso de memoria es impredecible.

## 6. Clasificación de variables por su l-valor

| Tipo | Cuándo se liga el l-valor | Dónde vive |
|---|---|---|
| **Estática** | Antes de la ejecución; dirección absoluta fija | Zona estática. Todo el programa; sensible a la historia |
| **Automática / semiestática** | Al activar la unidad; tamaño y offset conocidos en compilación | Pila (dentro del RA) |
| **Semidinámica** | Al activar la unidad; el tamaño se conoce recién ahí | Pila (descriptor en el RA + datos arriba) |
| **Dinámica** | En cualquier momento de la ejecución; tamaño variable | Heap |

- **Estática vs. semiestática** (pregunta 17): la estática tiene una única dirección para toda la ejecución y conserva su valor entre llamadas; la semiestática se crea de nuevo en cada activación (una por cada RA), por eso permite recursión y no conserva el valor.

## 7. Comparación de los tres esquemas

| | Estático | Basado en pila | Dinámico |
|---|---|---|---|
| Memoria | Fija, conocida en compilación | Predecible, LIFO | Impredecible, heap |
| Alocación | Antes de ejecutar | Al entrar a la unidad | Cuando se necesita |
| Recursión | No | Sí | Sí |
| Ejemplo | Fortran, Cobol (C1, C2) | Algol, Pascal, C (C3–C5') | Lisp, APL, Python (C5'', C6) |

**Ventajas de pila sobre estático** (47c): permite recursión; usa memoria solo para las unidades activas (se libera al retornar); admite datos cuyo tamaño se conoce en ejecución.

---

## Preguntas de final relacionadas

- **30** — Qué comparten y qué se agrega en el RA basado en pila: tabla del punto 2 (+ enlace estático en C4).
- **16** — Automáticas, semidinámicas y dinámicas: punto 6.
- **17** — Estática vs. semiestática: punto 6.
- **42, 43** — Esquema estático: sin automáticas ni recursión (ver también Clase 4).
- **47, 56** — Manejo de memoria en los tres esquemas: puntos 2 y 7.
