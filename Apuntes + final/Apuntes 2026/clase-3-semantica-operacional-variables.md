# Clase 3 — Semántica operacional: ligadura y variables

## 1. Entidades, atributos y ligadura

- Los programas trabajan con **entidades**: variables, rutinas, sentencias.
- Cada entidad tiene **atributos** (variable: nombre, tipo, memoria…; rutina: nombre, parámetros…).
- **Descriptor**: lugar donde se guardan los atributos.
- **Ligadura (binding)**: asociación entre una entidad y un atributo. **Función**: los atributos deben estar fijados antes de poder usar la entidad.

Los lenguajes difieren en: cuántas entidades y atributos tienen, **cuándo** se hace la ligadura y su **estabilidad** (si puede cambiar).

### Momentos de ligadura
1. Definición del lenguaje — ej.: la palabra `int`, la forma de las sentencias.
2. Implementación — ej.: representación y rango de los enteros.
3. Compilación — ej.: en `int a` se liga el tipo a la variable.
4. Ejecución — ej.: el valor de `a` y su lugar de memoria.

(1–3 son **estáticos**, 4 es **dinámico**.)

### Ligadura estática y dinámica
- **Estática**: se establece **antes** de la ejecución y **no puede cambiar**.
- **Dinámica**: se establece **en ejecución** y **puede cambiar** según reglas del lenguaje.
- Excepción: las **constantes** (se ligan en ejecución en algunos lenguajes, pero no cambian).

## 2. Variable

Abstracción de la celda de memoria; la asignación modifica destructivamente su valor.
Atributos: **\<nombre, alcance, tipo, l-valor, r-valor\>**

### Nombre
String con el que se referencia la variable. Aspectos de diseño: longitud máxima, caracteres permitidos (`_`, `$`), sensibilidad a mayúsculas (C y Python sí, Pascal no), palabras reservadas.

### Alcance
Rango de instrucciones donde el nombre es **conocido** (visibilidad).

- **Alcance estático (léxico)**: se determina mirando el **texto** del programa, sin ejecutarlo. La mayoría de los lenguajes (C, Pascal, Ada, Python). El nombre vale desde su declaración hacia los bloques anidados, salvo que se redeclare.
- **Alcance dinámico**: se determina por el **orden de ejecución** (cadena de llamadas): vale la última declaración ejecutada. APL, Lisp original, Perl, Tcl. Más fácil de implementar pero menos legible.

Conceptos asociados: **local** (creada en la unidad), **no local** (usada en la unidad pero creada en otra), **global** (creada en el programa principal). Python usa `global` y `nonlocal`.

**Espacio de nombres**: zona con nombre propio donde se declaran identificadores; evita choques de nombres en proyectos grandes.

### Tipo
Conjunto de **valores** y de **operaciones**. Protege a la variable de operaciones no permitidas (chequeo de tipos).

- **Predefinidos**: tipos base del lenguaje (ej.: boolean: true/false; and, or, not).
- **Definidos por el usuario**: con constructores (ej.: `type t = array[1..10] of integer`).
- **TAD**: el programador define representación y operaciones.

Momento de ligadura del tipo:
- **Estático** (compilación, no cambia): Fortran, Pascal, C, Java, Ada.
  - *Explícito*: por declaración (`int x;`).
  - *Implícito*: por reglas (Fortran: I..N enteras, el resto reales).
  - *Inferido*: se deduce de la expresión (lenguajes funcionales).
- **Dinámico** (ejecución, puede cambiar): Python, Ruby, JavaScript, APL. Más flexible, pero más costoso, chequeo dinámico y menos legible.

### L-valor
Área de **memoria** ligada a la variable.
- **Tiempo de vida**: período en que existe esa ligadura (mientras está alocada).
- **Alocación**: momento en que se reserva la memoria.

Clasificación según alocación / tiempo de vida:

| Tipo | Cuándo se aloca | Tiempo de vida |
|---|---|---|
| **Estática** | Antes de ejecutar | Todo el programa. **Sensible a la historia** (conserva el valor entre llamadas) |
| **Dinámica automática** | Al aparecer la declaración (activar la unidad) | Mientras dure la unidad |
| **Dinámica explícita** | Con un constructor (`new`, `malloc`) | Hasta que se libere |
| **Persistente** | — | No depende de la ejecución (archivos, bases de datos) |

### R-valor
**Valor** almacenado en el l-valor; se interpreta según el tipo. En `x := x + 1`: a la izquierda se usa el l-valor, a la derecha el r-valor.
- Es **dinámico** por naturaleza.
- **Constante**: el valor se congela. Pascal: ligadura estática; Ada: dinámica pero estable.
- **Inicialización**: ignorar (queda lo que haya en memoria), por defecto (0, blanco) o en la declaración (`int i = 0`).

## 3. Variables anónimas y referencias (punteros)

El r-valor de una variable puede ser una **referencia al l-valor de otra**. Ej.: `new(pxi)` aloca una variable anónima y deja el puntero apuntándola.

## 4. Alias y sobrecarga

- **Alias**: **distintos nombres, una entidad**. Dos variables comparten el mismo objeto en el mismo ambiente.
  ```c
  int x = 0; int *i = &x; int *j = &x;
  *i = 10;   // x y *j también valen 10
  ```
  - *Ventaja*: compartir datos sin copiar (ahorra memoria y tiempo).
  - *Peligros*: programas difíciles de leer, errores, **efectos laterales** (se modifica algo por otro camino).
  - *¿Se pueden evitar?* Sí: no usando punteros/pasaje por referencia, o con lenguajes que los restringen (Java elimina el puntero explícito).
- **Sobrecarga**: **un nombre, distintas entidades**. Ej.: `+` para enteros y reales. No hay ambigüedad porque los tipos permiten resolverlo en compilación.

---

## Preguntas de final relacionadas

- **1** — *Estática, global, constante, puntero* y los atributos: **estática** ↔ l-valor (tiempo de vida); **global** ↔ alcance; **constante** ↔ r-valor (no cambia); **puntero** ↔ tipo / r-valor (su r-valor es el l-valor de otra variable).
- **7b, 13, 14, 29** — Definir variable, enumerar y clasificar por atributos: punto 2.
- **12, 20, 28** — Ligadura, su función, estática y dinámica: punto 1.
- **15** — ¿Global = estática? **No**. Global habla del *alcance*, estática del *tiempo de vida*. Una global suele ser estática, pero una estática puede ser local (`static int c;` dentro de una función en C: vive todo el programa pero solo se ve en la función).
- **18** — Estática vs. constante: la estática fija el **l-valor** (el valor puede cambiar); la constante fija el **r-valor**.
- **19** — Por qué usar constantes: legibilidad, modificabilidad (se cambia en un solo lugar) y protección contra cambios accidentales.
- **21, 57** — Alias: punto 4.
- **36** — ¿L-valor se relaciona con alcance? Son atributos **distintos**: alcance = dónde se conoce el nombre; l-valor = dónde está y cuánto vive. Pueden no coincidir (variable estática local: sigue viva fuera de su alcance).
- **16, 41, 49** — Clasificación por tiempo de vida. Los **punteros** son variables comunes (automáticas, en la pila) pero apuntan a variables **dinámicas explícitas** (anónimas, en la heap). Problemas: ver Clase 7.
