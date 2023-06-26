# Sobre el Tipado estatico

## Tipos Basicos

Los tipos de datos basicos o llamados primitivos en TypesCript son los siguientes:

### Datos Primitivos

-  string
-  number
-  boolean
-  object
-  function
-  undefined
-  null PD: null es identificado como __object__ por el evaluador _typeof_

No hay que confindirlos con los constructores de estos mismos que tienen el mismo nombre pero con camel case:

### Constructores de primitivos

-  String
-  Number
-  Boolean
-  Object
-  Function

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

const ObiWan: MasterJedi = /* ... */
const Anakin: PadawanJedi = /* ... */

```

Como puede ver en el ejemplo anterior son definidas por un bloque de llaves las cuales contienen las propiedades que se quieren declarar como parte del contrato; tambien pueden heredar (extends) a otras interfaces y definir el contrato para metodos como en el ejemplo `sayHello(): string` el cual es un metodo sin parametros que retornara un estring. Tambien vemos introducido un nuevo operador de sintaxis llamado 'opcional' con el signo de interrogacion __?__ en el ejemplo `address?: string;` este operador se usa para declara como opcional la definicion de una propiedad o parametro y es el equivalente a `address?: string | undefined`.

### Alias de tipos

Un alias es una forma de acceder a un tipo primitibo o personalizado como una interfaz pero con un nombre diferente, la razon de ser de los alias es aportar sentido semantico al codigo, es decir que al momento de leer un bloque de codigo este sea capas de explicase solo mas alla del procedimiento algoritmico. Para Ejemplificar esto vamos a aumentar el ejemplo anterior de las interfaces donde la interfaz __Jedi__ posee una propiedad _id_ la cual es un estring, si bien esto puede ser correcto, si quisieramos aportar mayor sentido semantico a la lectura de nuestro codigo podriamos crear un alias para _string_ donde el nombre del mismo alias nos aporte informacion sobre el formato del estring que esperamos encontrarnos ejm:

```TypeScript

type UUID = string;

interface Jedi {
  id: UUID
  /// ...
}

type Student = PadawanJedi;

const Asoka: Student = /* ... */;

```

Como se ve en el ejemplo anterior la sintaxis para definir tipos es basica (y aunque no se llama _alias_) es intuitiva; sigue la estructora `type AliasName = typevalue` siendo __type__ una palabra reservada del lenguaje. Ahora tenemos mayor contexto sobre la composicion de un Jedi cullo id (tal vez en base de datos) es un string con el formato uuid. Los Alias tambien pueden referenciar a otros tipos de datos complejos como en el ejemplo donde un _Student_ es un alias de _PadawanJedi_.

### Combinacion

La Interfaces pueden heredar a otras de orden superior a travez del operador _extends_ pero en el caso los alias podemos tirar de ellos para crear alias compejos haciendo Uniones y Conjunciones de mas de un tipo primitivo e incluso de interfaces u otros alias o todos ellos a la vez.

#### Union de tipos

```Typescript
```

#### Conjuncion de tipos

```Typescript
```

### Cadenas literales como tipos de datos

...

### Tuplas

...

###  Operador de Propagacion


1. Tipos Basicos
4. Combinacion
    - Operador de Union
    - Operador de Interseccion
    - Componer Tipos e Interfaces
5. Cadenas literales
4. Tuplas
    - Que es una tupla.
    - Implementacion en el lenguaje.
2. Operador de Propagacion.
    - Desestructuracion de Objetos y Arrays
    - Desempacar Objetos
    - Empacar Objetos
    - Propagador como parametro de una funcion


 
