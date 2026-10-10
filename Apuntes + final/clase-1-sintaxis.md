# Clase 1 — Introducción, criterios de evaluación y Sintaxis

## 1. Para qué estudiar conceptos de lenguajes

- Producir mejor software, usar mejor un lenguaje, elegirlo mejor, aprender otros más rápido y poder diseñar/implementar lenguajes.
- Idea central: *el valor de un lenguaje se juzga por cómo afecta la producción de software*.

## 2. Criterios para evaluar un lenguaje

| Criterio | Qué significa |
|---|---|
| **Simplicidad y legibilidad** | Programas fáciles de leer, escribir, aprender. Atentan: demasiados componentes, un mismo concepto con varias sintaxis, una misma notación para conceptos distintos, abuso de sobrecarga. |
| **Claridad en los bindings** | Debe estar claro *cuándo* se liga cada atributo (definición del lenguaje, implementación, escritura, compilación, carga, ejecución). |
| **Confiabilidad** | Seguridad: chequeo de tipos (detectar errores temprano es más barato) y manejo de excepciones. |
| **Soporte** | Compilador/intérprete accesible, multiplataforma, documentación, cursos. |
| **Abstracción** | Definir y usar estructuras u operaciones ignorando detalles (de procesos y de datos). |
| **Ortogonalidad** | Pocas primitivas que se combinan libremente; toda combinación es legal y tiene sentido. |
| **Eficiencia** | Tiempo y espacio, esfuerzo humano, que sea optimizable. |

## 3. Sintaxis vs. semántica

- **Sintaxis**: reglas que dicen *cómo se escribe* un programa (cómo combinar caracteres para formar programas válidos).
- **Semántica**: reglas que dan *significado* a los programas sintácticamente válidos.
- Ejemplo: `v: array[1..10] of integer` (Pascal) e `int v[10]` (C): distinta sintaxis, misma semántica.
- Sirven a programadores e implementadores para saber si un programa es válido y qué significa.

## 4. Características que debe tener la sintaxis

**Legibilidad, verificabilidad, facilidad de traducción y falta de ambigüedad.**
(Justificación: ayuda a escribir programas correctos, permite chequearlos, que el compilador los traduzca rápido y que cada construcción tenga una sola interpretación.)

## 5. Elementos de la sintaxis

- **Alfabeto / conjunto de caracteres** (ASCII, UTF-8…): importa por el orden en las comparaciones.
- **Identificadores**: letras y dígitos empezando con letra. Limitar la longitud quita legibilidad.
- **Operadores**: `+ -` son uniformes; otros varían (`**` vs `^`).
- **Comentarios**: hacen más legible el código.
- **Palabra clave**: tiene significado en un contexto.
- **Palabra reservada**: palabra clave que además *no puede usarse como identificador*. Ventaja: legibilidad y traducción rápida.

## 6. Estructura sintáctica

- **Vocabulario o words**: identificadores, operadores, palabras clave… se arman a partir del alfabeto.
- **Expresiones**: funciones que a partir de datos devuelven un resultado.
- **Sentencias**: componente más importante; simples, estructuradas y anidadas.

## 7. Reglas léxicas y sintácticas

- **Reglas léxicas**: cómo formar las *words* a partir de los caracteres. Ej.: mayúsculas/minúsculas, distinto `!=` (C) vs `<>` (Pascal).
- **Reglas sintácticas**: cómo formar *expresiones y sentencias* con las words. Ej.: el `if` de C no lleva `then`, el de Pascal sí.

## 8. Tipos de sintaxis

- **Abstracta**: la *estructura*. `while (x!=y){…}` de C y `while x<>y do begin…end` de Pascal son iguales: *while condición bloque*.
- **Concreta**: la parte *léxica*. Esos dos `while` son distintos (`!=`/`<>`, `{}`/`begin end`).
- **Pragmática**: el *uso práctico*. Ej.: `<>` es más legible que `!=`; poder omitir `{}` con una sola sentencia lleva a errores cuando se agrega otra.

## 9. Cómo se define la sintaxis

Se necesita una descripción **finita** de un conjunto **infinito** de programas. Formas: lenguaje natural (Fortran), **BNF** (Algol), **EBNF**, **diagramas sintácticos**.

### Gramática
4-tupla **G = (N, T, S, P)**: no terminales, terminales, símbolo distinguido (∈ N) y producciones.

### BNF (Backus-Naur Form)
- Notación formal, **metalenguaje**, gramática **libre de contexto**.
- Metasímbolos: `< >` (no terminal), `::=` (se define como), `|` (o).
- Ej.: `<digito> ::= 0|1|2|...|9`
- La repetición se expresa con **recursión**: `<natural> ::= <digito> | <digito><natural>`

### EBNF (BNF extendida)
Agrega: `[ ]` opcional, `(|)` elegir una alternativa, `{ }` repetición (`*` cero o más, `+` una o más).

```
BNF:   <enterosig> ::= +<entero> | -<entero> | <entero>
       <entero>    ::= <digito> | <entero><digito>
EBNF:  <enterosig> ::= [(+|-)] <digito>{<digito>}*
```
**Diferencia**: EBNF elimina la recursión y es más corta y legible; expresan lo mismo.

### Diagramas sintácticos (Conway)
Grafo con una entrada y una salida por regla. Rectángulos = no terminales, óvalos = terminales. Una sentencia es válida si existe un camino. Equivalen a BNF pero son más intuitivos.

## 10. Otros conceptos

- **Parsing**: método que determina si un string pertenece al lenguaje; construye el **árbol sintáctico o de derivación**. Métodos **top-down** y **bottom-up** (de izq. a der. o de der. a izq.). Los compiladores usan bottom-up de izquierda a derecha.
- **Producciones recursivas**: hacen infinito el lenguaje. Recursión a izquierda → asociatividad izquierda; a derecha → asociatividad derecha.
- **Gramática ambigua**: una sentencia se puede derivar de más de una forma (más de un árbol). Ej.: `<exp> ::= <exp>+<exp> | <exp>*<exp> | <id>`.
- **Subgramáticas**: una gramática se define usando otras (expresiones usa números e identificadores). Así trabajan los compiladores.
- **Libre de contexto vs. sensible al contexto**: BNF/EBNF no miran el contexto, por eso aceptan `int e; a := b + c;` aunque `a` no esté declarada o los tipos no coincidan. Eso lo resuelve la semántica estática (Clase 2).

---

## Preguntas de final relacionadas

- **3, 9b** — Sintaxis y semántica estática: qué analiza cada una y cómo se expresan (sintaxis → BNF/EBNF/diagramas; semántica estática → gramática de atributos, Clase 2).
- **6, 35** — Dos características de la sintaxis: ver punto 4.
- **34** — Sintaxis abstracta, concreta y pragmática: punto 8.
- **40** — Qué analizan las reglas léxicas: punto 7.
- **46a** — BNF y EBNF, diferencias y ejemplo: punto 9.
- **46b** — BNF vs. gramática de atributos: BNF es libre de contexto (solo forma); la gramática de atributos es sensible al contexto, agrega atributos y reglas semánticas (Clase 2).
