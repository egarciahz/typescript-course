# Funciones Avanzadas

En typescript la declaracion de funciones es la misma que JavaScript respecto a sintaxis con el agregado de que puedes definir los tipos para los parametros de entrada y el tipo de dato de salida. Tal como lenguajes de alto nivel fuertemente tipados tambien podemos definir funciones avanzadas respecto al tipado estatico de los parametros de entrada y el tipo de salida lo cual nos permita reutilizar el codigo funcional para diferentes casos de uso sin tener que re-escrivir el tipado para cada caso.

## Inferencia de tipos

Typescript tiele la capacidad para, en algunos casos, inferir el tipo de dato resultante de una operacion, en el caso de las funciones esto resulta realmente util en tareas de migracionde codigo o mantenibilidad del minmo. Veamos un ejemplo en el que el tipo de retorno de una funcion es inferico por el lenguage:

```Typescript

function sum(a: number, b: number) {
    return a + b;
}

const c = sum(2, 2); // c is a number
```

En el ejemplo anterior la constante `c` ha sido 'inferida' como number de forma automatica por el lenguaje.

## Desestructuracion de parametros

El 'operador de propagacion' (spread operator) tambien funciona para desempaquetar parametros en una funcion, tambien comocido como 'resto' esta sintaxis le permite crear una funcion con una cantidad indeterminada de parametros y recivirlos dentro de la funcion como una propiedad de typo `Array<any>` unica que recoje todos los valores asignados en el contrato de la funcion. Veamos esto en un ejemplo:

```TypeScript

function sum(...values: number[]) {
    // code here
}

sum(2, 4, 6, 8)
```

En el ejemplo anterior se declarado una funcion que toma sus parametros de entrada y los retorna como una suma, vease tambien que los parametros se pasan de forma individual su acceso desde dentro de la funcion es a travez de un parametro de tipo arary.

Tambien podemos combinar un parametro 'resto' con parametros posicionales siempre y cuando el 'resto' este declarado al ultimo en el contrato de la funcion: veamos esto en un ejemplo:

```TypeScript

declare function mod(...values: number[], base: number) : number // Type Error: A rest parameter must be last in a parameter list.

declare function sum(title: string, ...values: number[]) : string

sum("The sum", 2, 2) //  output: "The sum is 4"

```

Los tipos para los parametros de 'resto' pueden ser primitivos, uniones, conjunciones o tipos personalizados; eso si, siempre se declararan como empaquetados en un array.

## Sobrecarga de funciones

La sobrecarga de funciones es algo heredado de otros lenguajes de programacion, es un procedimiento en el que declaras una funcion multiples con un contrato distinto cada vez. Veamos esto en un ejemplo:

> PD: Use funciones Genericas siempre que sea posible.

```TypeScript

function sum(...values: number[]) : number
function sum(...values: string[]) : string
function sum(...values: Array<string|number>) : number
function sum(title: string, ...values: number[]) : string

function sum(val0, ...rest): any {
    // code here
}

```

En el ejemplo anterior hemos declarat=do las sobrecargas para una funcion que demas de sumar puede concatenar strings, comvertir un string-number y sumarles, y hasta producir texto con el titulo de la suma; Note que la definicion de los parametros de la funcion esta convenientemente escrito para tomar el valor cero del contrato de la funcion e internamente descifrar lo que el usuario quiere obtener al momento de llamar a la funcion.

Aunque la sobrecarga de funciones esta disponible como una capacidad del lenguaje, y es ciertamente util, no se recomuenda su uso de manera extendida dado lo dificil que puede ser mantener un bloque de codigo de esta naturaleza, en su lugar se recomienda el uso de tippos genericon para la declaracion de funciones cullos contratos puedan tomar mas de un tipo.

## Funciones genericas

Las funciones 'genericas' son llamadas asi por la forma en que el contrato de la funcion es declaradom permitiendo reutilizar el codigo en toda la aplicacion al indicar a las funciones que tipos se quieren usar al invocarse. Veamos esto en un ejemplo con una funcion que podemos usar para sumar o concatenar dos o mas valores pasados por parametros:

```TypeScript

function sum<P extends string | number>(val0: P, ...rest: Array<number | string>): P {
    if(typeof val0 === 'string') {
        return [val0, ...rest].join(' ') as P;
    }

    return ([val0, ...rest] as any[]).reduce((a, b) => a + b) as P;
}

console.log(sum("The value", 24, 24) )// "The value is 24 24"
console.log(sum(20, "Mundo") )// "20Mundo"
```

En el ejemplo anterior podemos ver como la funcion generica `sum` retorna una especie de titulo cuando el proper parametro es un string y una suma/concatenacion cuando el primer parametro es un numero.

Nuestra funcion generica esta trabajando pero aun no hace todo lo que queremos; haremos dependiente el tipo del parametro `rest` con una condicional, este tomara un tipo u otro dependiendo de tipo asignado a `val0`.

```TypeScript

function sum<P extends string | number, R = P extends number ? number : string | number>(val0: P, ...rest: R[]): P {
    if(typeof val0 === 'string') {
        return [val0, ...rest].join(' ') as P;
    }

    return  ([val0, ...rest] as number[]).reduce((a, b) => a + b) as P;
}

```

El parametro `rest` es ahora dependiente de `val0` y el tipo de retorno de la funcion esta ligado al tipo de `val0`. Nuestra funcion `sum` ya hace lo que nesecitamos, suma cuando `val0` es numerico y concatena cuando es texto, pero nuestro codigo aun es algo tosco y al interprete le costara algo de trabajo inferir los tipos de forma correcta, para mejorar la calidad del codigo vamos a limpiar los tipos usados como parametros y usaremos alias en lugar de declararlos directamente en el contrato de la funcion:

```TypeScript
type ValidParams = string | number;
type SumResult<T> = T extends number ? number : string;
type SumArgs<T> = T extends number ? number[] : ValidParams[];

function sum<P extends ValidParams>(val0: P, ...rest: SumArgs<P>): SumResult<P> {
    if(typeof val0 === 'string') {
        return [val0, ...rest].join(' ') as SumResult<P>;
    }

    return ([val0, ...rest] as number[]).reduce((a, b) => a + b) as SumResult<P>;
}

console.log(sum("The value", 24, '4') )// "The value is 24 4"
console.log(sum(20, 90) )// 110
```

El codigo resultante es algo mas largo de lo que seria con JavaScript pero tambien es mas claro de usar, no es 'intuitivo', mas bien esta Auto-Documentado a travez del tipado estatico; el linter y el interprete guiaran al desarrollador a hacer uso correcto del contrato de la funcion para cada caso disenado y si a esto lo acompanamos de comentarios de codigo describiendo el proposito general de la funcion entonces obtenemos un bloque de codigo reusable, mantenible y sobre todo estable siendo todo esto un veneficion mayor que sopesa sobre el tiempo adicional que podria tomarnos llegar a esta solucion.

**Ejemplo final con comentarios de codigos**

````TypeScript

type ValidParams = string | number;
type SumResult<T> = T extends number ? number : string;
type SumArgs<T> = T extends number ? number[] : ValidParams[];

/**
 * @description
 * Concatenate numbers and strings or just add numbers
 *
 * @param val0 - if is a string function join all params, otherwise add al params
 * @param rest - rest of parmas
 *
 * @return returns a sum or union of parameters
 *
 * @example
 * ```TypeScript
 * console.log(sum("The value", 24, '4'))// "The value is 24 4"
 * console.log(sum(20, 90))// 110
 * ```
 * */
function sum<P extends ValidParams>(val0: P, ...rest: SumArgs<P>): SumResult<P> {
    if(typeof val0 === 'string') {
        return [val0, ...rest].join(' ') as SumResult<P>;
    }

    return ([val0, ...rest] as number[]).reduce((a, b) => a + b) as SumResult<P>;
}

````

## Declarar tipo para 'this'

La palabra reservada `this` como primer parametro del contrato de una funcion se usa para declarar el contexto interno de la funcion. Este parametro es removido del codigo de salida y de la sintaxis de llamado a la funcion. Veamos un ejemplo:

Codigo TypeScript:

```TypeScript

type Point = { x: number; y: number }

declare const nextPoint: Point;
declare const context: Point;

function invertPoint(this: Point, next: Point): void {
    this.x = next.y;
    this.y = next.x;
}

invertPoint.call(context, nextPoint)
```

Codigo de Salida:

```JavaScript
"use strict";
function invertPoint(next) {
    this.x = next.y;
    this.y = next.x;
}
invertPoint.call(context, nextPoint);
```

Esto parece ser inconveniente, o extranio pero en un patron de disenio fabrica o fachada seria de mucha utilidad conocer el contexto `this` de la funcion para consumir sus servicios.

Otro uso del contexto declarado de `this` en la definicion de metodos de clases es el poder asegurar el correcto uso de la invocacion de un metodo de una clase para preservar su contexto.

```TypeScript
class MyClass {
  name = "MyClass";
  getName(this: MyClass) {
    return this.name;
  }
}
const c = new MyClass();
// OK
c.getName();

// Error, would crash
const g = c.getName;
console.log(g());
```

En el ejemplo anterior extraido de la documentacion ofifial del lenguaje TypeScript podemos ver como la invocacion de `g()` podria fallar dado que dependiendo de la implementacion en el codigo, el scope de `c: MyClass` poria haverse perdido generando comportamientos inesperados.

> No se recomienda invocar un metodo fuera del alcance de su objeto raiz.

En una clase, la palabra reservada `this` tambien se comporta como un tipo que hace referancia a la clase misma, eso nos permite hacer uso de `this` como un elemento de tipado para parametros de metodos, propiedades de la misma clase o tipos de retorno para metodos. Veamos un ejemplo:

```TypeScript
class MyClass {
  name: string = "MyClass"
  changeName(next: string): this {
    this.name = next;
    return this
  }
  isTheSame(instance: this): boolean {
    return instance.name === this.name
  }
}

const a = new MyClass();
const b = new MyClass();

// checking a === b
console.log(a.isTheSame(b)) // true
// rename a and check
console.log(a.changeName("NewName").isTheSame(b)) // false
// rename b
console.log(b.changeName("NewName")) // MyClass { "name": "NewName" }
// checking b === a
console.log(b.isTheSame(a)) // true
```

La restriccion de esta propiedad el lengiaje es que el contexto de una clase base `class Base` y una clase extendida `class ExtendedBase extends Base` son diferentes por lo tanto el tipo inferido para `ExtendedBase` es distinto al tipo de `Base` y por lo tento no podriamos evaluar a `ExtendedBase` como un contexto de `Base`.

```TypeScript
class Base {
  name: string = "DefaultName";
  sameAs(other: this) {
    return other.name === this.name;
  }
}

class ExtendedBase extends Base {
  otherName: string = "DefaultValue";
}

const base = new Base();
const extended = new ExtendedBase();

extended.sameAs(base); // Type Error: 'Base' is not assignable to parameter of type 'ExtendedBase'
```

## ReturnType

ReturnType es una utilidad del lenguaje TypeScript y lo que hace es extraer el tipo de retorno de una funcion dada como parametro. Veamos un ejemplo:

```TypeScript
declare function add<P extends boolean>(isjoin: P, ...values: Array<number | string>): P extends true ? string : number;

type GetAddResult<isjoined extends boolean> = ReturnType<typeof add<isjoined>>

const wrapper: <P extends boolean>(p: P) => GetAddResult<P> = (p) => {
    return add(p, 1, 3, 4) // string | number
}

wrapper(true) // string
wrapper(false) // number
```
