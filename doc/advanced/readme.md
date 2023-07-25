# Tipado estatico avanzado

El super-poder de Typescript es la Inferencia de tipos, por esto es muy comun que no empleemos las capacidades de tipado avanzado que el lenguaje tiene para ofrecernos. En este documento que sirve como una mirada rapida a la implementacion del lenguaje aprenderemos sobre como potenciar nuestro trabajo en solitario, pero mas importante, nuestro trabajo colaborativo escribiendo buenos tipos XD.

## Indice

1. Tipos genericos
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
4. Casting de Tipos
   - sintaxis: "as"
   - sintaxis: "<...>"
   - sintaxis "satisfies"
5. Template Literal Type
6. Mapeo de Tipos
   - hacer requerido
   - hacer opcional
   - hacer solo lectura
   - hacer mutable
   - excluir propiedades
7. Ejemplo Practico
