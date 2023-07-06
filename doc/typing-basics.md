# Sobre el Tipado estatico

## Tipos Basicos

Los tipos de datos basicos o llamados primitivos en TypesCript son los siguientes:

### Datos Primitivos

- string
- number
- boolean
- object
- function
- undefined
- null

> PD: null es identificado como **object** por el evaluador _typeof_

No hay que confindirlos con los constructores de estos mismos que tienen el mismo nombre pero con camel case:

### Constructores de primitivos

- String
- Number
- Boolean
- Object
- Function

### Sintaxis para asignacion de tipos

La declaracion de tipos sobre una variable, propiedad, parametro, constante etc. esta basada en la misma estructura sintactica: `statement: type = value` y es muy parecido a las declaraciones de tipo clave valor de JSON u Objetos de JavaScript. Un ejemplo de la implementacion de estas declaraciones de tipado estatico puede verse a continuacion:

```TypeScript

 // Podemos asignar un tipo de dato a la constante
const STATIC_STRING: string = "string value";

// Aunque no este asignado el tipo, al ser una constante
// TypeScript infiere el tipo de dato como 'number'.
const STATIC_NUMBER = 33;

// La variable age es declarada de tipo 'number' pero no asignada.
let age: number;

// age es asignada con el valor 2.
age = 2;

// age no admite otro tipo de datos que no sea numerico.
age = '2'; // Sintax Error

```

### Interfaces y Alias

En Typescript es muy facil definir interface sobre todo si vienes de lenguajes fuertemente tipados como Java C o C++ entre otros. Estas interfaces tienen una estructura simple e intuitiva tal como se muestra en el siguiente ejemplo:

```TypeScript

interface Jedi {
  id: string;
  name: string;
  surName: string;
  age: number;
  isAlive: boolean;
}

interface MasterJedi extends Jedi {
  isHonest: boolean;
  address?: string;

  sayHello(): string;
}

interface PadawanJedi extends Jedi {
  masterName?: string;
}

const ObiWan: MasterJedi = { ... };
const Anakin: PadawanJedi = { ... };

```

Como puede ver en el ejemplo anterior son definidas por un bloque de llaves las cuales contienen las propiedades que se quieren declarar como parte del contrato; tambien pueden heredar (extends) a otras interfaces y definir el contrato para metodos como en el ejemplo `sayHello(): string` el cual es un metodo sin parametros que retornara un estring. Tambien vemos introducido un nuevo operador de sintaxis llamado 'opcional' con el signo de interrogacion **?** en el ejemplo `address?: string;` este operador se usa para declara como opcional la definicion de una propiedad o parametro y es el equivalente a `address?: string | undefined`.

### Alias de tipos

Un alias es una forma de acceder a un tipo primitibo o personalizado como una interfaz pero con un nombre diferente, la razon de ser de los alias es aportar sentido semantico al codigo, es decir que al momento de leer un bloque de codigo este sea capas de explicase solo mas alla del procedimiento algoritmico. Para Ejemplificar esto vamos a aumentar el ejemplo anterior de las interfaces donde la interfaz **Jedi** posee una propiedad _id_ la cual es un estring, si bien esto puede ser correcto, si quisieramos aportar mayor sentido semantico a la lectura de nuestro codigo podriamos crear un alias para _string_ donde el nombre del mismo alias nos aporte informacion sobre el formato del estring que esperamos encontrarnos ejm:

```TypeScript

type UUID = string;

interface Jedi {
  id: UUID
  /// ...
}

type Student = PadawanJedi;

const Asoka: Student = { ... };

```

Como se ve en el ejemplo anterior la sintaxis para definir tipos es basica (y aunque no se llama _alias_) es intuitiva; sigue la estructora `type AliasName = typevalue` siendo **type** una palabra reservada del lenguaje. Ahora tenemos mayor contexto sobre la composicion de un Jedi cullo id (tal vez en base de datos) es un string con el formato uuid. Los Alias tambien pueden referenciar a otros tipos de datos complejos como en el ejemplo donde un _Student_ es un alias de _PadawanJedi_.

### Combinacion

Las Interfaces pueden heredar a otras de orden superior a travez del operador _extends_ para definir contratos complejos pero en el caso los alias lo que podemos hacer es tirar unos operadores de Uniones y Conjuncione para crear alias compejos haciendo uso de mas de un tipo primitivo e incluso de interfaces u otros alias o todos ellos a la vez.

#### Union de tipos

La Union de tipos se logra a travez del simbolo `|` el cual se comporta como un operador logico **or** excluyente. Veamos un ejemplo a continuacion:

```TypeScript

type PostalAddress = number | string;

let postalAddress: PostalAddress;

postalAddress = '9624 Taylor Road'; // OK
postalAddress = 9624; // OK

postalAddress = null; // Type 'null' is not assignable to type 'PostalAddress'
postalAddress = { }  // Type '...' is not assignable to type 'PostalAddress'

```

#### Conjuncion de tipos

La Conjuncion de tipos se logra a travez del simbolo `&` el cual se comporta como un operador logico **and** incluyente. Veamos un ejemplo a continuacion:

```TypeScript

type Identification = {
  imperialCode: number;
}

type Specs = {
  model: string;
  hasHiperpropulsor: boolean;
  maxSpeed?: number | string
}

type Starship = Identification & Specs;

const MillenniumFalcon: Starship = {
  imperialCode: 1995,
  model: 'YT-1300',
  maxSpeed: '1050Km/h',
  hasHiperpropulsor: true
}

```

Como puede ver en el ejemplo anterior el tipo `Spaceship` creado a partir de la conjuncion de `Identification` y `Specs` se comporta como una nueva definicion que contiene las propiedades de ambos tipos.

#### Convergencia de tipos en operaciones de conbinacion

Al combinar tipos en un alias, TypeScript intentara hacer converger los tipos de las propiedades de cada estructura involucrada en la mezcla, veamos esto en un ejemplo:

```TypeScript

type Identification = {
  imperialCode: number;
  model: number;
}

type Specs = {
  model: string;
  hasHiperpropulsor: boolean;
  maxSpeed?: number | string;
}

type Starship = Identification & Specs;

const MillenniumFalcon: Starship = {
  imperialCode: 1995,
  model: 'YT-1300',
  maxSpeed: '1050Km/h',
  hasHiperpropulsor: true,
}

const TieFighter: Starship = {
  imperialCode: 1987,
  model: 1_978_241,
  maxSpeed: '100MGLT',
  hasHiperpropulsor: false,
}

```

En el ejemplo anterior `Identification` y `Specs` tienen una propiedad `model` la cual identifica el modelo de la nave, en este caso el interprete de TypeScript hara una union de los tipos de la propiedad `model` lo cual seria equivalente a haber definido a `model` tal que `model: string | number`.

### Cadenas literales como tipos de datos

Una 'cadena literal' es un string 'quemado' en el codigo pero no como el valor de una variable o propiedad si no como un tipo que que si bien es un string, el contrato estrictamente debe cumplir el valor literal definido. Veamos esto en un ejemplo en donde el identificador de una nave puede tomar un valor de entre una serie de opciones para saber si la nave es imperial o rebelde.

```TypeScript

type Identification = {
  imperialCode: number;
  model: number;
  side: 'revel' | 'imperial' | 'old-republic'
}

// the rest of code ...

const MillenniumFalcon: Starship = {
  imperialCode: 1995,
  model: 'YT-1300',
  maxSpeed: '1050Km/h',
  hasHiperpropulsor: true,
  side: 'revel',
}

const TieFighter: Starship = {
  imperialCode: 1987,
  model: 1_978_241,
  maxSpeed: '100MGLT',
  hasHiperpropulsor: false,
  revel: 'new-republic' // Error: Type 'new-republic' is not assignable to type 'revel' | 'imperial' | 'old-republic'
}

```

### Tuplas

JavaScript y por tanto TypeScript son lenguajes de asignacion dinamica por lo que no hacemos cosas como declarar el tamanio de un array o lacantidad total de propiedades en un objeto si no que el lenguaje mismo se encarga de reservar, asignar y liberar la memoria durante todo el ciclo de vida de la aplicacion. Las tuplas entonces podrian parecer no necesario son super utiles a la hora de definir tipos de datos comprimidos o de 'plantillas' como lo es el patron Template para JSON; las tuplas tienen una sintaxis u consumo identica al de un array con el adicional que para estas si defines la cantidad de posiciones validad y no solo eso, tambien defines los tipos de datos admitidos para cada posicion reservada. Veamos esto en un ejemplo.

```TypeScript

type Specs = {
  model: string;
  hasHiperpropulsor: boolean;
  maxSpeed?: number | string;
  dimensions: [number, number, number, string]
}

MillenniumFalcon: Starship = {
 // ...
 dimensions: [34.37, 25.61, 8.27, 'metters']
}

TieFighter: Starship = {
 // ...
 dimensions: [6.3, 4.5, 6.0, 'unknown']
}
```

En el ejemplo anterior hemos declarado la propiedad `dimensions` como una tupla de 4 posiciones, las tres primeras de tipo `number` y la cuarta y ultima como un string que intenta ser una descripccion de la unidad de medida; en el siguiente ejemplo definiremos estas unidades de medida como una unio de estring literales.

```TypeScript

type Specs = {
  model: string;
  hasHiperpropulsor: boolean;
  maxSpeed?: number | string;
  dimensions: [number, number, number, 'ft' | 'm' | 'cm']
}

MillenniumFalcon: Starship = {
 // ...
 dimensions: [34.37, 25.61, 8.27, 'm']
}

TieFighter: Starship = {
 // ...
 dimensions: [6.3, 4.5, 6.0, 'ft']
}
```

En el ejemplo anterior hemos agregado las opciones literales disponibles para usar como ultimo parameto de nuestra tupla.

### Operador de Propagacion

Una forma de explicar la propagación es con la analogía de la caja de legos, donde el objeto es la caja y los legos son las propiedades del objeto mismo; entonces podemos decir que la acción de Propagar sería como si volcáramos el contenido de la caja, algo más que podemos hacer es sacar algunos legos seleccionados sin tener que sacar todo o incluso podemos hacer ambas cosas. Veamos esto en un ejemplo:

#### Propagacion de un Array

```TypeScript

const lang = ['es', 'en', 'fr'];

console.log(...lang);// output: 'es', 'en', 'fr'

const langCopy = [ ...lang ]; // create a copy of the original array

const [esLang, enLang, frLang, undefinedPosition] = lang;

console.log(esLang); // output: 'es'
console.log(enLang); // output: 'en'
console.log(frLang); // output: 'fr'
console.log(undefinedPosition); // output: undefined

const [esLang, ...others] = lang;

console.log(esLang) // output: 'es'
console.log(others) // output: ['en', 'fr']

```

#### Propagacion de un Diccionario

```TypeScript
const sayHello = {
  hola: 'mundo',
  hello: 'world',
  bonjour: 'le monde',
};

console.log(sayHello); // output: { hola: 'mundo', hello: 'world', bonjour: 'le monde' }

const sayHelloCopy = [ ...sayHello ]; // create a copy of the original object

const { hola, hello, bonjour, undefinedFiled } = sayHello;

console.log(hola); // output: 'mundo'
console.log(hello); // output: 'world'
console.log(bonjour); // output: 'le monde'
console.log(undefinedFiled); // output: undefined

const { hola, ...others } = sayHello;

console.log(hola) // output: 'hola'
console.log(others) // output: { hello: 'world', bonjour: 'le monde' }

```
