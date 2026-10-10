# Clase 6 — Rutinas: formas de comunicación y pasaje de parámetros

## 1. Rutinas

Unidad de programa: conjunto de sentencias que representa una **acción abstracta**. Amplían el lenguaje y dan modularidad y claridad. Responden al esquema **call/return**.

- **Procedimiento**: da nombre a un grupo de sentencias; crea *nuevas acciones*. Puede no recibir ni devolver nada; sus resultados salen por variables no locales o parámetros.
- **Función**: además **devuelve un valor**; crea *nuevas operaciones*. Se invoca dentro de expresiones; idealmente sin efectos colaterales.

Al diseñarla importa el *cómo*; al usarla solo el *qué* (implementación oculta). Una definición → muchas activaciones.

## 2. Formas de compartir datos entre unidades

### a) Ambiente no local
- **Implícito**: automático, según la regla de alcance (estático o dinámico).
- **Común explícito**: el programador declara qué comparte en un área común. Ej.: `COMMON` de Fortran, variables externas de PL/1, paquetes de Ada.

### b) Pasaje de parámetros (la mejor forma)
- **Parámetro real (argumento)**: está en la invocación; puede ser valor, variable o expresión.
- **Parámetro formal**: está en la declaración; es una **variable local** de la rutina.

**Ventajas de los parámetros sobre el ambiente no local**: datos distintos en cada invocación, más flexibilidad y abstracción, **protección** (las variables no quedan visibles donde no hace falta), **legibilidad** y **modificabilidad** (los errores se buscan en lo que se comparte).
**Desventaja del ambiente no local**: pérdida de control, efectos colaterales, errores difíciles de encontrar. (Su ventaja: no hay costo de pasar datos.)

**Evaluación y ligadura**: primero se evalúan los reales y luego se ligan. Ligadura **posicional** o **por nombre/palabra clave** (hay que conocer los nombres de los formales). Varios lenguajes admiten valores por defecto (C++, Ada, Python, JS).

## 3. Datos como parámetros: modos

| Modo | Conexión | Formas |
|---|---|---|
| **IN** | Al inicio y se corta | Por valor, por valor constante |
| **OUT** | Al final | Por resultado, resultado de funciones |
| **IN/OUT** | Al inicio y al final | Valor-resultado, referencia, nombre |

### IN — por valor
El valor del real **se copia** al formal, que actúa como variable local.
- ✔ Protege el dato del llamador (no se modifica); sin efectos colaterales.
- ✘ Costo de tiempo y espacio por la copia (grave con datos grandes).

### IN — por valor constante
Se pasa un valor que la rutina **no puede modificar**. Ada (modo `in`), C/C++ con `const`.
- ✔ Protege los datos. ✘ Más trabajo para controlar que no se modifique.

### OUT — por resultado
El formal es una variable local **sin valor inicial**; al terminar, su valor **se copia** al real.
- ✔ Protege el real durante la ejecución.
- ✘ Costo de la copia; hay que inicializar el formal dentro de la rutina.

### OUT — resultado de funciones
El valor devuelto reemplaza a la invocación en la expresión. Con `return` (C, Python) o asignando al nombre de la función (Pascal).

### IN/OUT — por valor-resultado
Se copia al entrar y se copia de vuelta al salir; en el medio la rutina trabaja sobre su copia local. Tiene ventajas y desventajas de ambos.

### IN/OUT — por referencia (o "variable")
Se pasa la **dirección (l-valor)** del real. El formal es una variable local que contiene esa dirección; cada uso es un acceso indirecto al ambiente no local. Todo cambio impacta **inmediatamente** en el real.
- ✔ Eficiente en tiempo y espacio (no copia).
- ✘ Acceso más lento por la indirección; se puede modificar el real sin querer; genera **alias** → menos legible y confiable.

### IN/OUT — por nombre
El formal se **sustituye textualmente** por la expresión del real; la ligadura de valor se **difiere**: se evalúa **cada vez que se usa** (evaluación diferida), en el ambiente del llamador. Se implementa con **thunks** (procedimientos sin nombre).
- Si el real es una variable simple → se comporta como referencia.
- Si es una constante → como por valor.
- Si es un elemento de arreglo `a[i]` o una expresión → puede dar otro resultado si cambia `i`.
- ✔ Flexible. ✘ Lento, difícil de implementar, confuso.

### Referencia vs. nombre (pregunta clásica)
```
m := 4;  B(a[m]);        -- dentro de B(j):  m := m+1;  j := j+5;
```
- **Referencia**: la dirección se calcula **una vez** al llamar → `j` es siempre `a[4]`.
- **Nombre**: `j` es el texto `a[m]`, se recalcula **en cada uso** → después de `m := m+1` pasa a ser `a[5]`.

### Valor-resultado vs. referencia
Suelen dar el mismo resultado pero son semánticamente distintos. Difieren cuando hay alias: con `B(m)` y `j := j+5; j := j+m`, por referencia `m` cambia al instante (4→9→18), por valor-resultado no (j: 4→9→13, y `m` recién vale 13 al salir).

### Más seguro / más inseguro
- **Más seguro**: por valor (o valor constante): el real nunca se modifica.
- **Más inseguro**: por referencia (alias, modificaciones inadvertidas) y por nombre (resultados difíciles de prever).

## 4. Pasaje de parámetros en los lenguajes

| Lenguaje | Formas |
|---|---|
| **C** | Por valor (referencia simulada con punteros); `const` para valor constante |
| **Pascal** | Por valor (defecto), por referencia (`var`) |
| **C++** | Como C + referencia |
| **Java** | Solo copia de valor; los no primitivos son referencias, así que se comporta como referencia |
| **PHP** | Por valor (defecto), por referencia (`&`) |
| **Python / Ruby** | Se pasan objetos: inmutables → como por valor; mutables (listas) → se trabaja sobre el mismo objeto |
| **Ada** | `in` (defecto), `out`, `in out` (primitivos por valor-resultado; compuestos por referencia). En **funciones** solo `in` → evita efectos colaterales |

## 5. Subprogramas como parámetros

Útil, por ejemplo, para pasar el criterio de ordenamiento o simular un manejador de excepciones. C lo permite (punteros a función), Pascal también; Ada no (usa unidades genéricas).

**Problema**: ¿cuál es el ambiente no local del subprograma pasado?
- **Ligadura shallow (superficial)**: ambiente de la unidad que tiene el **parámetro formal** (quien lo ejecuta). Ej.: SNOBOL. No apto para alcance estático.
- **Ligadura deep (profunda)**: ambiente de la unidad donde está **declarado** el subprograma. La natural con alcance estático.
- **Ad hoc**: ambiente de la unidad donde está la **llamada** que lo pasa como parámetro. Nunca se usó.

## 6. Unidades genéricas

Unidad que se **instancia con distintos tipos** (ej.: `generic package Conjuntos` → `new Conjuntos(Integer)`, `new Conjuntos(Float)`). Proveen la funcionalidad del parámetro subprograma.

---

## Preguntas de final relacionadas

- **5a** — Tipos de pasaje: punto 3. **5b** — Más seguro e inseguro: fin del punto 3.
- **22** — Por valor y por resultado, ventajas y desventajas.
- **31, 51c, 52** — Referencia vs. nombre, con ejemplo: ejemplo de `a[m]`.
- **51a, 51b** — Ambiente común vs. parámetros: punto 2.
