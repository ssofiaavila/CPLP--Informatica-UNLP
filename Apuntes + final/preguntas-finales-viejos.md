# Conceptos y paradigmas de lenguajes de programación | Preguntas finales viejos

> ⚠️ = contenido que no está en las diapositivas de la cátedra (sale de la bibliografía); conviene verificarlo contra la clase.

## 1. Estática, global, constante, puntero. Relacione estos conceptos con los "atributos" de la entidad variable. Realice una explicación completa, justificando cada relación y dando ejemplos.

**Respuesta**

Una variable tiene los atributos **\<nombre, alcance, tipo, l-valor, r-valor\>**. Cada uno de estos cuatro conceptos describe cómo se liga **un atributo distinto**:

- **Estática → l-valor (tiempo de vida).** Su memoria se aloca **antes de la ejecución** y queda ligada durante **todo el programa**. Por eso es **sensible a la historia**: conserva su valor entre una activación y otra de la unidad.
  ```c
  void cuenta() { static int c = 0; c++; printf("%d", c); }  // imprime 1, 2, 3...
  ```
- **Global → alcance.** Se declara en el programa principal y su nombre es **conocido en todas las unidades** (salvo que una unidad lo redeclare y lo oculte). Habla de *dónde se ve* el nombre, no de cuánto vive.
  ```c
  int total = 0;            // global
  void sumar(int x) { total += x; }
  ```
- **Constante → r-valor.** El valor queda **congelado**: la ligadura del r-valor no puede cambiar. En Pascal se liga en compilación (`const MAX = 10;`); en Ada puede ligarse en ejecución pero es estable (`MAX : constant Integer := N;`).
- **Puntero → tipo y r-valor.** Es una variable de **tipo puntero**, cuyo **r-valor es una referencia (el l-valor) de otro objeto**, en general una variable anónima creada con `new`/`malloc` en la heap. El puntero en sí suele ser automático (vive en la pila); lo apuntado es dinámico explícito.
  ```c
  int *p = malloc(sizeof(int));  // r-valor de p = dirección de una variable anónima
  ```

**Justificación de que son independientes:** como afectan atributos distintos, se pueden combinar. Una variable estática puede ser **local** (`static` dentro de una función: vive todo el programa pero solo se ve en la función); un puntero puede ser constante (`int * const p = &x;`); una global suele ser estática, pero no es obligatorio.

---

## 2. Tipos
- a. Sistema de tipos y compatibilidad de tipos. Defina estos dos conceptos en forma completa e indique qué relación hay entre ellos.
- b. Lenguaje de tipado estático, lenguaje fuertemente tipado. Indique si estos dos conceptos son iguales o indican diferentes cosas. Justifique la respuesta en forma completa. Ejemplifique.

**Respuesta**

**a. Sistema de tipos y compatibilidad**

- **Sistema de tipos**: conjunto de **reglas** que usa un lenguaje para **estructurar y organizar sus tipos**. Su objetivo es permitir escribir **programas seguros**, buscando un equilibrio entre seguridad y flexibilidad. Provee:
  - mecanismos para **definir tipos** y **asociarlos** a las construcciones del lenguaje;
  - reglas de **equivalencia**, **compatibilidad** e **inferencia**.
  
  Se especifica por: el tipo y el momento de chequeo (estático/dinámico, fuerte/débil), las reglas de equivalencia y conversión, las reglas de inferencia y el nivel de polimorfismo.
- **Compatibilidad de tipos**: reglas que determinan si el tipo de un objeto es **válido en un contexto** (una asignación, un parámetro, un operando). Un tipo es compatible con otro si es **equivalente** o si **se puede convertir**:
  - **Coerción**: conversión implícita. *Widening* (entero → real, no pierde información) o *narrowing* (real → entero, puede perder).
  - **Casting**: conversión explícita, la escribe el programador.

**Relación:** la compatibilidad es **parte del sistema de tipos**: es el sistema de tipos quien define las reglas de equivalencia y de compatibilidad. Un sistema de tipos estricto tiene reglas de compatibilidad restrictivas (Ada casi no hace coerciones); uno permisivo admite muchas conversiones implícitas (C convierte libremente entre `char`, `int` y `float`).

**b. Tipado estático vs. fuertemente tipado**

**No son lo mismo**: responden a preguntas distintas y son ejes independientes.

- **Tipado estático**: habla de **cuándo** se liga y se chequea el tipo → en **compilación**. (El opuesto es el tipado dinámico: en ejecución.)
- **Fuertemente tipado**: habla de **qué tan estricto** es el sistema → garantiza que **se detectan todos los errores de tipo** y no permite operar con tipos incompatibles. (El opuesto es el débil: permite operaciones y conversiones inseguras.)

| | Fuerte | Débil |
|---|---|---|
| **Estático** | Ada, Java | **C** |
| **Dinámico** | **Python** | JavaScript, Perl |

**Ejemplos:**
- **C es estático pero débil**: los tipos se conocen en compilación, pero compila `int x = 'a' + 3.7;`, permite aritmética de punteros y `union` sin control.
- **Python es dinámico pero fuerte**: los tipos se ligan en ejecución, pero `"3" + 4` lanza `TypeError`.
- JavaScript (dinámico y débil): `"3" + 4` da `"34"` sin error.

---

## 3. Sintaxis y semántica estática de los lenguajes. Defina estos conceptos, indicando claramente y con ejemplos qué analiza cada una y alguna forma de expresarlas.

**Respuesta**

- **Sintaxis**: conjunto de reglas que define **cómo se escribe** un programa, es decir, cómo combinar los caracteres del alfabeto para formar programas válidos.
  - *Qué analiza*: la **forma**. Las reglas **léxicas** (cómo formar las *words*: identificadores, operadores, palabras reservadas) y las **sintácticas** (cómo combinar las words en expresiones y sentencias).
  - *Cómo se expresa*: con gramáticas **libres de contexto**: **BNF**, **EBNF** y **diagramas sintácticos** (Conway).
  - *Ejemplo*: `while x <> y do` es válido en Pascal; `while (x != y)` lo es en C.
- **Semántica estática**: reglas que se verifican **antes de la ejecución** (en compilación) y que la sintaxis no puede expresar porque dependen del **contexto**.
  - *Qué analiza*: las **formas válidas** según el contexto: que las variables estén declaradas, que no se declaren dos veces, que los tipos sean compatibles en una asignación u operación.
  - *Cómo se expresa*: con **gramáticas de atributos** (Knuth): gramáticas sensibles al contexto que asocian atributos a los símbolos y reglas semánticas a cada producción.
    | Regla gramatical | Regla semántica |
    |---|---|
    | `decl → tipo lista-var` | `lista-var.at = tipo.at` |
    | `tipo → int` | `tipo.at = int` |
  - *Ejemplo*: `int e; a := b + c;` es **sintácticamente correcto** (respeta la BNF), pero si `a` no está declarada o los tipos no coinciden, es un **error de semántica estática**.

En el compilador, la sintaxis la verifican los analizadores **léxico** y **sintáctico**; la semántica estática, el **analizador semántico**. Un programa solo se ejecuta si es correcto en ambas.

---

## 4. Pasaje de parámetros
- a. Explique cuáles son los diferentes tipos de pasajes de parámetros que puede proveer un lenguaje.
- b. Indique cuáles de los anteriores le parece más seguro y cuál inseguro justificando su respuesta.

**Respuesta**

**a. Tipos de pasaje**

Se clasifican según la dirección en que viaja el dato:

| Modo | Conexión real-formal | Formas |
|---|---|---|
| **IN** | Solo al inicio | Por valor, por valor constante |
| **OUT** | Solo al final | Por resultado, resultado de funciones |
| **IN/OUT** | Al inicio y al final | Valor-resultado, referencia, nombre |

- **Por valor**: el valor del real **se copia** en el formal, que es una variable local. Los cambios no afectan al real.
- **Por valor constante**: como por valor, pero el formal **no se puede modificar** (`in` de Ada, `const` de C/C++).
- **Por resultado**: el formal es una local **sin valor inicial**; al terminar, su valor **se copia** al real.
- **Resultado de función**: el valor devuelto reemplaza la invocación en la expresión (`return`, o asignar al nombre de la función en Pascal).
- **Por valor-resultado**: se copia al entrar y se copia de vuelta al salir; en el medio se trabaja sobre la copia.
- **Por referencia**: se pasa la **dirección (l-valor)** del real; todo cambio en el formal impacta **inmediatamente** en el real (acceso indirecto).
- **Por nombre**: el formal se **sustituye textualmente** por la expresión del real, que se **reevalúa cada vez que se usa**, en el ambiente del llamador (se implementa con *thunks*).

**b. Más seguro e inseguro**

- **Más seguro: por valor (y por valor constante).** La rutina trabaja sobre una copia, así que el dato del llamador **nunca se modifica**: no hay efectos colaterales ni alias. Su costo es la copia.
- **Más inseguros: por referencia y por nombre.**
  - *Referencia*: crea **alias** entre el formal y el real; la rutina puede modificar el real sin querer y el código se vuelve menos legible y confiable.
  - *Nombre*: el resultado depende de **cuándo** se evalúa la expresión, por lo que es difícil de prever (por ejemplo, con `a[i]` si `i` cambia dentro de la rutina).

---

## 5. Cite al menos dos características que debería tener la sintaxis de un lenguaje. Justifique.

**Respuesta**

Las características deseables son **legibilidad, verificabilidad, facilidad de traducción y falta de ambigüedad**. Por ejemplo:

- **Legibilidad**: el programa debe poder leerse y entenderse fácilmente. *Justificación*: un código claro se escribe con menos errores y es más fácil de mantener y modificar. Ej.: palabras reservadas significativas, comentarios, sentencias de cierre como `end if`.
- **Falta de ambigüedad**: cada construcción debe tener **una sola interpretación**. *Justificación*: si una sentencia admite dos lecturas (como el *else colgante*), el programador y el compilador pueden entenderla distinto y el programa hace algo inesperado.
- **Verificabilidad**: debe ser posible comprobar formalmente que el programa es correcto.
- **Facilidad de traducción**: debe ser sencillo para el compilador procesar el programa, lo que hace la traducción más rápida.

---

## 6. Sentencias y variables.
- a. Sentencia de asignación. Explique detalladamente esta sentencia en el lenguaje C y qué diferencias tiene respecto a los otros lenguajes.
- b. Variables. Defina el concepto, nombre sus atributos y dé una explicación de cada uno de ellos.

**Respuesta**

**a. Sentencia de asignación en C**

La asignación modifica la memoria: guarda en el **l-valor** de la izquierda el **r-valor** de la expresión de la derecha.

En la mayoría de los lenguajes (Pascal `:=`, Ada, Python) la asignación es una **sentencia**, distinta de una expresión. **En C es una expresión con efecto colateral**: además de modificar la memoria, **devuelve el valor asignado**. Consecuencias:

- **Asignación múltiple**, evaluada de derecha a izquierda: `a = b = c = 0;`
- Se puede usar donde va una expresión: `if (i = 30) {...}` es válido (asigna 30 y, como es distinto de 0, la condición es verdadera). Esto es una fuente típica de errores al confundir `=` con `==`; en Pascal o Python sería un error.
- A la izquierda tiene que haber un **l-valor**: `++p = *q;` o `(i < j ? z : y) = 4;` dan error porque esas expresiones devuelven un r-valor.
- Tiene **operadores de asignación compuestos**: `+=`, `-=`, `++`, `--`.

**b. Variables: concepto y atributos**

Una variable es una **abstracción de una celda de memoria**; la asignación modifica destructivamente su valor. Sus atributos son **\<nombre, alcance, tipo, l-valor, r-valor\>**:

- **Nombre**: identificador con el que se la referencia. Aspectos de diseño: longitud máxima, caracteres permitidos, sensibilidad a mayúsculas, palabras reservadas.
- **Alcance**: rango de instrucciones donde el nombre es **conocido**. Puede ser estático (según el texto del programa) o dinámico (según la cadena de llamadas).
- **Tipo**: conjunto de **valores** que puede tomar y de **operaciones** permitidas. Se puede ligar en compilación (estático) o en ejecución (dinámico).
- **L-valor**: **área de memoria** ligada a la variable. Su **tiempo de vida** es el período en que existe esa ligadura.
- **R-valor**: el **valor** codificado almacenado en el l-valor, interpretado según el tipo.

En `x := x + 1`, la `x` de la izquierda usa el l-valor y la de la derecha el r-valor.

---

## 7. Excepciones
- a. Indique cómo simular, en un lenguaje como ADA y Python, un mecanismo de reasunción del manejo de las excepciones.
- b. Indique cómo es el esquema de propagación de ambos lenguajes.

**Respuesta**

**a. Simular reasunción en Ada y Python**

Ada y Python usan el modelo de **terminación**: al ejecutarse el manejador, termina la unidad o bloque donde se produjo la excepción. Para simular **reasunción** (seguir con la instrucción siguiente), se encierra **solo la instrucción que puede fallar** en un bloque propio. Así, la excepción termina únicamente ese bloque interno y el programa continúa con las instrucciones que siguen.

```ada
-- Ada
begin
   declare
   begin
      X := A / B;                   -- puede fallar
   exception
      when Constraint_Error => X := 0;   -- se corrige
   end;                             -- termina solo el bloque interno
   Put(X);                          -- la ejecución sigue acá
end;
```

```python
# Python
try:
    x = a / b                       # puede fallar
except ZeroDivisionError:
    x = 0                           # se corrige
print(x)                            # la ejecución continúa

# Variante: reintentar hasta que funcione
while True:
    try:
        n = int(input())
        break
    except ValueError:
        print("Ingrese un número")
```

**b. Esquema de propagación**

- **Ada (dinámica)**: si la unidad donde se levantó la excepción tiene un manejador, se ejecuta y la unidad termina. Si no lo tiene, la unidad termina y la excepción **se vuelve a levantar en la unidad que la llamó**, siguiendo la cadena de llamadas. Si sale del alcance de su nombre queda **anónima** y solo la captura `when others`. Con `raise;` dentro de un manejador se la relanza hacia arriba.
- **Python (primero estática, luego dinámica)**: primero busca un manejador en los `try` que **contienen textualmente** la instrucción (try anidados). Si no encuentra, sigue **dinámicamente** por la cadena de llamadas. Si nadie la maneja, el programa termina con el mensaje de error estándar (traceback).

---

## 8. Semántica
- a. ¿Qué formas de definir la semántica de un lenguaje conoce?
- b. Defina y diferencie la semántica estática de la dinámica. Ejemplifique.

**Respuesta**

**a. Formas de definir la semántica**

- **Semántica estática** → se define con **gramáticas de atributos** (formal).
- **Semántica dinámica** → no hay una herramienta estándar como BNF. Hay tres formas:
  - **Axiomática** (formal): el programa es una máquina de estados y cada sentencia se describe con **predicados**: precondición y poscondición. Sirve para probar la corrección. Ej.: `{b ≠ 0} c = a/b {a = b*c + r y r < b}`.
  - **Denotacional** (formal): asigna a cada construcción una **función matemática** (recursiva) que da su significado. Ej.: `F(<N>0) = 2*F(<N>)`, `F(<N>1) = 2*F(<N>)+1`.
  - **Operacional** (no formal): describe el significado traduciendo cada construcción a un **lenguaje de bajo nivel sobre una máquina abstracta** (SIMPLESEM); los cambios de estado de esa máquina definen el significado. Es la que usa la materia.

Diferencia entre axiomática y denotacional: una describe los estados con **predicados**, la otra con **funciones**.

**b. Semántica estática vs. dinámica**

| | Estática | Dinámica |
|---|---|---|
| Cuándo | Antes de ejecutar (compilación) | Durante la ejecución |
| Qué describe | Las **formas válidas** según el contexto (nombres y tipos) | El **efecto de ejecutar** cada construcción |
| Ejemplos de error | Variable no declarada o declarada dos veces; tipos incompatibles; condición de un `if` que no es booleana | División por cero; índice fuera de rango (`int notas[10]; notas[15] = 3;`) |
| Cómo se define | Gramática de atributos | Axiomática, denotacional, operacional |

---

## 9. Semántica operacional. Enumere los elementos principales de la semántica operacional y describa dos de ellos.

**Respuesta**

**Elementos:**
1. **Procesador abstracto (SIMPLESEM)**.
2. Su **conjunto de instrucciones**: `set`, `jump`, `jumpt`.
3. **Instancia de unidad**: **segmento de código** + **registro de activación**.
4. **Punto de retorno**.
5. **Ambiente de referencia** (local y no local).

**Descripción de dos:**

- **Procesador abstracto SIMPLESEM**: máquina simple usada para describir el significado de las construcciones. Tiene una **memoria de código C** (`C[y]` es la instrucción en la celda *y*), una **memoria de datos D** (`D[y]` es el valor de la celda *y*; *y* es el l-valor y `D[y]` el r-valor) y un **ip** (instruction pointer) que apunta a la próxima instrucción. Su ciclo es: tomar `C[ip]`, incrementar ip y ejecutar.
- **Instancia de unidad**: representación de una rutina en ejecución. Tiene dos partes:
  - **Segmento de código**: las instrucciones; su contenido es **fijo**.
  - **Registro de activación (RA)**: los datos locales; su contenido es **cambiante**. Guarda las variables locales y el **punto de retorno** (dónde seguir al terminar la rutina).

---

## 10. ¿Para qué sirve el procesador abstracto?

**Respuesta**

Sirve para **describir la semántica operacional** de un lenguaje: cada construcción del lenguaje se traduce a instrucciones de esta máquina simple, y el **efecto** que producen esas instrucciones (los cambios de estado de su memoria) **define el significado** de la construcción. Permite estudiar cómo se ordenan y acceden los datos y cómo funcionan las llamadas, **independientemente del hardware real**.

Está formado por una **memoria de código** (C), una **memoria de datos** (D) y un **puntero a la instrucción** en ejecución (**ip**), y tiene instrucciones como `set`, `jump` y `jumpt`.

---

## 11. Describa el concepto de ligadura y su función.

**Respuesta**

- **Concepto**: la **ligadura (binding)** es la **asociación entre una entidad y uno de sus atributos**. Ej.: entre una variable y su tipo, o entre una variable y su lugar de memoria.
- **Función**: los atributos de una entidad deben estar **fijados antes de poder usarla**. La ligadura es lo que les da valor.

Los lenguajes se diferencian en **cuándo** se produce y en su **estabilidad** (si puede cambiar).

**Momentos de ligadura:**
1. **Definición del lenguaje**: la palabra `int`, la forma de las sentencias.
2. **Implementación**: representación y rango de los enteros.
3. **Compilación**: en `int a;` se liga el tipo a la variable.
4. **Ejecución**: el valor de `a` y su lugar en memoria.

Los tres primeros son **estáticos**; el último, **dinámico**.

---

## 12. Enumere los atributos de las variables y descríbalos en general.

**Respuesta**

Una variable tiene los atributos **\<nombre, alcance, tipo, l-valor, r-valor\>**:

- **Nombre**: string de caracteres (letras y dígitos) usado para identificar la variable. Aspectos de diseño: longitud, caracteres permitidos, si distingue mayúsculas.
- **Alcance**: rango de instrucciones en el que se conoce el nombre.
- **Tipo**: conjunto de valores que puede tomar y operaciones permitidas sobre ella. La protege de operaciones indebidas.
- **L-valor**: lugar de memoria asociado a la variable. Su tiempo de vida es el período en que existe esa asociación.
- **R-valor**: valor codificado almacenado en esa ubicación, interpretado según el tipo.

---

## 13. Clasifique las variables de acuerdo a cada uno de sus atributos y describa cada una en forma general.

**Respuesta**

- **Según el nombre**: no da una clasificación de variables en sí, sino decisiones de diseño (longitud, sensibilidad a mayúsculas, palabras reservadas). Variables **anónimas**: no tienen nombre y se acceden por un puntero.
- **Según el alcance**:
  - **Alcance estático**: se determina por el **texto** del programa (C, Pascal, Ada, Python). **Alcance dinámico**: por la **cadena de llamadas** en ejecución (APL, Lisp original, Perl).
  - **Local**: declarada en la unidad. **No local**: usada en una unidad pero declarada en otra. **Global**: declarada en el programa principal.
- **Según el tipo**:
  - **Ligadura estática** (en compilación, no cambia): *explícita* (por declaración, `int x;`), *implícita* (por reglas, Fortran: I..N enteras) o *inferida* (se deduce de la expresión, lenguajes funcionales).
  - **Ligadura dinámica** (en ejecución, puede cambiar): Python, JavaScript.
  - Según qué tipo: **predefinido**, **definido por el usuario** o **TAD**.
- **Según el l-valor (tiempo de vida)**:
  - **Estática**: alocada antes de ejecutar; vive todo el programa.
  - **Automática**: alocada al activar la unidad; vive mientras la unidad esté activa.
  - **Semidinámica**: como la automática, pero su tamaño se conoce recién al activar la unidad.
  - **Dinámica (explícita)**: alocada con `new`/`malloc` en cualquier momento; vive hasta que se libere.
  - **Persistente**: su vida no depende de la ejecución (archivos, bases de datos).
- **Según el r-valor**:
  - **Variable** (su valor cambia) o **constante** (su valor se congela).
  - Según la inicialización: sin inicializar (queda lo que haya en memoria), con valor por defecto, o inicializada en la declaración.

---

## 14. ¿Es lo mismo una variable global que una variable estática? ¿Qué relación tienen? Ejemplifique.

**Respuesta**

**No.** Se refieren a **atributos distintos**:

- **Global** habla del **alcance**: el nombre es visible en todo el programa.
- **Estática** habla del **l-valor / tiempo de vida**: la memoria se asigna antes de ejecutar y dura todo el programa.

**Relación**: una variable global **suele ser estática** (vive todo el programa porque cualquier unidad puede usarla). Pero **una estática no tiene por qué ser global**: puede ser local.

```c
int total = 0;                 // global y estática

void contador() {
    static int veces = 0;      // estática pero LOCAL:
    veces++;                   // vive todo el programa y conserva su valor,
}                              // pero solo se conoce dentro de contador()
```

---

## 15. Variables automáticas, semidinámicas y dinámicas. Indique diferencias entre ellas (respecto a su l-valor).

**Respuesta**

| | Automática | Semidinámica | Dinámica |
|---|---|---|---|
| Cuándo se liga el l-valor | Al activar la unidad | Al activar la unidad | En cualquier momento de la ejecución (`new`, `malloc`) |
| Tamaño | Conocido en **compilación** | Conocido recién **al activar la unidad** | Puede **cambiar** durante la ejecución |
| Dónde vive | Pila, dentro del RA (offset fijo) | Pila: un **descriptor** en el RA y los datos arriba | **Heap**; en la pila queda solo el puntero |
| Cuándo se libera | Al terminar la unidad | Al terminar la unidad | Explícitamente o por el garbage collector |
| Ejemplo | `int i;` local | `a: array(1..n)` en Ada, con `n` parámetro | Nodo de una lista creado con `new` |

---

## 16. ¿Cuáles son las diferencias semánticas entre variable estática y variable semiestática?

**Respuesta**

- **Estática**: se le asigna una **única dirección fija antes de la ejecución** y la conserva durante **todo el programa**. Es **sensible a la historia**: mantiene su valor entre una llamada y otra. Hay **una sola instancia**, por eso no permite recursión.
- **Semiestática (automática)**: su tamaño y su desplazamiento (offset) dentro del RA se conocen en compilación, pero la memoria se **aloca al activar la unidad** y se libera al terminar. Se crea **una instancia nueva por cada activación**, por eso **permite recursión** y **no conserva** su valor entre llamadas.

Diferencia semántica clave: la estática tiene **memoria entre llamadas** y una sola copia; la semiestática **arranca de cero** en cada activación y hay una copia por activación.

---

## 17. ¿Cuáles son las diferencias semánticas entre variable estática y constante?

**Respuesta**

Fijan **atributos distintos**:

- **Variable estática**: fija el **l-valor**. Su lugar en memoria no cambia durante la ejecución, pero **su valor sí puede cambiar**.
- **Constante**: fija el **r-valor**. **Su valor no puede cambiar**. Su memoria puede ser estática o no.

```c
static int c = 0;  c = 5;        // válido: la dirección es fija, el valor cambia
const int MAX = 10; MAX = 20;    // error: el valor está congelado
```

Además, la ligadura del valor de una constante puede ser estática (Pascal, en compilación) o dinámica pero estable (Ada: `N : constant Integer := F(x);`, se calcula en ejecución y luego no cambia).

---

## 18. ¿Por qué se recomienda el uso de constantes?

**Respuesta**

- **Legibilidad**: un nombre como `MAX_ALUMNOS` explica más que el número `50` suelto en el código.
- **Modificabilidad**: si el valor cambia, se modifica **en un solo lugar** y no en cada aparición.
- **Confiabilidad / protección**: el compilador impide **modificar accidentalmente** el valor.

---

## 19. Describa la ligadura estática y dinámica.

**Respuesta**

- **Ligadura estática**: se establece **antes de la ejecución** (en la definición del lenguaje, la implementación o la compilación) y **no puede cambiar** durante la ejecución. Ej.: en `int a;` (C), el tipo se liga a la variable en compilación.
- **Ligadura dinámica**: se establece **durante la ejecución** y **puede cambiar** según las reglas del lenguaje. Ej.: en Python `x = 5` y luego `x = "hola"`, el tipo de `x` cambia; en cualquier lenguaje, el valor de una variable.

**Excepción**: las **constantes** de algunos lenguajes (Ada) se ligan en ejecución pero, una vez ligadas, no cambian.

Ventajas: la estática es más **eficiente** y **segura** (errores detectados en compilación); la dinámica es más **flexible**.

---

## 20. Alias. Defina, analice e indique ventajas y peligros de los alias. Ejemplifique e indique si se pueden evitar.

**Respuesta**

- **Definición**: un alias se da cuando **dos o más nombres distintos referencian a la misma entidad** (el mismo objeto de datos) en el mismo ambiente. Es lo opuesto a la sobrecarga (un nombre, varias entidades).
- **Cómo se generan**: con punteros, con pasaje de parámetros por referencia, o con construcciones como `EQUIVALENCE` de Fortran o `union` de C.
  ```c
  int x = 0;
  int *i = &x;
  int *j = &x;
  *i = 10;        // ahora x y *j también valen 10
  ```
- **Ventajas**: permiten **compartir datos sin copiarlos**, lo que ahorra memoria y tiempo.
- **Peligros**:
  - Programas **difíciles de leer y entender**: no es evidente que modificar `*i` cambia `x`.
  - **Efectos colaterales**: un dato se modifica por otro camino.
  - Errores **difíciles de encontrar** y dificultad para verificar el programa.
- **¿Se pueden evitar?** Sí, en buena medida: no usando punteros ni pasaje por referencia, usando pasaje por valor, o con lenguajes que los restringen (Java elimina los punteros explícitos). No se eliminan del todo en lenguajes con referencias (dos referencias de Java al mismo objeto también son alias).

---

## 21. Describir y nombrar las ventajas y desventajas de los pasajes de parámetro "por valor" y "por resultado".

**Respuesta**

- **Por valor (modo IN)**: al invocar, el valor del real **se copia** en el formal, que se comporta como una variable local. Al terminar, la conexión se corta.
  - ✔ **Protege** el dato del llamador: nunca se modifica. **Sin efectos colaterales**.
  - ✘ **Costo de tiempo y espacio** por la copia, grave con datos grandes (un arreglo completo).
- **Por resultado (modo OUT)**: el formal es una variable local **sin valor inicial**. Al **terminar** la rutina, su valor **se copia** al real.
  - ✔ **Protege** al real **durante la ejecución** de la rutina: no se toca hasta el final.
  - ✘ **Costo** de la copia. El formal **debe inicializarse dentro** de la rutina. Si se pasa la misma variable dos veces (`p(x, x)`), el valor final depende del **orden** en que se copien los resultados.

---

## 22. Escribir qué aspectos se consideran para las excepciones (o algo parecido, no recuerdo bien) y describir las excepciones por terminación.

**Respuesta**

**Qué debe proveer un lenguaje:**
1. Una forma de **definir** las excepciones.
2. Una forma de **reconocerlas**.
3. Una forma de **lanzarlas y capturarlas**.
4. Una forma de **manejarlas** (el código del manejador).
5. Un **criterio de continuación**: a dónde vuelve el control después del manejador.

**Preguntas para analizar un lenguaje:** qué excepciones se pueden manejar y si hay predefinidas; cómo se lanzan; cómo se definen los manejadores y cuál es su ámbito; cómo se liga la excepción con el manejador; a dónde se cede el control al terminar; cómo se propagan; qué pasa si no hay manejador.

**Modelo de terminación**: cuando se produce una excepción, se ejecuta el manejador, pero **no se vuelve al punto donde ocurrió**: **termina el bloque o unidad** que la levantó y la ejecución continúa **después del manejador** (o en la unidad que llamó). Es el modelo más usado: **Ada, CLU, C++, Java, Python, PHP**.

```python
try:
    x = 10 / 0
    print("no se ejecuta")      # se saltea el resto del try
except ZeroDivisionError:
    print("error")
print("sigue acá")              # se continúa después del manejador
```

Se opone al modelo de **reasunción** (PL/1), en el que el manejador corrige y se continúa en la instrucción siguiente a la que falló.

---

## 23. Describir qué es short circuit.

**Respuesta**

Es una forma de evaluar expresiones lógicas: los operandos se evalúan **de izquierda a derecha** y la evaluación **se corta apenas se conoce el resultado**:

- `a and b`: si `a` es **falso**, no se evalúa `b` (el resultado ya es falso).
- `a or b`: si `a` es **verdadero**, no se evalúa `b` (el resultado ya es verdadero).

**Utilidad**: evita errores en condiciones que dependen de la primera:
```c
if (p != NULL && p->valor > 0)   // si p es NULL, no se accede a p->valor
```
Se opone al **circuito largo**, que evalúa siempre todos los operandos. Hay que tener cuidado si el segundo operando tiene efectos colaterales (por ejemplo, llama a una función que modifica algo): con short circuit puede no ejecutarse.

---

## 24. ¿Qué elementos comparten y cuáles se agregan en el registro de activación basado en pila?

**Respuesta**

**Elementos que comparte con el esquema estático:**
- **Punto de retorno**: dónde continuar al terminar la rutina.
- **Variables locales**.

**Elementos que se agregan:**
- **Enlace dinámico**: puntero al RA de la unidad **llamadora**. Permite volver a su RA al terminar. Los enlaces encadenados forman la **cadena dinámica**.
- **Valor de retorno**: lugar para guardar el resultado de una función.
- **Parámetros**: al pasar a rutinas con parámetros.
- **Enlace estático** (con estructura de bloques o rutinas anidadas): puntero al RA de la unidad que **contiene textualmente** a la actual, para acceder a variables no locales con alcance estático.

Se agregan porque en el esquema basado en pila puede haber **varios RA de la misma rutina** (recursión) y su posición **no se conoce en compilación**: hace falta saber a dónde volver y cómo acceder a cada uno.

---

## 25. Pasaje de parámetros por referencia y por nombre. Cite diferencias semánticas y en el resultado. Ejemplifique y justifique.

**Respuesta**

- **Por referencia**: al invocar se calcula **una sola vez** la **dirección (l-valor)** del parámetro real y se pasa esa dirección. Todo uso del formal es un acceso indirecto a esa misma posición.
- **Por nombre**: el formal se **sustituye textualmente** por la **expresión** del real, que **se reevalúa cada vez que se usa**, en el ambiente del llamador.

**Diferencia semántica**: en referencia, la ligadura con la posición de memoria es **fija desde la llamada**. En nombre, la ligadura se **difiere** hasta cada uso, así que la posición puede cambiar si cambian las variables de la expresión.

**Diferencia en el resultado**: si el real es una variable simple, ambos dan **el mismo resultado**. Si el real es un elemento de arreglo con un índice que cambia dentro de la rutina, **dan resultados distintos**.

**Ejemplo:**
```
a = [10, 20, 30, 40, 50, 60]   (índices 1..6)
m := 4;
B(a[m]);

procedure B(j)
   m := m + 1;
   j := j + 5;
```
- **Por referencia**: `j` se liga a `a[4]` al llamar. Aunque `m` pase a 5, `j` sigue siendo `a[4]`: **`a[4]` pasa de 40 a 45**.
- **Por nombre**: `j` es el texto `a[m]`. Después de `m := m+1`, `j` se evalúa como `a[5]`: **`a[5]` pasa de 50 a 55** y `a[4]` queda en 40.

**Justificación**: la diferencia se da porque en nombre la expresión `a[m]` se evalúa en cada uso y `m` cambió entre la llamada y el uso.

---

## 26. Definir sintaxis abstracta, concreta y pragmática. Ejemplificar.

**Respuesta**

- **Abstracta**: se refiere a la **estructura** de las construcciones, sin importar los símbolos con que se escriben. Dos construcciones con la misma estructura tienen la misma sintaxis abstracta.
- **Concreta**: se refiere a la **parte léxica**, a los símbolos concretos que se usan para escribir la construcción.
- **Pragmática**: se refiere al **uso práctico** de la construcción: qué es más legible, cómodo o propenso a errores.

**Ejemplo con un `while`:**
```c
while (x != y) { ... }              // C
```
```pascal
while x <> y do begin ... end        { Pascal }
```
- **Abstracta**: son **iguales**; ambos son *while condición bloque*.
- **Concreta**: son **distintos**: `!=` vs. `<>`, `{ }` vs. `begin end`, `( )` vs. `do`.
- **Pragmática**: `<>` puede ser más legible que `!=`. En C se pueden omitir las `{ }` si el bloque tiene una sola sentencia, pero eso lleva a errores al agregar otra sentencia después; por eso se recomienda usarlas siempre.

---

## 27. ¿El l-valor de una variable se relaciona con su alcance? Justificar.

**Respuesta**

**No directamente: son atributos distintos e independientes.**

- El **alcance** indica **dónde se conoce el nombre** (en qué instrucciones es visible). Es una propiedad del texto del programa (con alcance estático).
- El **l-valor** indica **dónde está la variable en memoria y cuánto tiempo existe** (tiempo de vida). Es una propiedad de la ejecución.

**Justificación con ejemplo**: pueden no coincidir.
```c
void f() {
    static int c = 0;   // alcance: solo dentro de f
    c++;                // l-valor: existe durante todo el programa
}
```
`c` sigue **viva** (conserva su l-valor y su valor) aun cuando la ejecución está fuera de `f`, donde su nombre **no es visible**. Al revés, una variable local de `main` está viva durante toda la ejecución, pero no es visible dentro de las funciones que `main` llama (con alcance estático, si no están anidadas).

En el caso de las **variables automáticas** sí suelen coincidir: su memoria se aloca al activar la unidad y se libera al terminar, que es justamente cuando su nombre es visible. Pero esto es una coincidencia de implementación, no una regla.

---

## 28. Dar al menos 3 diferencias entre el paradigma lógico y el imperativo.

**Respuesta**

| | Imperativo | Lógico |
|---|---|---|
| **Qué es un programa** | Una **secuencia de sentencias** que modifican el estado | Un conjunto de **hechos y reglas** (aserciones lógicas, cláusulas de Horn) |
| **Enfoque** | Describe **cómo** resolver el problema (los pasos) | Describe **qué** se sabe del problema; el motor busca la respuesta |
| **Variables** | **Celdas de memoria** modificables por asignación | **Incógnitas** que se ligan por unificación; no se modifican |
| **Control** | **Explícito**: secuencia, selección e iteración | **Implícito**: lo maneja el motor de inferencia (búsqueda y backtracking) |
| **Ejecución** | Cambios de estado | **Deducción** a partir de hechos y reglas para responder una consulta |

```prolog
padre(juan, pedro).
abuelo(X, Z) :- padre(X, Y), padre(Y, Z).
?- abuelo(juan, Quien).
```
En imperativo habría que escribir el algoritmo que recorre los datos y compara; en lógico solo se declaran las relaciones.

---

## 29. Compare paradigma funcional y paradigma imperativo.

**Respuesta**

| | Imperativo | Funcional |
|---|---|---|
| **Programa** | Secuencia de **sentencias** | Conjunto de **definiciones de funciones**; se evalúan expresiones |
| **Enfoque** | **Cómo** (pasos) | **Qué** (el valor de una expresión) |
| **Variable** | **Celda de memoria** que cambia por asignación | Variable **matemática**: un valor fijo, inmutable |
| **Ejecución** | Cambios de estado de la memoria | **Reducción** de expresiones hasta su forma canónica |
| **Control** | Secuencia, selección, iteración (bucles) | **Composición** de funciones y **recursión** |
| **Efectos colaterales** | Sí (una función puede modificar variables no locales) | No: **transparencia referencial** (una misma expresión siempre da el mismo valor) |
| **Funciones** | Generalmente no son valores | Son **valores de primera clase**: se pasan como parámetro y se devuelven |
| **Eficiencia** | Mayor (cercano a la arquitectura de Von Neumann) | Menor eficiencia de ejecución |

Ejemplo: factorial.
```c
int fact(int n) { int r = 1; for (int i = 2; i <= n; i++) r *= i; return r; }   // imperativo
```
```haskell
fact 0 = 1
fact n = n * fact (n - 1)        -- funcional
```

---

## 30. ¿Qué analizan las reglas léxicas?

**Respuesta**

Las reglas léxicas indican **cómo formar las palabras (*words*) del lenguaje a partir de los caracteres del alfabeto**: identificadores, palabras reservadas, operadores, números, literales, comentarios.

Ejemplos de lo que definen:
- Si un identificador debe empezar con letra y qué caracteres admite (`_`, `$`).
- Si se distinguen **mayúsculas y minúsculas** (C y Python sí; Pascal no).
- Cómo se escriben los operadores: distinto es `!=` en C y `<>` en Pascal.

En el compilador las verifica el **analizador léxico (scanner)**: divide el programa en **tokens**, descarta blancos y comentarios y da error si algo no pertenece a ninguna categoría léxica válida. Se diferencian de las **reglas sintácticas**, que indican cómo combinar esas words en expresiones y sentencias.

---

## 31. ¿En esquema estático puede haber variables automáticas? Justifique.

**Respuesta**

**No.** En el esquema estático **toda la memoria se reserva antes de la ejecución**: cada variable tiene una dirección fija y cada rutina un único RA fijo, conocidos en compilación.

Una variable **automática** se aloca **al activar la unidad** y se libera **al terminar**, y puede haber una instancia por cada activación. Eso requiere crear y destruir espacio durante la ejecución con disciplina LIFO, es decir, una **pila**, que el esquema estático no tiene.

En un esquema estático, las variables locales de una rutina son en realidad **estáticas**: existen durante todo el programa.

---

## 32. ¿Por qué en esquema estático de ejecución no puede haber recursividad? ¿Por qué las variables son sensibles a la historia?

**Respuesta**

- **Sin recursión**: en el esquema estático cada rutina tiene **un único registro de activación fijo**, con un solo lugar para su **punto de retorno** y sus **variables locales**. Si la rutina se llamara a sí misma, la segunda activación usaría ese mismo RA y **pisaría** el punto de retorno y los valores locales de la primera: al volver no se sabría a dónde regresar y los datos de la activación anterior se perderían. La recursión necesita un RA **nuevo** por cada activación, cuya cantidad no se conoce en compilación.
- **Sensibles a la historia**: como la memoria de cada variable se asigna antes de ejecutar y **nunca se libera**, al volver a llamar una rutina sus variables locales **conservan el valor que tenían al terminar la llamada anterior**. Su valor depende de la historia de ejecuciones previas.

---

## 33. Compare el paradigma lógico con el funcional. Dé al menos 3 diferencias.

**Respuesta**

Ambos son **declarativos** (describen el *qué*, no el *cómo*) y no tienen efectos colaterales, pero:

1. **Base**: el funcional se basa en **funciones** matemáticas; el lógico en **relaciones** (predicados) expresadas como hechos y reglas.
2. **Ejecución**: el funcional ejecuta por **reducción** de expresiones hasta obtener un valor; el lógico por **deducción**: el motor busca demostrar una consulta a partir de los hechos y reglas (con backtracking).
3. **Dirección y resultados**: una función tiene una **dirección fija** (entrada → salida) y devuelve **un único valor**. Un predicado puede usarse **en varias direcciones** y dar **varias respuestas**: con `padre(X, pedro)` se busca el padre; con `padre(juan, Y)`, los hijos.
4. **Variables**: en el funcional una variable representa un **valor**; en el lógico es una **incógnita** que se liga por unificación.
5. **Resultado**: en el funcional, un **valor**; en el lógico, **sí/no** y los valores de las variables que hacen verdadera la consulta.

---

## 34. Sintaxis
- a. ¿Qué es BNF y EBNF? Defina y detalle sus diferencias. Ejemplifique.
- b. ¿Qué diferencia hay entre BNF y gramática de atributos?

**Respuesta**

**a. BNF y EBNF**

- **BNF (Backus-Naur Form)**: notación formal (un **metalenguaje**) para describir la sintaxis mediante una gramática **libre de contexto**. Usa los metasímbolos `< >` (no terminal), `::=` (se define como) y `|` (alternativa). La repetición se expresa con **recursión**.
- **EBNF (BNF extendida)**: agrega metasímbolos para hacer las reglas más cortas: `[ ]` (opcional), `( | )` (elegir una alternativa), `{ }` (repetición; `*` cero o más veces, `+` una o más).

**Diferencias**: ambas tienen el **mismo poder expresivo**. EBNF **elimina la recursión** para las repeticiones, por lo que es **más corta y legible**.

**Ejemplo: entero con signo opcional**
```
BNF:   <enterosig> ::= + <entero> | - <entero> | <entero>
       <entero>    ::= <digito> | <entero> <digito>
       <digito>    ::= 0|1|2|3|4|5|6|7|8|9

EBNF:  <enterosig> ::= [ (+|-) ] <digito> { <digito> }*
```

**b. BNF vs. gramática de atributos**

- **BNF** es **libre de contexto**: describe solo la **forma** del programa (sintaxis). Por eso acepta `int e; a := b + c;` aunque `a` no esté declarada o los tipos no coincidan.
- La **gramática de atributos** es **sensible al contexto**: parte de una gramática como la BNF, pero a cada símbolo le asocia **atributos** (tipo, valor, lugar en memoria) y a cada producción, **reglas semánticas** que los calculan. Así describe la **semántica estática**: chequeo de tipos, variables declaradas, duplicados.

---

## 35. Manejo de memoria
- a. Indique cómo es el manejo de memoria (y las características del lenguaje) en esquemas de ejecución estático, basado en pila y dinámico.
- b. Indique qué elementos necesita un esquema basado en pila para controlar la zona de datos.
- c. Indique qué ventajas tiene un esquema basado en pila respecto del esquema estático.

**Respuesta**

**a. Esquemas estático, basado en pila y dinámico** ⚠️ (detalle de pila y dinámico)

| | Estático | Basado en pila | Dinámico |
|---|---|---|---|
| **Memoria** | **Fija**, se conoce en compilación | **Predecible**: no se conoce en compilación pero sigue disciplina **LIFO** | **Impredecible** |
| **Alocación** | Toda **antes de ejecutar** | Al **activar** cada unidad (se libera al terminar) | En **cualquier momento** de la ejecución |
| **RA** | Uno fijo por rutina | Uno nuevo por cada activación, apilado | Datos en la **heap** |
| **Recursión** | **No** | **Sí** | Sí |
| **Lenguajes** | Fortran, Cobol (C1, C2) | Algol, Pascal, C (C3 a C5') | Lisp, APL, Python (C5'', C6) |

En los lenguajes **dinámicos** (C6) además puede haber tipado y alcance dinámico; las variables no locales se buscan por la cadena dinámica.

**b. Elementos para controlar la zona de datos en un esquema basado en pila** ⚠️
- **current**: dirección base del RA de la unidad en ejecución. Las variables locales se acceden por **desplazamiento (offset)** respecto de `current`.
- **free**: próxima celda libre, el tope de la pila. Ahí se crea el RA de la próxima unidad llamada.
- **Enlace dinámico** en cada RA: puntero al RA del llamador; los enlaces forman la **cadena dinámica** y permiten restaurar `current` al terminar.
- **Enlace estático** (si hay anidamiento): para acceder a variables no locales.

**c. Ventajas del esquema basado en pila sobre el estático**
- Permite **recursión** (un RA nuevo por activación).
- **Usa mejor la memoria**: solo ocupan espacio las unidades **activas**, y el espacio se libera al retornar.
- Admite **datos cuyo tamaño se conoce recién en ejecución** (semidinámicos).
- Las variables locales **no son sensibles a la historia**: cada activación empieza limpia.

---

## 36. Variables
- a. Clasifique las variables de acuerdo a su tiempo de vida. Indique características de cada una de ellas.
- b. Punteros. Si una variable es de tipo puntero, ¿qué tipo de variable sería en la clasificación realizada en el punto a? Indique al menos 2 ejemplos de problemas de uso de punteros, además de generar alias.

**Respuesta**

**a. Clasificación según el tiempo de vida**

- **Estáticas**: se alocan **antes de la ejecución** y viven **todo el programa**. Dirección fija; son sensibles a la historia. Ej.: `static` en C, variables globales.
- **Automáticas (semiestáticas)**: se alocan **al activar la unidad** donde se declaran y se liberan al **terminar** la unidad. Viven en la pila, dentro del RA. Ej.: variables locales de una función en C.
- **Semidinámicas**: como las automáticas, pero su tamaño se conoce recién al activar la unidad (arreglos con límites dados por parámetros).
- **Dinámicas (explícitas)**: se alocan **explícitamente** con un constructor (`new`, `malloc`) en cualquier momento, y viven **hasta que se liberan** (con `free`/`dispose` o por el garbage collector). Viven en la **heap** y no tienen nombre (son **anónimas**): se acceden por un puntero.
- **Persistentes**: su vida **no depende de la ejecución** del programa (archivos, bases de datos).

**b. Punteros**

- **Tipo de variable**: el **puntero en sí** es una variable común; si es local es **automática** (vive en la pila) y si es global, estática. Lo que **apunta** es normalmente una variable **dinámica explícita** (anónima, en la heap).
- **Problemas de uso** (además de los alias):
  1. **Referencias sueltas (dangling)**: el puntero apunta a una variable dinámica que **ya fue liberada**. Ej.: `p = q; free(q);` → `p` apunta a memoria inválida.
  2. **Objetos perdidos (garbage)**: variables de la heap a las que **ya ningún puntero apunta**; ocupan memoria y no se pueden liberar. Ej.: `p = malloc(...); p = malloc(...);` → se pierde el primer bloque.
  3. **Punteros no inicializados**: permiten un acceso **descontrolado** a la memoria. Se mitiga con el valor nulo (`NULL`, `nil`, `None`).
  4. **Violación de tipos**: tratar lo apuntado como otro tipo (por ejemplo, con aritmética de punteros o con uniones que mezclan enteros y punteros).

---

## 37. Explique la filosofía del paradigma orientado a objetos y qué elementos de este paradigma pueden ser simulados en un lenguaje imperativo y cuáles no y por qué.

**Respuesta**

**Filosofía**: *un programa es un conjunto de objetos que interactúan enviándose mensajes*. Se modela el problema como entidades del mundo real:

- **Objeto**: entidad con **estado interno** (atributos) y **comportamiento** (métodos). Equivale a un dato abstracto.
- **Mensaje**: pedido de un objeto a otro para que ejecute un método. Todo el procesamiento se activa por mensajes.
- **Clase**: tipo definido por el usuario que describe la estructura y el comportamiento de sus **instancias**, con **ocultamiento** de la representación (primer nivel de abstracción).
- **Herencia**: organiza las clases en jerarquías de sub y superclases (segundo nivel de abstracción).
- **Polimorfismo** y **binding dinámico**: distintos objetos responden al mismo mensaje, y el método que se ejecuta se decide **en ejecución**.

**Qué se puede simular en un lenguaje imperativo:**
- **Encapsulamiento y abstracción de datos**: con registros + rutinas que operan sobre ellos, o con módulos y TAD (`struct` + funciones en C, paquetes en Ada).
- Parcialmente el **ocultamiento de información** (archivos separados, `static` en C, parte privada de un paquete Ada).

**Qué no se puede simular naturalmente y por qué:**
- **Herencia**: el lenguaje no tiene un mecanismo para que un tipo **herede** estructura y comportamiento de otro y lo extienda; habría que duplicar código o anidar registros a mano.
- **Polimorfismo con binding dinámico**: requiere que el lenguaje resuelva **en ejecución** qué método corresponde según la clase del objeto. Se puede imitar a mano con punteros a funciones, pero no es un soporte del lenguaje y no tiene la seguridad del chequeo de tipos.

---

## 38. Parámetros
- a. Explique qué diferencias hay entre compartir datos a través de un ambiente común y a través del pasaje de parámetros. Cite ventajas y desventajas.
- b. Indique cómo se compartirían los datos a través de un ambiente común. Ejemplifique.

**Respuesta**

**a. Diferencias, ventajas y desventajas**

- **Ambiente común (no local)**: las unidades comparten datos accediendo a **variables declaradas fuera de ellas**, ya sea de forma implícita (según la regla de alcance) o explícita (un área común declarada por el programador).
  - ✔ No hay costo de pasar datos.
  - ✘ **Pérdida de control**: cualquier unidad puede modificarlos; **efectos colaterales**; errores **difíciles de encontrar**; menos legibilidad.
- **Pasaje de parámetros**: los datos se pasan **explícitamente** en cada invocación.
  - ✔ Datos **distintos en cada llamada**, más **flexibilidad y abstracción**, **protección** (las variables no quedan visibles donde no se necesitan), **legibilidad** y **modificabilidad** (lo que se comparte está a la vista en la interfaz).
  - ✘ Costo de pasar los datos (por ejemplo, la copia en el pasaje por valor).

Por eso el pasaje de parámetros se considera la **mejor forma** de compartir datos.

**b. Cómo se comparten datos por ambiente común**

Con un **ambiente común explícito**, el programador declara un área compartida y las unidades que la necesitan la incluyen:

```fortran
C     Fortran: bloque COMMON
      PROGRAM PRINCIPAL
      COMMON /DATOS/ A, B
      A = 1.0
      CALL SUB
      END

      SUBROUTINE SUB
      COMMON /DATOS/ X, Y      ! X y Y son la misma memoria que A y B
      Y = X + 1.0
      END
```
Otros ejemplos: las variables externas de PL/1 y los **paquetes** de Ada (las unidades que hacen `with Paquete` acceden a sus variables). Con **ambiente implícito**, basta que la variable esté en el alcance: una variable global en C es visible en todas las funciones.

---

## 39. Excepciones. Indique qué son y explique detalladamente qué cosas uno debería analizar de un lenguaje (o tener en cuenta al diseñar uno nuevo) a la hora de utilizar sus estructuras de manejo de excepciones. Ejemplifique esos puntos de análisis, tomando un lenguaje de los vistos en la materia.

**Respuesta**

**Qué son**: una excepción es una **condición inesperada o inusual** que ocurre **durante la ejecución** y **no puede manejarse en el contexto local**. Ej.: división por cero, índice fuera de rango, desbordamiento de pila. Interrumpe el flujo normal y transfiere el control a un **manejador** previamente definido, que permite que el programa **se recupere** en vez de abortar. Pueden ser **implícitas** (predefinidas por el lenguaje) o **explícitas** (definidas por el programador).

**Qué analizar de un lenguaje (o tener en cuenta al diseñar uno nuevo):**
1. ¿Qué excepciones se pueden manejar? ¿Hay **predefinidas**? ¿Cómo se **definen** las propias?
2. ¿Cómo se **lanzan**?
3. ¿Cómo se definen los **manejadores** y cuál es su **ámbito**?
4. ¿Cómo se **liga** una excepción con su manejador?
5. ¿A dónde se cede el control al terminar el manejador? (**reasunción** o **terminación**)
6. ¿Cómo se **propagan** si no hay manejador local?
7. ¿Qué pasa si **nadie** la maneja?

**Ejemplo con Java:**
1. Hay excepciones predefinidas (`ArithmeticException`, `NullPointerException`); las propias se definen como **clases** que heredan de `Exception` (son objetos, instancias de `Throwable`).
2. Se lanzan con `throw new MiExcepcion();`.
3. Los manejadores son bloques `catch (Tipo e)` asociados a un `try`; su ámbito es el bloque `try`.
4. Se liga por **tipo**: el primer `catch` cuyo tipo coincida (por eso van de lo más específico a lo más general).
5. Modelo de **terminación**: se abandona el resto del `try` y se continúa después del `try/catch`. El bloque `finally` se ejecuta **siempre**.
6. Si no hay `catch` que coincida, se propaga a los `try` externos y luego al **método llamador** (los métodos declaran con `throws` las excepciones que pueden lanzar).
7. Si llega a `main` sin manejar, el programa termina mostrando la traza de la excepción.

```java
try {
    int x = a / b;
} catch (ArithmeticException e) {
    System.out.println("División por cero");
} finally {
    System.out.println("Siempre se ejecuta");
}
```

---

# Finales 2026

## 1. Describa las situaciones que hacen que una gramática sea ambigua y la manera de detectar la ambigüedad. Explique cuál de los diferentes tipos de sintaxis (abstracta, concreta o pragmática) debe considerarse en esta situación y justifique su respuesta.

**Respuesta**

**Qué hace que una gramática sea ambigua**: una gramática es ambigua cuando **una misma sentencia se puede derivar de más de una forma**, es decir, tiene **más de un árbol sintáctico (de derivación)**. Las situaciones típicas son:

- **Producciones recursivas por ambos lados sin precedencia ni asociatividad**:
  ```
  <exp> ::= <exp> + <exp> | <exp> * <exp> | <id>
  ```
  `a + b * c` tiene dos árboles: `(a + b) * c` y `a + (b * c)`. Lo mismo pasa con `a - b - c` si no se fija la asociatividad.
- **El *else colgante***: con `if … then` e `if … then … else`, la sentencia `if x>0 then if x<10 then x:=0 else x:=1000` admite dos árboles, porque el `else` puede pertenecer a cualquiera de los dos `if`.

**Cómo detectarla**: encontrando **una cadena que tenga dos árboles de derivación distintos** (o, equivalentemente, dos derivaciones por izquierda distintas). Señales de alerta: un no terminal que aparece recursivamente a izquierda y a derecha en la misma producción, o construcciones opcionales anidadas como el `else`. ⚠️ No existe un algoritmo general que decida si cualquier gramática libre de contexto es ambigua; en la práctica se buscan contraejemplos.

**Cómo se resuelve**: reescribiendo la gramática con un nivel por precedencia y recursión de un solo lado para la asociatividad (`<exp> ::= <exp> + <term> | <term>`, `<term> ::= <term> * <factor> | <factor>`), o agregando una regla o una marca de cierre al lenguaje.

**Qué tipo de sintaxis se considera**: la **sintaxis abstracta**, porque la ambigüedad es un problema de **estructura**: una misma forma concreta (la misma cadena de símbolos) se corresponde con **dos estructuras abstractas distintas**, y por lo tanto con dos significados. La sintaxis concreta no es el problema: los símbolos son los mismos. Al resolverla, en cambio, sí se puede tocar la **concreta** (agregar `end if`, `fi`, `{ }`), y la **pragmática** sugiere qué solución es más legible (la regla de "el `else` va con el `if` más cercano" de C y Pascal elimina la ambigüedad pero puede ser poco legible; `end if` de Ada es más claro).

---

## 2. Explique y detalle 3 atributos de variables (puede reforzar la explicación con pseudocódigo), que sean necesarios para conocer e implementar al trabajar en un modelo de ejecución basado en pila y explique el motivo por el cual son necesarios.

**Respuesta**

En un modelo basado en pila, las variables locales se alocan en el **registro de activación (RA)** de cada activación, y se acceden por un **desplazamiento (offset)** respecto de la base del RA (`current`). Para implementarlo, el compilador necesita conocer:

1. **Tipo** → necesario para calcular el **tamaño** de cada variable. Con los tamaños, el compilador calcula el **offset** de cada variable dentro del RA y el tamaño total del RA a reservar en la pila al activar la unidad.
2. **Alcance** → necesario para saber **en qué RA está la variable** al usarla. Si es **local**, está en el RA actual (`D[current + offset]`). Si es **no local**, está en el RA de otra unidad, al que se llega siguiendo el **enlace estático** (alcance estático) o la cadena dinámica (alcance dinámico).
3. **L-valor (tiempo de vida)** → necesario para saber **dónde** alocarla. Una variable **automática** va en el RA y se crea y destruye con cada activación. Una **estática** va en una zona fija, fuera de la pila. Una **dinámica** va en la heap y en el RA solo queda su puntero.

```
procedure P(n: integer)          // RA de P: | retorno | enlace din. | n | x |
   x: integer;                   //            offsets:   0      1        2   3
   if n > 0 then P(n - 1);       // cada llamada crea un RA nuevo con su propia n y x
   x := n;                       // acceso: D[current + 3] := D[current + 2]
```

Sin el tipo no se sabría cuánto lugar ocupa cada variable; sin el alcance no se sabría en qué RA buscarla; sin el tiempo de vida no se sabría si va en la pila o en otra zona.

---

## 3. Describa qué significa un paradigma de lenguaje de programación y mencione las diferencias conceptuales entre un paradigma orientado a objetos y uno orientado a aspectos.

**Respuesta**

**Paradigma**: estilo de desarrollo de programas; un **modelo para resolver problemas** que determina cómo se estructura un programa (como sentencias, funciones, hechos y reglas u objetos). Un lenguaje puede encuadrarse en uno o en varios paradigmas.

**Diferencias conceptuales:**

- **Orientado a objetos**: el programa es un conjunto de **objetos que interactúan mediante mensajes**. La unidad de modularización es la **clase**: agrupa datos y comportamiento de una entidad, con ocultamiento, herencia y polimorfismo.
- **Orientado a aspectos**: busca **dividir el programa en módulos independientes**, cada uno con un comportamiento y una responsabilidad bien definidos. ⚠️ Su motivación es que hay funcionalidades **transversales** (*crosscutting concerns*): logging, seguridad, manejo de transacciones, control de errores. En POO quedan **dispersas** por muchas clases y **mezcladas** con su lógica principal. La orientación a aspectos las separa en un módulo propio, el **aspecto**, que define **qué** código ejecutar (*advice*) y **dónde** (*pointcuts*, que seleccionan *join points* como llamadas a métodos). Luego un proceso de **tejido** (*weaving*) inserta ese código en los puntos indicados.

| | Orientado a objetos | Orientado a aspectos |
|---|---|---|
| Unidad de modularización | Clase / objeto | Aspecto |
| Organiza según | Las **entidades** del problema | Las **incumbencias transversales** |
| Problema que resuelve | Abstracción y reutilización de entidades | Código repetido y disperso en muchas clases |
| Relación | Paradigma base | **Complementa** a la POO (se usa sobre ella, ej. AspectJ sobre Java) |

---

## 4. Mencione y describa con ejemplos en pseudocódigo y su justificación las formas de propagación de una excepción (al menos 2).

**Respuesta**

Cuando una excepción se produce y **no hay manejador en el lugar donde ocurrió**, se **propaga**: se busca un manejador en otro lugar. Hay dos formas:

**1. Propagación estática**: se busca el manejador en las **unidades o bloques que contienen textualmente** a la instrucción que falló (según la estructura del código).

```
procedure P
   try                          // try externo
      try                       // try interno
         x := a / 0             // ocurre ZeroDivision
      catch IndexError
         ...                    // no coincide
   catch ZeroDivision
      print("manejada")         // la encuentra en el bloque que lo contiene
```
*Justificación*: el manejador se puede determinar mirando el texto del programa, sin ejecutarlo. Python empieza así (try anidados) y CLU busca así dentro del procedimiento llamador.

**2. Propagación dinámica**: si la unidad no tiene manejador, **termina** y la excepción se vuelve a levantar **en la unidad que la llamó**, siguiendo la **cadena de llamadas** de la ejecución.

```
procedure C
   raise Error                  // C no tiene manejador → termina C

procedure B
   C()                          // B tampoco → termina B

procedure A
   B()
exception
   when Error => print("manejada en A")   // la encuentra siguiendo las llamadas
```
*Justificación*: el manejador depende de **quién llamó** a la unidad, lo que solo se sabe en ejecución. Es el esquema de Ada, y el que usan C++, Java y Python después de agotar la búsqueda estática.

Si en ninguno de los dos recorridos se encuentra manejador, el programa termina (con el manejador por defecto del lenguaje).

---

## 5. Explique el modelo de manejo de excepciones por reasunción de PL1. Indique en qué contextos podría ser utilizado este mecanismo.

**Respuesta**

**Modelo de reasunción**: cuando se produce una excepción, se ejecuta el manejador, que puede **corregir** la situación, y luego la ejecución **continúa en la instrucción siguiente** a la que provocó la excepción. No se abandona la unidad.

**En PL/1** (el primer lenguaje con manejo de excepciones), las excepciones se llaman **CONDITIONS**:
- **Declarar un manejador**: `ON CONDITION(nombre) manejador`, donde el manejador es una instrucción o un bloque `BEGIN … END`.
- **Lanzar** una excepción: `SIGNAL CONDITION(nombre)`.
- Tiene **condiciones predefinidas** con manejador por defecto, como `ZERODIVIDE`, que el programador puede redefinir.
- **Ligadura dinámica**: la excepción se liga con el **último manejador definido** (los manejadores se apilan a medida que se ejecutan los `ON`). El alcance de un manejador termina cuando termina la unidad donde se declaró.

```pli
ON ZERODIVIDE BEGIN;
   PUT LIST('División por cero, uso 0');
   X = 0;
END;
X = A / B;          /* si B = 0, se ejecuta el manejador */
PUT LIST(X);        /* y se continúa acá */
```

**Contextos donde podría usarse**: situaciones en las que el error es **recuperable** y tiene sentido **corregir y seguir** sin abandonar el cálculo:
- **Entrada de datos** inválida: pedir el dato de nuevo o usar un valor por defecto.
- **Desbordamientos o errores numéricos** en cálculos donde se puede usar un valor límite.
- **Fin de archivo** o eventos de E/S que son parte del funcionamiento normal.
- Sistemas que **no pueden detenerse** (control, tiempo real), donde es preferible continuar con un valor seguro.

Su desventaja es que puede ocultar errores y complicar el flujo del programa; por eso la mayoría de los lenguajes eligieron el modelo de terminación.

---

## 6. Explique al menos 2 diferentes esquemas de liberación implícita de memoria y el motivo de su implementación.

**Respuesta**

**Motivo**: las variables dinámicas se crean en la **heap**. Si su liberación queda en manos del programador (`free`, `dispose`), aparecen dos problemas: **referencias sueltas (dangling)** si se libera algo que otro puntero sigue usando, y **objetos perdidos (garbage)** si se pierde el último puntero sin liberar. La liberación **implícita** (el **garbage collector**) delega en el sistema la detección de los objetos inaccesibles. Es clave en lenguajes con muchas variables dinámicas (Lisp, Java, Python, PHP); debe ser eficiente y poder correr en paralelo.

**1. Cuenta de referencias (PHP)**
- Cada objeto guarda un **contador** de cuántas referencias lo apuntan (en PHP, el contenedor *zval* tiene los campos `refcount` e `is_ref`).
- Cada vez que se agrega una referencia, el contador aumenta; cuando se elimina, disminuye.
- **Cuando llega a 0**, el objeto no es accesible y **se libera inmediatamente**.
- ✔ Libera la memoria apenas deja de usarse. ✘ Costo de actualizar contadores y **no detecta ciclos** (dos objetos que se apuntan entre sí nunca llegan a 0), lo que causaba fugas de memoria en PHP hasta la versión 5.3.

**2. Generacional (Java)**
- Divide la heap en **generaciones**: joven, vieja y permanente.
- Se basa en que **la mayoría de los objetos vive poco**: los objetos nuevos van a la generación joven, que se recorre con frecuencia; los que sobreviven varias recolecciones **envejecen** y pasan a la generación vieja, que se revisa menos seguido.
- Se eliminan los objetos sin referencias y se **compactan** los bloques de cada generación.
- ✔ Eficiente: concentra el trabajo donde está la mayor parte de la basura; maneja los ciclos (porque busca objetos alcanzables desde la pila, no cuenta referencias). Java ofrece varias implementaciones: Serial GC, Parallel GC, CMS y Garbage First (G1).

---

## 7. Explique y detalle el uso de la sentencia break en las estructuras de selección múltiple como C y PHP.

**Respuesta**

En C (y C++), el `switch` evalúa una expresión y salta a la etiqueta `case` cuyo valor constante coincida. **Una vez que entra a una rama, ejecuta esa rama y continúa con las sentencias de las ramas siguientes** (*fall-through*), **salvo que encuentre un `break`**. El `break` provoca la **salida inmediata del `switch`**. Si ningún `case` coincide, se ejecuta `default` (que es opcional).

```c
switch (op) {
    case '+': result = a + b; break;    // sin break, seguiría con el '-'
    case '-': result = a - b; break;
    default:  result = a * b;
}
```

**Usos del `break`:**
- Lo normal es ponerlo **al final de cada rama** para que se comporte como una selección exclusiva.
- Omitirlo a propósito permite que **varias etiquetas compartan código**:
  ```c
  switch (c) {
      case 'a': case 'e': case 'i': case 'o': case 'u':
          vocales++; break;
      default:
          otras++;
  }
  ```
- Olvidarlo es un error muy común y difícil de detectar: el compilador no lo marca.

**PHP** ⚠️: el `switch` funciona igual que en C: hay *fall-through* y se necesita `break` para salir de cada rama; `default` es opcional. Diferencias: los `case` pueden ser cualquier expresión (incluidos strings) y la comparación es **no estricta** (`==`). Dentro de un `switch`, `continue` se comporta como `break`. Y `break n` permite salir de **n estructuras anidadas** a la vez.

**Comparación con otros lenguajes**: en Pascal, Ada y Ruby **no existe el fall-through**: se ejecuta solo la rama elegida y no hace falta `break`. Ada además exige cubrir todos los valores (o usar `when others`). Por eso el `switch` de C es más flexible pero **menos seguro**.

---

## 8. Realice una comparación entre el esquema de ejecución estático y basado en pila y describa sus características fundamentales.

**Respuesta**

| | Estático | Basado en pila |
|---|---|---|
| **Memoria** | **Fija**: toda se calcula en compilación | **Predecible**: no se conoce en compilación, pero crece y decrece con disciplina **LIFO** |
| **Cuándo se aloca** | Toda **antes de ejecutar** | Al **activar** cada unidad; se libera al **terminar** |
| **Registros de activación** | **Uno fijo por rutina**, en una dirección absoluta | **Uno nuevo por cada activación**, apilado en el tope |
| **Recursión** | **No es posible**: una segunda activación pisaría el único RA | **Sí**: cada activación tiene su propio RA |
| **Variables locales** | En realidad **estáticas**: **sensibles a la historia** | **Automáticas**: se crean en cada activación y no conservan valor |
| **Acceso a variables** | Por dirección absoluta | Por **offset** respecto de `current` (la base del RA actual) |
| **Contenido del RA** | Punto de retorno + variables locales | Se agregan **enlace dinámico**, **valor de retorno**, parámetros y, con anidamiento, **enlace estático** |
| **Datos de tamaño variable** | No | Sí, semidinámicos (descriptor en el RA y datos sobre la pila) |
| **Lenguajes** | Fortran, Cobol (modelos C1, C2) | Algol, Pascal, C (modelos C3 a C5') |

**Características fundamentales:**
- **Estático**: simple y eficiente (no hay costo de alocación en ejecución, todo se resuelve antes), pero **rígido**: sin recursión ni datos de tamaño variable, y desperdicia memoria porque todas las rutinas tienen su espacio reservado aunque no estén activas.
- **Basado en pila**: usa memoria **solo para las unidades activas**, permite **recursión** y datos cuyo tamaño se conoce al activar la unidad. A cambio, tiene el costo de crear y destruir RA y de acceder a las variables de forma relativa. Usa los registros `current` y `free` y la **cadena dinámica** para controlar la pila.

---

## 9. Describa las características fundamentales de un paradigma funcional y los elementos más comunes que lo componen detallando sus características. Explique brevemente el concepto de currificación.

**Respuesta**

**Características fundamentales**: el programa es un conjunto de **definiciones de funciones** y su ejecución es la **evaluación de expresiones**.
- **Semántica basada en valores** y **transparencia referencial**: una expresión siempre da el mismo valor, que depende solo de sus subexpresiones. **No hay efectos colaterales** ni asignación.
- **Funciones como valores de primera clase**: se pasan como parámetro y se devuelven como resultado.
- **Composición** de funciones como forma principal de combinar.
- Manejo **automático de memoria**.
- Ventajas: vista uniforme de programa y función, sin efectos colaterales. Desventaja: **ineficiencia** de ejecución.

**Elementos principales:**
- **Función**: correspondencia `f: A → B` entre un dominio y un codominio. Importa **qué valor** devuelve, no cómo: `doble x = x + x` y `doble' x = 2 * x` son la misma función.
- **Script**: lista de definiciones que se someten a evaluación (`?cuadrado (3+4)` → 49).
- **Expresión**: noción central; *una expresión es su valor*.
- **Variable**: es una variable **matemática** que representa un valor, no una celda de memoria.
- **Evaluación por reducción**: se simplifica la expresión hasta su **forma canónica**. Lo que no se puede reducir (como `1/0`) vale **bottom** (indefinido). Dos órdenes:
  - **Aplicativo**: evalúa primero los argumentos.
  - **Normal (lazy)**: no evalúa un argumento hasta que se necesita, y no más de una vez.
- **Tipos**: básicos (`num`, `bool`, `char`) y derivados (pares, funciones). Toda función tiene un tipo, declarado o **inferido**. Admite **tipos polimórficos**: `id x = x` tiene tipo `β → β`.
- **Cálculo lambda**: modelo formal de cómputo en el que se basan estos lenguajes (`λx. x + 1`).

**Currificación**: técnica que **reemplaza una función con un argumento estructurado (varios parámetros juntos) por una función que recibe los argumentos de a uno** y devuelve otra función.
```
suma  (x, y) = x + y        -- tipo: (num, num) → num
suma' x y    = x + y        -- tipo: num → (num → num)

suma' 3      -- es una función que suma 3:  num → num
```
Permite la **aplicación parcial**: pasar menos argumentos y obtener una nueva función.

---

## 10. Respecto de tipos de datos, indique qué permiten las reglas de equivalencia, cuáles conoce y escriba ejemplos sencillos.

**Respuesta**

**Qué permiten**: decidir **si dos tipos son el mismo**. Junto con las reglas de conversión, determinan la **compatibilidad**: si un valor de un tipo puede usarse donde se espera otro (asignaciones, parámetros, operandos). Son parte del **sistema de tipos**.

**Cuáles hay:**

- **Equivalencia por nombre**: dos variables tienen el mismo tipo si se declaran **juntas** o con **el mismo nombre de tipo**. Es más **estricta** y segura, y fácil de implementar (se comparan nombres).
- **Equivalencia por estructura**: dos variables tienen el mismo tipo si sus tipos tienen **la misma estructura** (los mismos componentes, en el mismo orden). Es más **flexible**, pero más costosa de verificar y puede considerar iguales tipos que el programador pensó distintos.

**Ejemplos:**
```pascal
type T1 = array[1..10] of integer;
     T2 = array[1..10] of integer;
var  a: T1;
     b: T2;
     c: T1;
     d, e: array[1..10] of integer;
```
| Asignación | Por nombre | Por estructura |
|---|---|---|
| `a := c` | ✔ mismo nombre de tipo (`T1`) | ✔ |
| `a := b` | ✘ `T1` y `T2` son nombres distintos | ✔ misma estructura |
| `d := e` | ✔ declaradas juntas | ✔ |
| `a := d` | ✘ | ✔ |

---

## 11. Tome dos lenguajes de programación de los vistos, que presenten un manejo de propagación de errores con una marcada diferencia. Explique detalladamente el manejo de cada uno para notar su diferencia. Indique cuál considera más seguro y por qué.

**Respuesta**

Ambos usan el modelo de **terminación**, pero propagan las excepciones de forma muy distinta.

**Ada — propagación dinámica**
- Se declaran como una variable más: `MiError : exception;`. Se lanzan con `raise MiError;`.
- Los manejadores van **al final de cada unidad** (bloque, subprograma, paquete, tarea), después de `exception`, con `when MiError => …` y `when others => …`.
- **Propagación**: si la unidad donde ocurrió tiene un manejador para la excepción, lo ejecuta y la unidad **termina**. Si no lo tiene, la unidad termina y la excepción se **vuelve a levantar en la unidad que la llamó**, y así siguiendo por la **cadena de llamadas**. Si en el camino sale del alcance de su nombre, queda **anónima** y solo la puede atrapar `when others`. Con `raise;` dentro de un manejador se relanza la misma excepción.
```ada
procedure C is
begin
   raise MiError;            -- C no la maneja: termina y se levanta en B
end C;

procedure B is
begin
   C;                        -- B tampoco: termina y se levanta en A
end B;

procedure A is
begin
   B;
exception
   when MiError => Put("manejada en A");
end A;
```

**CLU — el que la lanza no la maneja; búsqueda estática en el llamador**
- Solo los **procedimientos** lanzan excepciones, y deben **declararlas en el encabezado**: `p = proc() signals (error1, error2)`. Se lanzan con `signal error1`.
- **Un procedimiento no puede manejar las excepciones que él mismo genera**: al lanzarla, **termina** y devuelve el control a su **llamador inmediato**.
- Los manejadores se **asocian a sentencias**: `<sentencia> except when error1: … when others: … end`.
- **Propagación**: en el llamador se busca un manejador en la sentencia que hizo la llamada y, si no hay, en las sentencias que la **contienen textualmente** (búsqueda **estática**). Si no se encuentra en ese procedimiento, **no sigue subiendo**: se levanta la excepción predefinida **`failure`** y el programa termina. Para pasarla explícitamente un nivel más arriba se usa `resignal`.
```
p = proc(x: int) signals (negativo)
    if x < 0 then signal negativo end      % p termina; la maneja quien llamó
    ...
end p

% en el llamador:
p(n) except when negativo: n := 0 end
```

**Diferencias clave:**

| | Ada | CLU |
|---|---|---|
| Quién maneja | La propia unidad puede manejarla | Nunca la unidad que la lanza; siempre el llamador |
| Propagación | **Dinámica**, por toda la cadena de llamadas | **Un solo nivel**; dentro del llamador, **estática** |
| Declaración | Como una variable; no se declara en la interfaz | Obligatoria en el encabezado (`signals`) |
| Si nadie la maneja | Sube hasta el programa principal, puede quedar anónima | Se convierte en `failure` |

**¿Cuál es más seguro?** **CLU**:
- Las excepciones que puede lanzar un procedimiento están **declaradas en su interfaz**: el llamador sabe exactamente qué debe manejar. Es la misma idea que el `throws` de Java.
- La propagación es **acotada y explícita**: una excepción no puede viajar sin control por muchos niveles ni volverse **anónima** como en Ada; si no se maneja donde corresponde, se convierte en `failure`, que es explícita, o se debe relanzar a propósito con `resignal`.

Ada es más **flexible** (cualquier nivel puede manejarla), pero una excepción puede aparecer lejos de donde se originó, en una unidad que no sabía que podía recibirla.

(Otra comparación válida: **PL/1**, con reasunción y ligadura dinámica al último manejador, vs. **Ada**, con terminación y propagación dinámica.)
