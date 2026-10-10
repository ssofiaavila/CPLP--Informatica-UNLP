# Clase 2 — Semántica y procesamiento de programas

## 1. Semántica

Describe el **significado** de los símbolos, palabras y frases de un lenguaje sintácticamente válido.
BNF/EBNF no alcanzan: hay errores que la sintaxis no puede detectar. Dos tipos:

| | Semántica **estática** | Semántica **dinámica** |
|---|---|---|
| Cuándo | Antes de ejecutar (compilación) | Durante la ejecución |
| Qué mira | Las **formas válidas**, no el significado de ejecutar | El **efecto de ejecutar** cada construcción |
| Ejemplos | Tipos incompatibles, variable declarada dos veces, variable no declarada | División por cero, índice fuera de rango en `int notas[10]` |
| Cómo se describe | **Gramática de atributos** (formal) | Axiomática, denotacional (formales) y operacional (no formal) |

Un programa solo se ejecuta si es correcto en sintaxis **y** en semántica estática.

## 2. Semántica estática: gramática de atributos (Knuth, 1968)

- Gramática **sensible al contexto**; la usan los compiladores.
- A cada símbolo (terminal o no terminal) se le asocian **atributos** (tipo, valor, lugar en memoria…).
- A cada producción se le asocian **ecuaciones o reglas semánticas** que calculan esos atributos.
- Se expresa en forma tabular:

| Regla gramatical | Reglas semánticas |
|---|---|
| `decl → tipo lista-var` | `lista-var.at = tipo.at` |
| `tipo → int` | `tipo.at = int` |
| `lista-var → id` | `id.at = lista-var.at` ; `Añadetipo(id.entrada, lista-var.at)` |

- Al evaluar las ecuaciones (árbol sintáctico **atribuido**): se cargan símbolos en la **tabla de símbolos**, se detectan duplicados, problemas de tipos y combinaciones no permitidas, y se genera código para el paso siguiente.

## 3. Semántica dinámica

Difícil de describir: no hay herramienta estándar como BNF.

- **Axiomática** (formal): el programa es una *máquina de estados*; cada instrucción cambia el estado. El estado se describe con **predicados** (cálculo de predicados). Cada sentencia lleva **precondición** y **poscondición**. Sirve para probar la corrección.
  Ej.: `{b ≠ 0}  c = a/b  {a = b*c + r y r < b}`
- **Denotacional** (formal): se basa en **funciones recursivas** y modelos matemáticos. Asigna a cada construcción sintáctica una función que da su significado.
  Ej.: `FNbin(<Nbin>0) = 2*FNbin(<Nbin>)`, `FNbin(<Nbin>1) = 2*FNbin(<Nbin>)+1` → `110` = 6.
- **Operacional** (no formal): el significado se describe con otro **lenguaje de bajo nivel sobre una máquina abstracta**; los cambios de estado de esa máquina definen el significado. Es la más usada en libros (PL/1 fue el primero). Es la que usa la materia (SIMPLESEM, Clase 4).
  Ej.: un `for` de Pascal se traduce a `i := pri; lazo: if i > ul goto sal; …; i := i+1; goto lazo`.

Diferencia axiomática/denotacional: una describe estados con **predicados**, la otra con **funciones**.

## 4. Procesamiento de un programa: traducción

Las máquinas ejecutan lenguaje de máquina (0 y 1). Primero se pasó a **ensamblador** (códigos mnemotécnicos), pero era dependiente de cada procesador. Los **lenguajes de alto nivel** dieron la abstracción; necesitan un traductor. La elección la hace quien diseña el lenguaje, no el programador.

### Intérprete
Traduce **en ejecución**, sentencia por sentencia: lee, analiza, decodifica y ejecuta. Solo pasa por lo que se ejecuta. Ej.: Lisp, Smalltalk, Basic, Python, Ruby, PHP, Perl.

### Compilador
Traduce **todo antes de ejecutar**: lenguaje fuente → lenguaje objeto (ejecutable o intermedio). El resultado se guarda y reutiliza. Ej.: C, C++, Ada, Pascal, Fortran.

### Comparación

| Aspecto | Intérprete | Compilador |
|---|---|---|
| Cómo ejecuta | Línea a línea; hace falta el intérprete y el fuente | Genera programa objeto; el fuente no hace falta |
| Orden | **Lógico** de ejecución | **Físico** de las sentencias (recorre todo) |
| Tiempo | Decodifica cada vez (en un loop, muchas veces) → más lento | Traduce una sola vez → ejecución más rápida |
| Espacio | Ocupa menos; tablas se crean al usarse | Ocupa más (una sentencia = muchas de máquina) |
| Errores | Fáciles de ubicar y corregir (relación directa con el fuente) | Se pierde la referencia fuente/objeto |

### Combinaciones
1. **Interpretar y luego compilar**: intérprete en desarrollo (diagnóstico), compilador para la versión final (eficiencia).
2. **Compilar y luego interpretar**: se compila a **código intermedio portable** que luego se interpreta. Ej.: Java (bytecode, ejecutado por la máquina virtual), C#, VB.NET, Python.

## 5. Cómo funciona un compilador

**Etapa de análisis** (ligada al código fuente)
1. **Análisis léxico (Scanner)**: nivel *palabra*. Divide en **tokens** (identificadores, palabras reservadas, números, símbolos), descarta comentarios y blancos, carga identificadores en la tabla de símbolos, da error si algo no es una categoría léxica válida. Es el que más tiempo lleva.
2. **Análisis sintáctico (Parser)**: nivel *sentencia*. Con los tokens y la gramática arma el **árbol sintáctico** y verifica la estructura.
3. **Análisis semántico (semántica estática)**: fase medular. Chequeo de tipos (gramática de atributos), duplicados, nombres declarados; agrega descriptores de tipo a la tabla de símbolos. Nexo entre análisis y síntesis.

**Generación de código intermedio** (opcional): representación independiente de la máquina, fácil de producir y de traducir. La más común es el **código de tres direcciones** (`x := y op z`).

**Etapa de síntesis** (ligada a la máquina)
- **Optimización** (optativa): menos saltos, código muerto, velocidad vs. tamaño.
- **Generación de código objeto**.
- **Linkeditor**: enlaza los módulos compilados por separado → módulo de carga.
- **Loader**: carga el programa en memoria.

Orden completo: fuente → léxico → sintáctico → semántico → código intermedio → optimizador → código objeto → linkeditor → loader.

---

## Preguntas de final relacionadas

- **3** — Sintaxis y semántica estática: qué analiza cada una y cómo expresarlas (BNF/EBNF vs. gramática de atributos).
- **9a, 25** — Formas de definir la semántica: gramática de atributos (estática); axiomática, denotacional y operacional (dinámica).
- **9b, 26, 33** — Estática vs. dinámica con ejemplos: tabla del punto 1.
- **46b** — BNF vs. gramática de atributos: punto 2.
