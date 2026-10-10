# Clase 11 — Paradigmas de programación

**Paradigma**: estilo de desarrollo de programas, un modelo para resolver problemas. Un lenguaje puede encuadrarse en uno o varios.

| Paradigma | Un programa es… |
|---|---|
| **Imperativo** | Sentencias + secuencias de comandos que modifican el estado (variables = celdas de memoria). Dice *cómo*. |
| **Declarativo** | Descripción del resultado esperado, sin listar los pasos. Dice *qué*. Incluye lógico y funcional. |
| **Lógico** | Aserciones lógicas: **hechos + reglas**. |
| **Funcional** | Composición de **funciones**. |
| **Orientado a objetos** | **Objetos** que interactúan con **mensajes**. |

Más recientes: **dirigido por eventos** (el flujo lo determinan sucesos externos) y **orientado a aspectos** (módulos independientes con responsabilidad bien definida).

## 1. Paradigma lógico (Prolog)

Programa = serie de aserciones lógicas; el conocimiento se representa con **hechos y reglas**.

**Elementos** (la unidad básica es el *término*):
- **Variables**: elementos indeterminados; empiezan con **mayúscula** (`X`, `Longitud`).
- **Constantes**: elementos determinados; en **minúscula** o números (`juan`, `padre`).
- **Término compuesto**: *functor* + argumentos; **aridad** = cantidad de argumentos. *Estructura*: término compuesto sin variables (`tamaño(4,5)`).
- **Listas**: `[]` vacía; `[X|Y]` = cabeza `X` y cola `Y`.

**Cláusulas de Horn**:
- **Hecho**: verdad/relación entre objetos. `tiene(coche, ruedas).`
- **Regla**: `conclusión :- condición.` (`:-` = "si"; las comas son AND).
- **Query**: lo que se quiere averiguar. `?- longitud([rojo,verde,azul], X).`

```prolog
longitud([], 0).
longitud([X|Y], N) :- longitud(Y, M), N = M + 1.
```

**Ejecución**: el programa especifica *qué se conoce*; la consulta es el objetivo. El motor intenta **deducir** la respuesta a partir de hechos y reglas (responde YES si se puede derivar; prueba alternativas cuando una falla).

## 2. Paradigma orientado a objetos

*"Un programa es un conjunto de objetos que interactúan mandándose mensajes."*

- **Objeto**: entidad con **estado interno** y **comportamiento** (equivale a un dato abstracto).
- **Mensaje**: petición de un objeto a otro para que ejecute un método. Todo el procesamiento se activa por mensajes.
- **Método**: programa asociado a un objeto, que se ejecuta al recibir un mensaje.
- **Clase**: tipo definido por el usuario (estructura + operaciones). Primer nivel de abstracción: estructura, comportamiento y **ocultamiento**.
- **Instancia**: objeto concreto de una clase.
- **Herencia**: segundo nivel de abstracción; jerarquías de sub y superclases (generalización/especialización).
- **Polimorfismo**: objetos de distintas clases responden al mismo mensaje (`3+5` suma, `"a"+"b"` concatena).
- **Binding dinámico**: el mensaje se vincula con el método en ejecución.

C++ es **híbrido**: extiende C con clases, ocultamiento, herencia y mensajes.

**Simular POO en un lenguaje imperativo**: se puede simular el **encapsulamiento** (registros + rutinas, módulos, TAD) y parcialmente el ocultamiento. **No** se puede simular naturalmente la **herencia** ni el **polimorfismo con binding dinámico**, porque requieren soporte del lenguaje para jerarquías de tipos y resolución del método en ejecución.

## 3. Lenguajes basados en script

- Los lenguajes **convencionales** construyen aplicaciones autocontenidas; priorizan eficiencia, mantenibilidad, portabilidad y detección estática de errores.
- Los **script** nacieron como archivos de comandos interpretados; luego sumaron variables y control (Perl, Python, PHP, JavaScript). Priorizan **flexibilidad, desarrollo rápido y chequeo dinámico**.
- Idea (Ousterhout): no escribir aplicaciones desde cero sino **combinar componentes** existentes.

**Objetivos / características**:
- **Pegar programas** (*glue languages*) y extender herramientas.
- Desarrollo y evolución rápida; sintaxis concisa.
- Editores livianos.
- **Interpretados** (la eficiencia no es crítica).
- Alto nivel para **strings** (expresiones regulares), reportes y GUI.
- **Tipado dinámico**; pocas o ninguna declaración; reglas de alcance simples (Perl: global por defecto; PHP y Tcl: local por defecto; Python: local donde se asigna).
- Estructuras de alto nivel: listas, tuplas, conjuntos, diccionarios; garbage collection.

**Dominios**: shell, procesamiento de texto, matemática/estadística, glue y propósito general, extensión, WWW (CGI, scripts en servidor y en cliente).

- **JavaScript**: nació para Netscape; estándar ECMA-262. Sintaxis tipo Java pero tipado dinámico; OO con herencia por prototipos; manipula el HTML.
- **Python**: libre, interpretado, multiplataforma, **multiparadigma**, **fuerte y dinámicamente tipado**; las variables no se declaran.

## 4. Paradigma funcional (aplicativo)

Basado en **funciones**. Usado en IA, matemática, lógica, paralelismo.
- ✔ Vista uniforme de programa y función, funciones como datos, **sin efectos colaterales**, manejo automático de memoria.
- ✘ Ineficiencia de ejecución.

**Características**: funciones primitivas, formas funcionales, **semántica basada en valores**, **transparencia referencial**, composición como regla de combinación, funciones de primer orden.

- **Función**: correspondencia `f: A → B`. Son **valores**: se pasan como parámetro y se devuelven.
- Valor vs. definición: `doble x = x + x` y `doble' x = 2 * x` son la misma función.
- **Script**: lista de definiciones que se someten a evaluación (`?cuadrado (3+4)` → 49).
- **Expresión**: noción central; *una expresión es su valor*, que depende solo de sus subexpresiones.
- **Variable**: es la variable **matemática**, no una celda de memoria.
- **Transparencia referencial**: dos expresiones iguales dan siempre el mismo valor → no hay efectos laterales.
- **Evaluación por reducción**: se simplifica hasta la forma canónica; el resultado no depende del orden. Lo que no se puede reducir (1/0) vale **bottom** (indefinido).
  - **Orden aplicativo**: evalúa siempre los argumentos primero.
  - **Orden normal (lazy)**: no evalúa hasta que se necesita, ni más de una vez.
- **Tipos**: básicos (`num`, `bool`, `char`) y derivados (pares, funciones). Toda función tiene tipo, declarado o **inferido**. **Tipos polimórficos**: `id x = x` tiene tipo `β → β`.
- **Currificación**: reemplaza argumentos estructurados por simples. `Suma(x,y)` es `(num,num) → num`; `Suma' x y` es `num → (num → num)`.
- **Cálculo lambda**: modelo de cómputo para definir funciones, independiente de la sintaxis. Expresiones: identificador/constante, definición (`λx. x+1`), aplicación (`(e1 e2)`).

## 5. Comparaciones (para el final)

| | Imperativo | Funcional | Lógico |
|---|---|---|---|
| Programa | Secuencia de sentencias | Definiciones de funciones | Hechos y reglas |
| Enfoque | *Cómo* (pasos) | *Qué* (expresiones) | *Qué* (relaciones) |
| Variable | Celda de memoria modificable | Valor matemático, inmutable | Incógnita que se liga por unificación |
| Ejecución | Cambios de estado (asignación) | Reducción de expresiones | Deducción / búsqueda |
| Control | Explícito: secuencia, selección, iteración | Composición y recursión | Implícito, lo maneja el motor |
| Efectos colaterales | Sí | No (transparencia referencial) | No |
| Resultado | Estado final de la memoria | Un valor | Sí/No y valores de las variables |

---

## Preguntas de final relacionadas

- **38** — Lógico vs. imperativo (3 diferencias): tabla del punto 5.
- **39** — Funcional vs. imperativo.
- **45** — Lógico vs. funcional (3 diferencias): funciones vs. relaciones; reducción vs. deducción; una función da un único resultado y tiene dirección entrada→salida, un predicado puede usarse en varias direcciones y dar varias respuestas.
- **50** — Filosofía de la POO y qué puede simularse en un imperativo: punto 2.
