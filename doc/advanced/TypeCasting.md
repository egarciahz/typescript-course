# Conversiones de tipos

## Operador: "as"

Esta es una palabra reservada del lenguaje TypeScript que le indica al interprete que debe considerar al elemento refereciado como de un tipo declarado arbitrariamente por el usuario y se considera una operacion de asignacion forsoza.

El este operador admite asercciones de tipos mas o menos espesificas sobre el tipo original del elemento referenciado. Esta regla previene acciones imposibles como la representada a continuacion:

```TypeScript
const x = "hello" as number; // Type Error: Conversion of type 'string' to type 'number' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.
```

A veces esta regla puede ser un fastidio y limitarnos al uso que podemos hacer de un objeto, por ello tenemos un truco para saltarla usando dos pasos de asercion con `any` o `unknown` en el primer paso y para el segundo paso el tipo `T` deseado.

```TypeScript
const x = (req as any) as Request; // OK
const x = (req as unknown) as Request; // OK
```

Vea el apartado **Sub-Tipos Vs Asignaciones** en la documentacion oficial del lenguaje [Aqui](https://www.typescriptlang.org/docs/handbook/type-compatibility.html#subtype-vs-assignment)

## Operador "satisfies"

El operador `satisfies` comparte sintaxis con `as` pero mientras que `as` forza la evaluacion de un tipo a otro o a un sub-tipo valido; `satisfies` hace cumplir la coalescencia del tipo del elemento referenciado con una interfaz o alias pero sin que esto implique cambiar su valor. Veamos esto con un ejemplo:

```TypeScript

type RGB = readonly [red: number, green: number, blue: number];
type Color = { value: RGB | string };

const myColorTuple: Color = { value: [20, 50, 55] }; // works
const myColorString = { value: 'red' } satisfies Color; // works
const myIncorrectColor = { value: 100 } satisfies Color; // Type 'number' is not assignable to type 'string | RGB'.

myColorTuple.value.reduce((a,b) => a + b); // Property 'reduce' does not exist on type 'string | RGB'.
myColorString.value.toUpperCase(); // works

```

El operador `satisfies` tiene como mayor beneficio la seguridad del tipo. `Satisfies` se asegura de que el elemento referenciado contenga todas las propiedades y metodos del tipo usado para la restriccion, lo cual reduce la posibilidad de comportamientos inesperados en tiempo de ejecucion pudiendo detectar problemas de tipos en tiempo de compilacion o en 'tiempo de desarrollo' con el uso del operador.

Tambien podemos combinar el operador `satisfies` con `as const` siguiendo la sintaxis: `const Element = { ... } as const satisfies T` en este caso el orden importa, es decir no podras hacer `const Element = { ... } satisfies T as const`

```TypeScript

type RGB = readonly [red: number, green: number, blue: number];
type Color = RGB | string;

const palette = {
    red: [255, 0, 0],
    green: "#00ff00",
    blue: [1,2,3],
} satisfies Record<string, Color>;

const constantPalette = {
    red: [255, 0, 0],
    green: "#00ff00",
    blue: [1,2,3],
} as const satisfies Record<string, Color>;

```

## Sintaxis "param is Type"

Tambien conocido como 'predicado de tipo' es una estructura especial del lenguaje, con una sintaxis sencilla en estructura pero potente en funcionamiento y utilidad. La sintaxis `param is Type` es usada como tipo de retorno en funciones y la mejor forma de leerle es: 'este elemento es de este tipo?' siendo el valor de retorno de la funcion un booleano primitivo o evaluado a partir del parametro, veamos esto en un ejemplo:

```TypeScript

type RGB = readonly [red: number, green: number, blue: number];
type RGBA = readonly [red: number, green: number, blue: number, alpha: number];

function isRGB(value: any): value is RGB {
    if(value?.length === 3) {
        return true
    }

    return false;
}

const color1: unknown = [25,40,55];
const color2: unknown = [25,40,55];

if (isRGB(color1)) {
    console.log('RGB ', color1); // RGB
    console.log('unknown ', color2); // unknown
}

if (isRGB(color2)) {
    console.log('rgba:', color2);
}
```
