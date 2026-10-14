# Clase 8 — Estructuras de control

Son el medio para determinar el **flujo de ejecución**.
- **A nivel de unidad**: entre rutinas (parámetros, call-return, excepciones).
- **A nivel de sentencia**: **secuencia, selección, iteración**.

## 1. Secuencia

Una sentencia a continuación de otra. Delimitador más usado: `;`.
- Lenguajes **orientados a línea** (sin delimitador, una instrucción por línea): Fortran, Basic, Ruby, Python.
- **Sentencias compuestas**: agrupan varias en una. `begin … end` (Pascal, Ada), `{ }` (C, C++, Java).

## 2. Asignación

Sentencia que modifica la memoria: asigna al **l-valor** de la izquierda el **r-valor** de la expresión de la derecha.
Sintaxis: `A := B` (Pascal, Ada), `A = B` (C, Fortran, Python), `MOVE B TO A` (COBOL), `A ← B` (APL), `(SETQ A B)` (Lisp).

### La asignación en C (diferencia con otros lenguajes)
En la mayoría de los lenguajes la asignación es una **sentencia**, distinta de una expresión. En **C es una expresión con efecto colateral**: además de modificar la memoria, **devuelve el valor asignado**. Consecuencias:
- **Asignación múltiple**, evaluada de derecha a izquierda: `a = b = c = 0;`
- Se puede usar donde va una expresión: `if (i = 30) …` es válido (asigna 30, y como es ≠ 0 da verdadero). Fuente típica de errores al confundir `=` con `==`.
- A la izquierda debe haber un **l-valor**: `++p = *q;` y `(i<j ? z : y) = 4;` dan error porque devuelven un r-valor.
- Operadores combinados: `+=`, `-=`, `++`.

## 3. Selección: `if`

Evolución:
- **Fortran**: `if (condición) sentencia` (if lógico, un solo camino).
- **Algol**: `if … then … else` (dos caminos). Problema: **ambigüedad** con if anidados (*else colgante*):
  `if x>0 then if x<10 then x:=0 else x:=1000` → ¿a cuál `if` pertenece el `else`?
- **PL/1, Pascal, C**: regla → cada `else` se empareja con el **`if` abierto más cercano**. Sin ambigüedad, pero puede ser poco legible.
- **Sentencia de cierre**: `fi` (Algol 68), `end if` (Ada), `end` (Modula-2). Más claro.
- **C**: condición entre `( )`, sin `then`, bloques con `{ }` (se recomienda usarlas siempre).
- **Python**: `:` obligatorio, **indentación obligatoria**, `elif`. Sin ambigüedad y legible.

**Condicional corto (operador ternario)**:
- C: `cond ? b : c`
- Python: `A if C else B`

### Circuito corto (short circuit)
Las condiciones se evalúan de izquierda a derecha y **se corta apenas se conoce el resultado**:
- `a and b`: si `a` es falso, no se evalúa `b`.
- `a or b`: si `a` es verdadero, no se evalúa `b`.
- Ej.: `a and false and f()` → nunca llama a `f()`.
- Utilidad: evita errores como `if (p != null and p.valor > 0)`.
- **Circuito largo**: evalúa siempre todos los operandos.

## 4. Selección múltiple

Reemplaza cadenas de `if … else if …`.

| Lenguaje | Construcción | Detalles |
|---|---|---|
| **Pascal** | `case var of … else … end` | Variable ordinal; `else` opcional. **Inseguro**: no define qué pasa si el valor no está en ningún caso. |
| **Ada** | `case expr is when … => … end case` | Entero o enumerativo. Deben cubrirse **todos** los valores o usar `when others` (último); si no, **no compila**. Permite rangos (`..`) y varias opciones (`\|`). |
| **C / C++** | `switch (expr) { case …: … break; default: … }` | Etiquetas constantes. Sin `break` **continúa en las ramas siguientes**. `default` opcional. |
| **Ruby** | `case expr when … else … end` | Cualquier valor. `else` opcional: si no entra en ninguna, la variable queda sin valor. |
| **PL/1** | `SELECT … WHEN … OTHERWISE … END` | Dos formatos: con identificador o con condiciones. |

## 5. Iteración

### Tipo `for` (cantidad de repeticiones conocida)
- **Fortran** `DO label var = ini, fin`: variable entera. El original chequeaba al final (ejecutaba al menos una vez); desde Fortran 77 se calcula antes de entrar. No modificar la variable de control.
- **Pascal** `for i := a to b do`: cualquier tipo ordinal. **No permite modificar** la variable de control ni los límites dentro del bucle; al salir, su valor es indefinido.
- **Ada** `for i in 1..N loop … end loop`: la variable **se declara implícitamente** y desaparece al salir; `in reverse` para decrementar; `exit` para salir.
- **C / C++** `for (inicialización; condición; incremento)`: en C++ se puede declarar la variable dentro. `for (;;)` es un bucle infinito.
- **Python**: `for elem in iterable:` (listas, tuplas, diccionarios…) y `for i in range(n):` (`range(5)` → 0..4; `range(2,5)`; `range(2,5,2)`).

### Tipo `while` (chequeo al inicio)
Repite **mientras** se cumpla la condición; puede no ejecutarse nunca.
`while cond do` (Pascal), `while (cond) {}` (C), `while cond loop … end loop` (Ada), `while cond:` (Python).

### Chequeo al final
El cuerpo se ejecuta **al menos una vez**.
- Pascal: `repeat … until cond` (hasta que sea verdadera).
- C: `do … while (cond);` (mientras sea verdadera).

### `loop` de Ada
`loop … exit when condición; … end loop` — bucle general del que se sale con `exit`; se combina con `for` y `while`.

---

## Preguntas de final relacionadas

- **7a, 48** — Sentencia de asignación en C y diferencias con otros lenguajes: punto 2.
- **24** — Qué es short circuit: punto 3.
