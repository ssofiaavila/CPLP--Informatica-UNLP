# Clase 7 — Tipos de datos

## 1. Concepto e historia

**Tipo** = conjunto de **valores** + conjunto de **operaciones** para manipularlos.
Evolución: pocos tipos básicos → tipos definidos por el usuario → **TAD** (separa representación y operaciones) → **clase**.

Clasificación:

| | Elementales | Compuestos |
|---|---|---|
| **Predefinidos** | Enteros, reales, caracteres, booleanos | String |
| **Definidos por el usuario** | Enumerados | Arreglos, registros, listas |

## 2. Tipos predefinidos (built-in / primitivos)

Reflejan el hardware y lo abstraen. Ventajas:
- **Invisibilidad de la representación**.
- **Verificación estática**.
- **Desambiguar operadores** (`+` entero o real).
- **Control de precisión**.

Los valores pueden estar definidos por el *lenguaje* (en su definición) o por la *implementación* (los elige el compilador).

## 3. Tipos definidos por el usuario

Se construyen agrupando tipos elementales mediante **constructores**. Ventajas: **legibilidad** (buenos nombres), **estructura jerárquica** (refinamiento), **modificabilidad** (se cambia solo la definición), **factorización** (se reutiliza), chequeo de consistencia.

### Constructores
| Constructor | Idea | Estructura |
|---|---|---|
| **Producto cartesiano** | n-tuplas ordenadas (a1,…,an) | Registros / `struct`. Acceso por campo (`pol.nro_lados`) |
| **Correspondencia finita** | Función de un dominio finito (índice) a un tipo resultado | Arreglos (`char digits[10]`), listas, diccionarios |
| **Unión** | Disyunción de tipos: campos **mutuamente excluyentes** | `union` de C. Chequeo en ejecución |
| **Unión discriminada** | Unión + **discriminante** que indica qué opción está activa | Registro variante |
| **Recursión** | Datos de tamaño arbitrario y estructura compleja | Listas, árboles; se implementan con **punteros** |

Problemas de la unión discriminada: el discriminante y las variantes se pueden manejar por separado, la implementación puede ignorar el chequeo, o puede omitirse el discriminante.

## 4. Punteros

Variable cuyo **r-valor es una referencia** (dirección) a otro objeto. Valores: direcciones o **nulo**. Operaciones: asignación (ligada a la alocación) y **desreferenciación**.
Sirven para: estructuras de tamaño arbitrario, relaciones múltiples sin duplicar datos, acceso a bajo nivel.
Se los compara con el `goto`: amplían las celdas que una variable puede referenciar.

### Inseguridad de los punteros
1. **Violación de tipos**: tratar lo apuntado como otro tipo (aritmética de punteros).
2. **Referencias sueltas (dangling)**: el puntero apunta a una variable dinámica **ya desalocada**.
3. **Punteros no inicializados**: acceso descontrolado a memoria. Solución: valor nulo (`nil` Pascal, `NULL` C, `null` Ada, `None` Python).
4. **Punteros y uniones discriminadas**: `union { int int_var; int* int_ref; }` permite fabricar direcciones. Java elimina el puntero explícito.
5. **Alias**: `p1 = &x; p2 = &x;`.
6. **Liberación de memoria — objetos perdidos (garbage)**: objetos de la heap a los que ya nadie apunta.

## 5. Manejo de memoria

Las variables puntero van en la **pila**; lo apuntado (creado con `new`) va en la **heap**. Un objeto es **accesible** si alguna variable de la pila lo alcanza; si no, es **basura**.

- **Liberación explícita** (`dispose`, `free`, `delete`): la decide el programador. Riesgo: dejar otros punteros **dangling**.
- **Liberación implícita — garbage collector**: el sistema detecta la basura en ejecución. Clave en lenguajes con muchas variables dinámicas (Lisp, Python, Java). Debe ser eficiente y correr en paralelo.

| | Java | PHP |
|---|---|---|
| Mecanismo | **Generacional** | **Cuenta de referencias** |
| Cómo | Heap en 3 generaciones (joven, vieja, permanente); los objetos envejecen y se compactan | Cada objeto cuenta sus referencias; al llegar a 0 se elimina |

Ada chequea que el tiempo de vida de lo apuntado no sea menor que el del puntero.

## 6. Tipos abstractos de datos (TAD)

**Abstracción**: representar algo por sus características esenciales ocultando el resto.
**TAD = representación (datos) + operaciones.** Debe cumplir:
- **Encapsulamiento**: representación y operaciones en una única unidad sintáctica.
- **Ocultamiento de la información**: la representación y la implementación quedan ocultas → modificabilidad.

Unidades que lo implementan: módulo (Modula-2), **paquete** (Ada), **clase** (C++, Java). Simula-67 tenía `class` pero sin ocultamiento.
Especificación formal: **sintaxis** (operaciones y tipos) + **semántica** (axiomas de comportamiento).
**Clase** = atributos + métodos; agrega un segundo nivel de abstracción: jerarquías con **herencia**.

## 7. Sistema de tipos

**Definición**: conjunto de **reglas** que usa un lenguaje para **estructurar y organizar sus tipos**. Objetivo: escribir **programas seguros**. Tensión: seguridad vs. flexibilidad.

Provee mecanismos para definir tipos y asociarlos a las construcciones, y reglas de **equivalencia**, **compatibilidad** e **inferencia**. Se especifica por: tipo y tiempo de chequeo, reglas de equivalencia y conversión, reglas de inferencia, nivel de polimorfismo.

### Tipado estático vs. dinámico (¿cuándo se liga/chequea?)
- **Estático**: en **compilación**. Variables declaradas con su tipo.
- **Dinámico**: en **ejecución**. Más comprobaciones en ejecución.

### Tipado fuerte vs. débil (¿qué tan estricto?)
- **Fuerte**: el sistema de tipos garantiza que **se detectan todos los errores de tipo** (restringe operar con tipos distintos).
- **Débil**: lo contrario; permite operaciones y conversiones inseguras.

**No son lo mismo** — son ejes independientes:

| | Fuerte | Débil |
|---|---|---|
| **Estático** | Gobstones, Ada, Java | **C** |
| **Dinámico** | **Python** | Perl, JavaScript |

### Equivalencia y compatibilidad
- **Compatibilidad**: reglas que determinan si el tipo de un objeto es **válido en un contexto**. Un tipo es compatible con otro si es **equivalente** o **se puede convertir**.
- Las **reglas de equivalencia** permiten decidir si dos tipos son el mismo:
  - **Por nombre**: mismo tipo si se declaran juntas o con el mismo nombre de tipo.
  - **Por estructura**: mismo tipo si sus componentes son iguales.
  ```
  type T1 = array[1..10] of integer;  T2 = array[1..10] of integer;
  var a: T1; b: T2;   -- por nombre: distintos; por estructura: iguales
  ```
- **Coerción**: conversión **implícita** de un tipo a otro.
  - **Widening** (ensanchar): no pierde información (entero → real).
  - **Narrowing** (estrechar): puede perder información (real → entero).
- **Casting**: conversión **explícita**.

*Relación sistema de tipos ↔ compatibilidad*: el sistema de tipos es quien define las reglas de compatibilidad.

### Inferencia
El tipo se **deduce** en vez de declararse: por un operador (`n mod m`), un operando (`n*2`), un argumento (`n:int`) o el tipo del resultado.

### Polimorfismo
- **Monomórfico**: cada entidad se liga a un único tipo. **Polimórfico**: puede ligarse a más de uno.
- **Ad-hoc**: comportamiento distinto según el tipo → **sobrecarga** y **coerción**.
- **Universal**: una misma operación uniforme sobre tipos relacionados → **paramétrico** (tipos parametrizados, `lista(T)`) y **por inclusión** (subtipos y herencia).

---

## Preguntas de final relacionadas

- **2a, 32** — Sistema de tipos y compatibilidad, y su relación: punto 7.
- **2b, 44** — Tipado estático vs. fuertemente tipado: tabla (C estático y débil; Python dinámico y fuerte).
- **54** — Reglas de equivalencia (nombre y estructura) con ejemplo.
- **41, 49b** — Punteros: tipo de variable y problemas (dangling, garbage, no inicializados, violación de tipos, alias).
