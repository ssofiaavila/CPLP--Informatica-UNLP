# Clase 10 — Excepciones

## 1. Qué es una excepción

**Condición inesperada o inusual** que ocurre **durante la ejecución** y **no puede manejarse en el contexto local**. Ej.: división por cero, índice fuera de rango, desbordamiento de pila, acceso a memoria no permitida.
Interrumpe el flujo normal y ejecuta un **controlador (manejador)** registrado previamente.
Caso Ariane 5: explotó a los 37 s por una excepción de overflow no manejada (software heredado del Ariane 4).

Pueden atenderse con mecanismos del **lenguaje**, del **hardware** (interrupciones) o del **sistema operativo** (señales).

**Manejador**: sección de código que maneja la excepción para que el programa se recupere en vez de abortar. Puede: imprimir un error, corregir, lanzar otra excepción o finalizar el programa.

**Tipos**: **implícitas** (definidas por el lenguaje, built-in) y **explícitas** (definidas por el programador).

## 2. Qué debe proveer un lenguaje / qué analizar

Mínimo que debe proveer:
1. Un modo de **definirlas**.
2. Una forma de **reconocerlas**.
3. Una forma de **lanzarlas y capturarlas**.
4. Una forma de **manejarlas** (código del manejador).
5. Un **criterio de continuación**.

Preguntas para analizar cualquier lenguaje:
- ¿Qué excepciones se pueden manejar y cómo se definen? ¿Hay predefinidas?
- ¿Cómo se lanza una excepción?
- ¿Cómo se especifican los manejadores y cuál es su **ámbito**?
- ¿Cómo se **liga** la excepción con el manejador?
- ¿A dónde se cede el control al terminar el manejador?
- ¿Cómo se **propagan**?
- ¿Qué pasa si no hay manejador?

**Punto de retorno** posible: continuar la ejecución normal, volver a un estado anterior, propagar la excepción o terminar el programa.

## 3. Dos modelos

| | **Reasunción** | **Terminación** |
|---|---|---|
| Qué hace | El manejador corrige y la ejecución **continúa desde donde se produjo** la excepción (sentencia siguiente) | El manejador se ejecuta pero **no se vuelve** al punto de la excepción: **termina el bloque/unidad** que la levantó y se continúa después del manejador |
| Lenguajes | **PL/1** | **Ada, CLU, C++, Java, Python, PHP** (el más usado) |

## 4. Lenguajes

### PL/1 — reasunción
- Primer lenguaje con excepciones. Se llaman **CONDITIONS**.
- Declarar manejador: `ON CONDITION(nombre) manejador` (instrucción o bloque `begin … end`).
- Lanzar: `SIGNAL CONDITION(nombre)`.
- Predefinidas (built-in) con manejador por defecto, ej.: `ZERODIVIDE`.
- **Ligadura dinámica**: la excepción se liga al **último manejador definido** (pila de manejadores). El alcance del manejador termina cuando termina la unidad donde se declaró.

### Ada — terminación
- Declarar: `MiExcepcion : exception;` (en la zona de declaraciones, mismo alcance que las variables).
- Lanzar: `raise MiExcepcion;`
- Manejadores: al final de la unidad (bloque, subprograma, paquete, tarea), tras la palabra `exception`, con `when nombre => …`. `when others` captura el resto (va al final; ojo, entran todas).
- Predefinidas: `Constraint_Error`, `Program_Error`, `Storage_Error`, `Tasking_Error`, `Name_Error`.
- **Propagación dinámica**: si la unidad tiene manejador, se ejecuta y la unidad termina. Si no, la unidad termina y la excepción **se vuelve a levantar en quien la llamó**, y así siguiendo. Si sale del alcance de su nombre queda **anónima** y solo la atrapa `when others`.
- `raise;` solo, dentro de un manejador: **relanza** la misma excepción hacia arriba.

### CLU — terminación
- Solo las lanzan los **procedimientos**, que deben declararlas en el encabezado: `procedure P() signals e1, e2`.
- Lanzar: `signal e1`. Relanzar (una vez): `resignal`.
- Manejadores **pegados a una sentencia**: `<sentencia> except when e1: … when others: … end;`
- Un procedimiento **no puede manejar** una excepción que él mismo genera: termina y la maneja **el llamador**.
- **Propagación**: termina el procedimiento → vuelve al llamante inmediato → busca manejador en la sentencia de la llamada; si no hay, busca **estáticamente** en las sentencias que la incluyen. Si no encuentra en ese procedimiento, se levanta `failure` y termina el programa.
- Admite parámetros en los manejadores.

### C++ — terminación
- `try { … }` delimita el código que puede fallar; `catch (Tipo e) { … }` son los manejadores; `throw` lanza (puede llevar parámetros, cualquier tipo).
- Si ningún `catch` coincide, se **propaga** a los `try` externos y luego dinámicamente a los llamadores. Si nadie la maneja → `terminate()`.
- Lista de excepciones en la interfaz: `void rutina() throw (Ayuda, Zerodivide);`
  - Lanza una que no está en la lista → no se propaga, se llama a `unexpected()` (→ `abort()`).
  - Sin lista `throw` → puede propagar **cualquiera**. `throw()` vacío → **ninguna**.

### Java — terminación
- Las excepciones son **objetos**, instancias de `Throwable` (subclases `Error` y `Exception`).
- Palabras clave: `try`, `catch`, `throw`, `throws`, `finally`.
- `throws` en el encabezado declara las excepciones que el método puede lanzar.
- `finally`: se ejecuta **siempre**, haya o no excepción.
- Los `catch` van de lo más específico a lo más general.

### Python — terminación
- `try: … except Tipo: …` (también `else` y `finally`). Lanzar: `raise`.
- Si ocurre una excepción se saltea el resto del `try`; si coincide con un `except` se ejecuta y se continúa después del `try`.
- **Propagación**: primero busca **estáticamente** (¿el `try` está dentro de otro `try`?); si no, **dinámicamente** (quién lo llamó). Si no hay manejador, corta con el mensaje de error estándar.

### PHP — terminación
- `try`, `catch`, `finally`, `throw`. Cada `try` necesita al menos un `catch`.
- El objeto lanzado debe ser instancia de `Exception` (o subclase).
- Sin manejador → Fatal Error "Uncaught Exception", salvo que se defina `set_exception_handler()`.

## 5. Simular reasunción con terminación

- **Ada**: encerrar la instrucción que puede fallar en un **bloque interno** (`declare … begin … exception when … end;`). El manejador se ejecuta, termina solo ese bloque interno, y el programa **sigue con las instrucciones que están debajo**.
- **Python**: lo mismo con un `try/except` que envuelva **solo** la instrucción riesgosa; o bien un `while True:` con `try … break / except` para reintentar.

Lenguajes sin excepciones: se simulan (códigos de retorno, rutina manejadora pasada como parámetro).

---

## Preguntas de final relacionadas

- **4, 55** — Dos lenguajes con manejo distinto: **PL/1** (reasunción, ligadura dinámica al último manejador) vs. **Ada** (terminación, propagación dinámica); o **Ada** (propaga dinámicamente) vs. **CLU** (el que lanza no maneja; busca estáticamente y luego `failure`).
- **8a** — Simular reasunción en Ada y Python: punto 5.
- **8b** — Propagación en Ada (dinámica) y Python (estática y luego dinámica).
- **23** — Aspectos a considerar y excepciones por terminación: puntos 2 y 3.
- **37, 53** — Qué analizar / qué debe tener un lenguaje nuevo: punto 2, ejemplificado con un lenguaje del punto 4.
