# Tipos Genericos

## Parametros en tipado dinamico

Una manera de componer definiciones de tipos es a travez de parametros que pasados a una Interfaz o Alias puede hacerlas parecer una funcion a la cual al pasarles parametros estas cambian su resultado, claro que el ejemplo es figurativo pues su sintaxis difiere del caso, veamos esto en un ejemplo:

Usaremos la interfaz de `StarShip` y le agregaremos un parametro que nos permita saber de que clase es su piloto.

```Typescript

interface Identification {
  imperialCode: number;
  model: number;
  side: 'revel' | 'imperial' | 'old-republic'
}

interface Specs {
  model: string;
  hasHiperpropulsor: boolean;
  maxSpeed?: number | string;
}


interface StarShip<Pilot> extends Identification, Specs {
   pilot: Pilot;
}

```

Empezamos re-definiendo los tipos como interfaces (por buenas practicas de codigo) pues los alias son eso... alias. Ahora `StarShip` es una interfaz que recive un parametro al cual he llamado `Pilot` para agregar semantica al codigo, luego, `Pilot` es asignado internamente a la propiedad `pilot` de la misma interfaz. De esta manera es que la analogia de una funcion con parametros encaja, claro que no hay computacion de valores pero se entiende la idea general.

### Ejemplo de implementacion

```TypeScript

const MillenniumFalcon: StarShip<{ name: string }> = {
  imperialCode: 1995,
  model: 'YT-1300',
  maxSpeed: 1050,
  hasHiperpropulsor: true,
  side: 'revel',
  pilot: {
   name: 'Han Solo',
  },
}

```

El ejemplo anterior demuestra como podemos definir 'tipos en linea' como parametro a la interfaz `StarShip`; entonces `pilot` pasa a ser un objeto con una propiedad `name: string`. El inconveniente de este caso es que `Pilot` podria recivir cualquier tipo pasado como parametro de la interfaz, entonces podria ocurrir que pilot sea definido como un `number` y esto cause problemas en la aplicacion.

```TypeScript
// TODO: no se escribir vehiculo en En
interface VehiclePilot {
   id: number;
   name: string;
   cardid: string;
}

interface StarShip<Pilot extends VehiclePilot> extends Identification, Specs {
   pilot: Pilot;
}

```

En el ejemplo anterior agregamos la interfaz `VehiclePilot` que declara la estructura de datos basica para identificar a una persona que conduce algun vehiculo, en este caso una Nave. Analicemos la sintaxis `Pilot extends VehiclePilot`, es el equivalente a hace `pilot: VehiclePilot` de esta manera le indicamos al lenguaje que: cualquier parametro que satisfaga la interfaz `VehiclePilot` es valida como parametro de la interfaz; esto significa que el tipo pasado a `StarShip<...>` tendra que ser un `VehiclePilot` o cualquier otro tipo que contenga todos todas las definiciones en `VehiclePilot`; veamos esto en un ejemplo:

```TypeScript

interface StarShip<Pilot extends VehiclePilot = VehiclePilot> extends Identification, Specs {
   pilot: Pilot;
}

interface TieFighterPilot extends VehiclePilot {
   flightHours: number;
   combatUnit: number;
}

// Pilot is VehiclePilot by default
const MillenniumFalcon: StarShip = {
  // rest of code...
}

// TieFighterPilot satisfies VehiclePilot
const TieFighter: Starship<TieFighterPilot> = {
  imperialCode: 1987,
  model: 1_978_241,
  maxSpeed: '100MGLT',
  hasHiperpropulsor: false,
  side: 'imperial',
  pilot: {
   id: 1423,
   name: 'Guideon',
   cardid: 'G117',
   flightHours: 5200,
   combatUnit: 305,
  },
}

```

En el ejemplo anterior pudimos ver como al extender a `TieFighterPilot` con `VehiclePilot` fuimos capaces de satisfacer a la propiedad de la interfaz `Starship` y ademas personalizar el resultado para agregar mas campos. Otro concepto que que vemos implementado en este ejemplo es el de los 'valores' por defecto para la propiedad de una interfaz, en este caso le hemos indicado que `Pilot` de tipo `VehiclePilot` sera por defecto `VehiclePilot` y de esta manera evitamos el tener que pasar el tipo de dato al parametro cuando no sea necesario.

Los parametros de una Interfaz se comportan como un Alias por lo que puedes hacer uniones, conjunciones y demas operaciones validad sobre un Alias es aplicable al parametro de una Interfaz.

---

## Condiciones y Enums

En TypeScript podemos usar la sintaxis de condicional ternario para la definicion de tipos segun el caso que tengamos y dado que las ternarias pueden ser anidadas es sumamente potente el uso que podemos hacer de estas, para este ejemplo no usaremos anidamiento pues la logica semantica es la misma que ya conocemos y solo cambia un poco su sintaxis.

```TypeScript

type MeasurementWord<T> = T extends number ? 'metters' : 'unknown'

let distanceWord: MeasurementWord<number>; // -> 'metters'


```

En este caso el uso de `extends` es comparativo, seria algo como: _Satisface el valor dado al parametro T a un dato tipo number?_. Agamoslo mas complejo actualizando nuestra interfaz `StarShip` para que el valor de la propiedad **pilot** cambie dependiendo de si el parametro `Side` toma un valor u otro usando un **Enum** lo cual es un elemento conocido desde otros lenguajes por lo que solo exploraremos su implementacion. Veamos esto en un ejemplo al refactorizar nuestra interfaz `StarShip`:

```TypeScript
enum POWER_SIDE {
   REVEL
   IMPERIAL
   OLD_REPUBLIC
}

interface TieFighterPilot extends VehiclePilot {
   flightHours: number;
   combatUnit: number;
}

interface XWingsPilot extends VehiclePilot {
   flightHours: number;
   combatUnit: number;
   color: string;
}

interface StarShip<Side extends POWER_SIDE> extends Identification, Specs {
   pilot: Side extends POWER_SIDE.IMPERIAL ? TieFighterPilot : XWingsPilot;
   side: Side; // overwrite the 'side' property of Identification
}

const TieFighter: Starship<POWER_SIDE.IMPERIAL> = {
  imperialCode: 1987,
  model: 1_978_241,
  maxSpeed: '100MGLT',
  hasHiperpropulsor: false,
  side: 'imperial',
  pilot: {
   id: 1423,
   name: 'Guideon',
   cardid: 'G117',
   flightHours: 5200,
   combatUnit: 305,
   color: 'red', // Error: property 'color' is not in TieFighterPilot
  },
}

```

---

## Acceso indexado

Pensemos por un momento en las interfaces como si fueran objetos y entendamos el acceso a los indices como la capacidad a acceder al valor de las propiedades del objeto con la sintaxis `InterfaceOrAlias['property']` es algo que ya conocemos y es muy util aprovechar al maximo un tipo para por ejemplo definir el tipo de retorno de una funcion dependiendo de los paraetros de entrada. Veamos un ejemplo:

```TypeScript

declare function getMaxSpeed<Ship extends StarShip<any>>(ship: Ship): Ship['maxSpeed'];

getMaxSpeed(TieFighter) // output type: string
getMaxSpeed(MillenniumFalcon) // output type: number

```

En el ejemplo anterior declaramos una funcion cullo tipo de returno es el mismo tipo de datos declarado en el parametro de entrada `ship.maxSpeed` tambien tenemos una introduccion a como usar definiciones de tipos avanzados en una funcion. Analicemos la sintaxis utilizada para la definicion de tipo del parametro `ship` de la funcion declarada `getMaxSpeed`.

Primero veamos que luego del nombre de las **funciones**, **clases**, **interfaces** o **alias** dentro de un espacio delimitado por corchetes angulares: podemos definir tipos como si de parametros se tratara, se comporta igual que lo explicado anteriormente con los parametros de tipos en las interfaces y si vienes de lenguajes fuertemente tipados como **Java**, **C++** o **C#** ya estaras familiarizado con esto.

Retomemos el acceso a indices para explicar algo mas sobre como podemos usarlos para declarar propiedades adicionales de las que no conoscamos sus nombres. Manteniendo la idea que las interfaces y objetos comparten su sintaxis

```TypeScript

interface StarShip<Side extends POWER_SIDE> extends Identification, Specs {
   pilot: Side extends POWER_SIDE.IMPERIAL ? TieFighterPilot : XWingsPilot;
   side: Side; // override the 'side' property of Identification

   // acces to the indexer for define the allowed property type
   [keyName: string]: string;
}

const TieFighter: Starship<POWER_SIDE.IMPERIAL> = {
  imperialCode: 1987,
  model: 1_978_241,
  maxSpeed: '100MGLT',
  hasHiperpropulsor: false,
  side: 'imperial',
  pilot: {
   id: 1423,
   name: 'Guideon',
   cardid: 'G117',
   flightHours: 5200,
   combatUnit: 305,
   color: 'red', // Error: property 'color' is not in TieFighterPilot
  },
  nonDeclaredProperty1: "property-value as long as it is a string", // OK
  nonDeclaredProperty2: 123 // Error: property 'nonDeclaredProperty2' of type number is not allowed
}

```

En el ejemplo anterior hemos actualizado la interfaz `StarShip` para que podamos agregar mas propiedades de manera dinamica al objeto que la implemente y hemos indicado que las nuevas propiedades agregadas al objeto seran validas siempre que sea de tipo `string`, por supuesto las demas capacidades de union y conjuncion de tipos tambien estan disponibles para estas propiedades indexadas.

Para mas detalles sobre el acceso a propiedades indexadas visita la web de typescript [Aqui](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html).

---

## Operador keyof

El operador `keyof` funciona como un extractor de indices sobre tipos como interfaces o alias; en un objetos los indices son los nombres clave y en un array son indices numericos.

```Typescript
type Point = { x: number; y: number };
type P = keyof Point; // = "x" | "y"

type Arrayish = { [n: number]: unknown };
type A = keyof Arrayish; // number

type Mapish = { [k: string]: boolean };
type M = keyof Mapish; // string | number
```

---

## Operador typeof

El operador `typeof` ya forma parte del lenguaje JavaScript y TypeScript lo integro a la evaluacion de tipos sobre elementos de codigo; esto significa que podemos indicarle al lenguaje que infiera "con esmero" la declaracion de un bloque de codigo.

```TypeScript

const datetime = 05072023;

type TypeOfVAlue = datetime; // Error: 'datetime' refers to a value, but is being used as a type here
type TypeOfVAlue = typeof datetime; // Ok, is the same of 'TypeOfVAlue = number'

```

En el ejemplo anterior pudimos ver la implementacion del operador, es un ejemplo simple el cual, en este caso, no es muy util dado que es un tipo basico pero convinadolo con otras utilidades del lenguaje viene a potenciar la capacidad de inferencia de tipos ayudando al desarrollador en tareas complejas como por ejemplo migraciones de JS a TS. Veamos un ejemplo a continuacion:

```TypeScript

function f() {
  return { x: 10, y: 3 };
}

type TypeOfF = typeof f; // OK: is the same of 'TypeOfF = () => { x: number; y: number; }'

```
