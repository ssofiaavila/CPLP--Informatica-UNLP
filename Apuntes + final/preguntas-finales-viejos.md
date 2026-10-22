# Conceptos y paradigmas de lenguajes de programación | Preguntas finales viejos

1. Estática, global, constante, puntero. Relacione estos conceptos con los "atributos" de la entidad variable. Realice una explicación completa, justificando cada relación y dando ejemplos.

2. Tipos
    - a. Sistema de tipos y compatibilidad de tipos. Defina estos dos conceptos en forma completa e indique qué relación hay entre ellos. 
    - b. Lenguaje de tipado estático, lenguaje fuertemente tipado. Indique si estos dos conceptos son iguales o indican diferentes cosas. Justifique la respuesta en forma completa. Ejemplifique.
3. Sintaxis y semántica estática de los lenguajes. Defina estos conceptos, indicando claramente y con ejemplos qué analiza cada una y alguna forma de expresarlas.
4. Pasaje de parámetros
    - a. Explique cuáles son los diferentes tipos de pasajes de parámetros que puede proveer un lenguaje.
    - b. Indique cuáles de los anteriores le parece más seguro y cuál inseguro justificando su respuesta.
5. Cite al menos dos características que debería tener la sintaxis de un lenguaje. Justifique.
6. Sentencias y variables.
    - a. Sentencia de asignación. Explique detalladamente esta sentencia en el lenguaje C y qué diferencias tiene respecto a los otros lenguajes.
    - b. Variables. Defina el concepto, nombre sus atributos y dé una explicación de cada uno de ellos.
7. Excepciones
    - a. Indique cómo simular, en un lenguaje como ADA y Python, un mecanismo de reasunción del manejo de las excepciones.
    - b. Indique cómo es el esquema de propagación de ambos lenguajes.
8. **Semántica**
    - a. **¿Qué formas de definir la semántica de un lenguaje conoce?**
        Teniendo en cuenta que la semántica da significado a los programas sintácticamente correctos, no hay una forma formal si no que hay distintos momentos en los que se evalúa la semántica:
        1. Semántica estática: se verifica antes de que el programa se ejecute.
        2. Semántica dinámica: se verifica durante su funcionamiento.
        3. Gramática de atributos: distintos tipos de semántica.
    - b. **Defina y diferencie la semántica estática de la dinámica. Ejemplifique.**
        La semántica estática se encarga de realizar la comprobación de nombres y tipos sobre las operaciones que se realizarán en el programa, mientras que la semántica dinámica describe el efecto de ejecutar las diferentes construcciones en el lenguaje. Es decir, la semántica estática va a evaluar que en una condición de if el valor de la condición sea un valor de verdad. La semántica dinámica verifica que por ejemplo en el recorrido de un vector realizando la búsqueda de un valor, no se excedan los límites del mismo.
9. **Semántica operacional. Enumere los elementos principales de la semántica operacional y describa dos de ellos.**

10. **¿Para qué sirve el procesador abstracto?**
    El procesador abstracto nos sirve para estudiar la estructura de un lenguaje de programación por ejemplo para saber cómo las estructuras de datos son ordenadas y accedidas en un conjunto de instrucciones de maquinas. Es un procesador que cuenta con una memoria de código, otra de datos y un puntero a la instrucción que se está ejecutando(ip).

11. Describa el concepto de ligadura y su función.
    
12. **Enumere los atributos de las variables y descríbalos en general.**
    Una variable tiene los siguientes atributos <nombre, alcance, tipo, l-valor, r-valor>
- *Nombre:* caracteres alfabéticos y numéricos utilizados para identificar la variable.
- *Alcance:* rango de instrucciones en el que se conoce el nombre.
- *Tipo:* valores y operaciones permitidas.
- *l-valor:* lugar de memoria asociado con la variable (tiempo de vida).
- *r-valor:* valor codificado almacenado en la ubicación de la variable.
13. Clasifique las variables de acuerdo a cada uno de sus atributos y describa cada una en forma general.
14. ¿Es lo mismo una variable global que una variable estática? ¿Qué relación tienen? Ejemplifique.
15. Variables automáticas, semidinámicas y dinámicas. Indique diferencias entre ellas (respecto a su l-valor).
16. ¿Cuáles son las diferencias semánticas entre variable estática y variable semiestática?
17. ¿Cuáles son las diferencias semánticas entre variable estática y constante?
18. ¿Por qué se recomienda el uso de constantes?
19. Describa la ligadura estática y dinámica.
20. Alias. Defina, analice e indique ventajas y peligros de los alias. Ejemplifique e indique si se pueden evitar.
21. Describir y nombrar las ventajas y desventajas de los pasajes de parámetro "por valor" y "por resultado".
22. Escribir qué aspectos se consideran para las excepciones (o algo parecido, no recuerdo bien) y describir las excepciones por terminación.
23. Describir qué es short circuit.
24. ¿Qué elementos comparten y cuáles se agregan en el registro de activación basado en pila?
25. Pasaje de parámetros por referencia y por nombre. Cite diferencias semánticas y en el resultado. Ejemplifique y justifique.
26. Definir sintaxis abstracta, concreta y pragmática. Ejemplificar.
27. ¿El l-valor de una variable se relaciona con su alcance? Justificar.
28. Dar al menos 3 diferencias entre el paradigma lógico y el imperativo.
29. Compare paradigma funcional y paradigma imperativo.
30. ¿Qué analizan las reglas léxicas?
31. ¿En esquema estático puede haber variables automáticas? Justifique.
32. ¿Por qué en esquema estático de ejecución no puede haber recursividad? ¿Por qué las variables son sensibles a la historia?
33. Compare el paradigma lógico con el funcional. Dé al menos 3 diferencias.
34. Sintaxis
    - a. ¿Qué es BNF y EBNF? Defina y detalle sus diferencias. Ejemplifique.
    - b. ¿Qué diferencia hay entre BNF y gramática de atributos?
35. Manejo de memoria
    - a. Indique cómo es el manejo de memoria (y las características del lenguaje) en esquemas de ejecución estático, basado en pila y dinámico.
    - b. Indique qué elementos necesita un esquema basado en pila para controlar la zona de datos.
    - c. Indique qué ventajas tiene un esquema basado en pila respecto del esquema estático.
36. Variables
    - a. Clasifique las variables de acuerdo a su tiempo de vida. Indique características de cada una de ellas.
    - b. Punteros. Si una variable es de tipo puntero, ¿qué tipo de variable sería en la clasificación realizada en el punto a? Indique al menos 2 ejemplos de problemas de uso de punteros, además de generar alias.
37. Explique la filosofía del paradigma orientado a objetos y qué elementos de este paradigma pueden ser simulados en un lenguaje imperativo y cuáles no y por qué.
38. Parámetros
    - a. Explique qué diferencias hay entre compartir datos a través de un ambiente común y a través del pasaje de parámetros. Cite ventajas y desventajas.
    - b. Indique cómo se compartirían los datos a través de un ambiente común. Ejemplifique.
39. Excepciones. Indique qué son y explique detalladamente qué cosas uno debería analizar de un lenguaje (o tener en cuenta al diseñar uno nuevo) a la hora de utilizar sus estructuras de manejo de excepciones. Ejemplifique esos puntos de análisis, tomando un lenguaje de los vistos en la materia.

---
# Finales 2026
1. Describa las situaciones que hacen que una gramática sea ambigua y la manera de detectar la ambigüedad. Explique cuál de los diferentes tipos de sintaxis (abstracta, concreta o pragmática) debe considerarse en esta situación y justifique su respuesta.
2. Explique y detalle 3 atributos de variables (puede reforzar la explicación con pseudocódigo), que sean necesarios para conocer e implementar al trabajar en un modelo de ejecución basado en pila y explique el motivo por el cual son necesarios.
3. Describa qué significa un paradigma de lenguaje de programación y mencione las diferencias conceptuales entre un paradigma orientado a objetos y uno orientado a aspectos.
4. Mencione y describa con ejemplos en pseudocódigo y su justificación las formas de propagación de una excepción (al menos 2).
5. Explique el modelo de manejo de excepciones por reasunción de PL1. Indique en qué contextos podría ser utilizado este mecanismo. 
6. Explique al menos 2 diferentes esquemas de liberación implícita de memoria y el motivo de su implementación.
7. Explique y detalle el uso de la sentencia break en las estructuras de selección múltiple como C y PHP.
8. Realice una comparación entre el esquema de ejecución estático y basado en pila y describa sus características fundamentales.
9. Describa las características fundamentales de un paradigma funcional y los elementos más comunes que lo componen detallando sus características. Explique brevemente el concepto de currificación.
10. Respecto de tipos de datos, indique qué permiten las reglas de equivalencia, cuáles conoce y escriba ejemplos sencillos.
11. Tome dos lenguajes de programación de los vistos, que presenten un manejo de propagación de errores con una marcada diferencia. Explique detalladamente el manejo de cada uno para notar su diferencia. Indique cuál considera más seguro y por qué.
