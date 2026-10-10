# Clase 4 — Semántica operacional: repaso y unidades de programa

> Nota: los PDF "clase 4" y "clase 5" que subiste son **el mismo archivo**. Este resumen cubre todo su contenido.

## 1. Repaso

Entidades (variables, unidades, sentencias) → atributos → ligadura (estática / dinámica, estabilidad) → momentos (definición, implementación, compilación, ejecución). Variable: nombre, alcance, tipo, l-valor, r-valor.

## 2. Unidades de programa (rutinas)

Una unidad representa una **acción abstracta**. Dos formas: **procedimientos** y **funciones** (devuelven un valor). C, C++ y Python solo tienen funciones (los procedimientos se simulan devolviendo `void`).

Las rutinas tienen los mismos atributos que las variables:

| Atributo | En una rutina |
|---|---|
| **Nombre** | Identificador con que se la invoca; se introduce en la declaración. |
| **Alcance** | Rango donde se conoce el nombre: desde la declaración hasta un cierre. La llamada debe estar dentro del alcance. Puede ser estático o dinámico. |
| **Tipo** | El encabezado: tipos de los parámetros y del retorno. **Signatura**: `fun: T1 x T2 x … x Tn → R`. La llamada es correcta si respeta el tipo. |
| **L-valor** | Lugar de memoria donde está el **cuerpo** (código). |
| **R-valor** | La **ejecución** del código. Normalmente estático; dinámico con **punteros a rutinas** (variables de tipo rutina). |

### Definición vs. declaración
- **Declaración**: solo el encabezado (prototipo). **Definición**: encabezado + cuerpo.
- Distinguirlas (C, C++, Ada) permite **rutinas mutuamente recursivas**: se declara A, se define B (que usa A) y luego se define A. En Pascal se usa `forward`.
- En algunas implementaciones de C, sin prototipo se asume que devuelve `int`; C++ no asume nada.

## 3. Comunicación entre rutinas

- **Ambiente no local** (variables compartidas).
- **Parámetros**: datos distintos en cada llamada; más legible y modificable.
  - **Formales**: los de la definición. **Reales**: los de la invocación.

Ligadura entre formales y reales:
- **Posicional**: uno a uno según el orden. Variante con valores por defecto (C++: `int distancia(int a=0, int b=0)`).
- **Por nombre**: se indica a qué formal va cada real (Ada: `Ejem(C => Z, A => X)`). Hay que conocer los nombres de los formales. Ada permite mezclar ambos.

## 4. Representación en ejecución

- **Instancia de la unidad**: representación de la rutina en ejecución. Tiene dos partes:
  - **Segmento de código**: instrucciones; contenido **fijo**.
  - **Registro de activación (RA)**: datos locales; contenido **cambiante**.

### Procesador abstracto SIMPLESEM
**¿Para qué sirve?** Para entender qué efecto producen las instrucciones del lenguaje al ejecutarse: se describe la semántica traduciendo cada construcción a instrucciones de esta máquina simple (semántica operacional).

Componentes:
- **Memoria de código C**: `C[y]` es la instrucción en la celda *y*.
- **Memoria de datos D**: `D[y]` es el valor en la celda *y* (*y* es el l-valor, `D[y]` el r-valor).
- **ip** (instruction pointer): apunta a la instrucción a ejecutar.
- Ciclo: tomar `C[ip]`, incrementar ip, ejecutar.

Instrucciones:
- `set destino, origen` — `set 10, D[20]` copia el valor de la celda 20 en la 10. E/S: `set 15, read` / `set write, D[50]`.
- `jump 47` — salto incondicional.
- `jumpt 47, D[13] > D[8]` — salto condicional.
- Direccionamiento indirecto: `set D[10], D[20]`, `jump D[30]`.

### Elementos en ejecución
- **Punto de retorno**: información cambiante que se guarda en el RA de la unidad llamada.
- **Ambiente de referencia**: **local** (variables en su propio RA) y **no local** (variables en RA de otras unidades).

## 5. Estructuras de ejecución de los lenguajes

| Esquema | Memoria | Características |
|---|---|---|
| **Estático** | Espacio **fijo** | Todo se conoce antes de ejecutar; alocación estática; **no hay recursión**. |
| **Basado en pila** | Espacio **predecible** | No se puede calcular en traducción pero sigue disciplina LIFO; variables se alocan al activar la unidad y se desalocan al terminar (Algol-60). La pila es parte del modelo semántico, no del lenguaje. |
| **Dinámico** | Espacio **impredecible** | Los datos se crean en cualquier momento de la ejecución; se usan en la **heap**. |

## 6. Lenguajes modelo estáticos

- **C1**: tipos simples, sin funciones, datos estáticos, solo `main`. Un único RA; cada variable tiene una celda fija de D.
- **C2**: C1 + rutinas internas (no anidadas, no recursivas) + datos globales. Sigue siendo **todo estático**: un RA fijo por rutina. Aparece el **call-return**: antes de saltar se guarda el **punto de retorno** en el RA de la rutina llamada (`set 6,16; jump 100` … `jump D[6]`).
- **C2'**: rutinas **compiladas por separado**. El compilador no puede ligar direcciones absolutas (usa relativas); el **linkeditor** combina los módulos y completa las ligaduras. Semánticamente igual a C2.

---

## Preguntas de final relacionadas

- **10, 27** — Elementos de la semántica operacional: procesador abstracto (C, D, ip), instrucciones, segmento de código, registro de activación, punto de retorno, ambiente de referencia.
- **11** — Para qué sirve el procesador abstracto: punto 4.
- **43** — Por qué no hay recursión en el esquema estático: cada rutina tiene **un solo RA fijo**, con un solo lugar para el punto de retorno y las locales; una segunda activación los pisaría. Las variables son **sensibles a la historia** porque esa memoria nunca se libera: conservan el valor de la llamada anterior.
- **42** — ¿Variables automáticas en esquema estático? No: todo se aloca antes de ejecutar; las automáticas requieren pila.
- **47, 56** — Esquemas estático / pila / dinámico: punto 5 (el detalle de pila está en el complemento de Clase 5).
