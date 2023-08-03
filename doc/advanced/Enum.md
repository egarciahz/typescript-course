# Enumeraciones

Las enumeraciones son un objeto el cual nor permite crear una coleccion de llaves numbradas que al declararse en codigo su valor es 'auto asignado' por el interprete de TypeScript de las sigientes formas:

- Secuencial o Automatico: el interprete de aTypescript asigna los valores a los miembros del enum automaticamente y en orden contando desde 0.
- Indexado: inicializando el contador interno a un valor numerico definido por el usuario.
- Manualmente, pudiendo usar numeros o cadenas como valores asignados a cada miembro del enum.

```TypeScript

enum AUTO_ASSIGNMENT {
    KEY_A, // 0
    KEY_B, // 1
    KEY_C  // 2
}

enum INDEXED_ASSIGNMENT {
    KEY_A = 4, // 4
    KEY_B, // 5
    KEY_C  // 6
}

enum MANUALLY_ASSIGNMENT {
    KEY_A = 4,   // 4
    KEY_B = "B", // "B"
    KEY_C = 0,   // 0
    KEY_D = "C"  // "C"
}

enum MIXED_A_ASSIGNMENT {
    KEY_A = 4,  // 4
    KEY_B,      // 5
    KEY_C = "C" // "C"
}

enum MIXED_B_ASSIGNMENT {
    KEY_A = 4,   // 4
    KEY_B = "B", // "B"
    KEY_C // ERROR: the internal counter is broken because the prev value are not a number
}

enum MIXED_C_ASSIGNMENT {
    KEY_A = 4,   // 4
    KEY_B = "B", // "B"
    KEY_C = 5,   // OK: the internal counter was reset to 5
    KEY_D        // 6
}
```

Algo a tener en cuenta es que el interprete siempre intentara indexar las proiedades del enum por lo que si el contador interno se 'rompe' como se muestra en `MIXED_B_ASSIGNMENT` usted tendra que inicializarlo manualmente como en el ejemplo `MIXED_C_ASSIGNMENT`. Una forma de prevenir el problema del contador roto es definir los miembros de valor asignado por el usuario al ultimo:

```TypeScript

enum MIXED_B_ASSIGNMENT {
    KEY_A, // 0
    KEY_B, // 1
    KEY_C, // 2
    KEY_D = 500, // 500
    KEY_E = "SomeValue", // "SomeValue"
    KEY_F = "OtherValue", // "OtherValue"
}

```

Como consideracion final evite el asignar frases como valor a los miembros de un enum; esto quiere decir que los valores del enum deben ser una palabra que se use como clave de comparacion o en su defecto un string case _(camel-case, snake-case, pascal-case, etc...)_ y nunca una frase separada por espacios o son normalizar.

```TypeScript

enum WRONG_ENUM {
    KEY_A = "Value",  // OK
    KEY_B = "OtherValue", // OK, bu it's not a pascal-case value like your sibling member
    KEY_C = "Another Value described" // supported by languaje but not recomended
}

enum GOOD_ENUM {
    KEY_A = "Value",  // OK
    KEY_B = "OtherValue", // OK
    KEY_C = "AnotherValueDescribed" // OK
}

```

## Enum bajo el capo

Los enum son elementos del lenguaje TypeScript y no tienen igual en Javascript por lo que el compilador hace algunas cosas 'magicas' para dar sentido a esto al codigo resultante es un truco del lenguaje y aunque no afecta el rendimiento habra casos en los que mantener el codigo de salida lo mas legible posible pueda tener beneficios.

Codigo TypeScript:

```TypeScript
enum MIXED_C_ASSIGNMENT {
    KEY_A = 4,   // 4
    KEY_B = "B", // "B"
    KEY_C = 5,   // OK: the internal counter was reset to 5
    KEY_D        // 6
}
```

Codigo de salida:

```JavaScript
"use strict";
var MIXED_C_ASSIGNMENT;
(function (MIXED_C_ASSIGNMENT) {
    MIXED_C_ASSIGNMENT[MIXED_C_ASSIGNMENT["KEY_A"] = 4] = "KEY_A";
    MIXED_C_ASSIGNMENT["KEY_B"] = "B";
    MIXED_C_ASSIGNMENT[MIXED_C_ASSIGNMENT["KEY_C"] = 5] = "KEY_C";
    MIXED_C_ASSIGNMENT[MIXED_C_ASSIGNMENT["KEY_D"] = 6] = "KEY_D";
})(MIXED_C_ASSIGNMENT || (MIXED_C_ASSIGNMENT = {}));
```

## Constante como enumeracion

Otra manera de usar un simil de enumeracion que si se traspila de forma limpia a javascript es usando constantes como enumeracion, esto 'removera' la declaracion del emum y 'quemara' en codigo las implementaciones del mismo, veamos un ejemplo de su sintaxis y la salida resultante:

Codigo TypeScript:

```TypeScript

// [const] enum MIXED_C_ASSIGNMENT { ... }
const enum MIXED_C_ASSIGNMENT {
    KEY_A = 4,
    KEY_B = "B",
    KEY_C = 5,
    KEY_D
}

console.log(MIXED_C_ASSIGNMENT.KEY_A);
console.log(MIXED_C_ASSIGNMENT.KEY_B);
console.log(MIXED_C_ASSIGNMENT.KEY_C);
console.log(MIXED_C_ASSIGNMENT.KEY_D);
```

Codigo de Salida:

```JavaScript
"use strict";
console.log(4 /* MIXED_C_ASSIGNMENT.KEY_A */);
console.log("B" /* MIXED_C_ASSIGNMENT.KEY_B */);
console.log(5 /* MIXED_C_ASSIGNMENT.KEY_C */);
console.log(6 /* MIXED_C_ASSIGNMENT.KEY_D */);
```

El uso de uno u otro dependera de las necesidades del desarrollo y que tan legible se quiere que sea el codigo de salida.
