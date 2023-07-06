# Tipado estatico en Typescript

En este curso sobre typescript vamos a aprender sobre el uso abanzado de las capacidades del lenguaje para
definir typos complejos personalizados y como usarlos para comprobar datos y "auto-ducumentar" codigo.

## Temario del curso

### Sobre el tipado estatico elemental

1. Tipos Basicos
   - Sintaxis
   - Tipos de datos primitivos
   - Definir alias (Type) personalizados
   - Definir interfaces personalizadas
   - Desestructuracion de Objetos y Arrays
2. Combinacion
   - Operador de Union
   - Operador de Interseccion
   - Componer Tipos e Interfaces
3. Cadenas literales
4. Tuplas
   - Que es una tupla
   - Implementacion en el lenguaje
5. Operador de Propagacion
   - Desestructuracion de Objetos y Arrays
   - Desempacar Objetos
   - Empacar Objetos
   - Propagador como parametro de una funcion

### Sobre el tipado estatico avanzado

1. Tipos genericons
   - Parametros en tipado dinamico
   - Condicionales
   - Acceso indexado a propiedades de tipos
   - Operador keyof
   - Operador typeof
2. Funciones Avanzadas
   - Inferencia de tipos
   - Desestructuracion de parametros y argumentos
   - Sobrecarga de funciones.
     > PD: _Always prefer parameters with union types instead of overloads when possible_
   - Funciones genericas
   - Declarar tipo para 'this'
   - ReturnType
3. Enumeraciones
   - compilar como valor constante
   - objetos como enumeraciones
4. Casting de tipos con
   - sintaxis: "as"
   - sintaxis: "<...>"
   - sintaxis "satisfies"
5. Template Literal Type
6. Mapeo de propiedades
   - hacer requerido
   - hacer opcional
   - hacer solo lectura
   - hacer mutable
   - excluir propiedades
7. Ejemplo Practico

### Sobre la Comprobacion de tipos en tiempo de ejecucion

1. typeof para comprobacion de tipos primitivos
2. instanceof para comprobacion de instancias de objetos
3. comprobacion de cadenas literales
4. sintaxis "param is Something" cara evaluacion de tipos
