# Vainilla Javascript Notes

## Funciones Arrow

Un ejemplo de una función javascript, en su forma tradicional, que acepte un parámetro y retorne un mensaje por consola, se puede escribir de la siguiente manera

```js
function saludar (nombre) {
  return "Hola " + nombre
 }
```

La misma función, escrita en "modo flecha", quedaría del siguiente modo

```js
var saludar = nombre => "Hola " + nombre;
```

Vemos que, para este caso tan sencillo, queda definido en una sola línea con el nombre de la función como variable, el parámetro (en éste caso como tiene uno solo, no es necesario ponerlo entre paréntesis), el signo igual y el símbolo de mayor que denotarían la palabra clave `return` y luego el cuerpo de la función (que, nuevamente, como es de una sola línea, puede no estar encerrada entre llaves `{}`.

Esto se puede explicar porque, `saludar` es básicamente sólo una variable en el ámbito exterior que incluye una referencia a la función que se está declarando. Es decir, la función en sí es un valor, al igual que sería `42` o `[1,2,3]`. Esto infiere que una función en sí puede ser un valor que se asigna a variables, o se pasa a, o se devuelve a otras funciones.

Considerar:

```js
var foo = function() {
    // ..
};

var x = function bar(){
    // ..
};
```

La primera expresión de función asignada a la variable `foo` se llama anónima porque no tiene nombre.

La segunda expresión de función llamada `bar`, incluso como una referencia a ella también se asigna a la variable `x`. Las expresiones de función nombradas son generalmente más preferibles, aunque las expresiones de función anónimas todavía son extremadamente comunes.

### Immediately Invoked Function Expressions \(IIFEs\) -  Expresiones de función invocadas Inmediatamente

En el snippet anterior, ninguna de las expresiones de función se ejecutan - podríamos si hubiéramos incluido `foo()` o `x()`, por ejemplo.

Hay otra forma de ejecutar una expresión de función, que normalmente se denomina expresión de función inmediatamente invocada \(IIFE\):

```js
(function IIFE(){
    console.log( "Hello!" );
})();
// "Hello!"
```

El exterior `(..)` que rodea la expresión de función `(función IIFE () {..})` es sólo un matiz de la gramática JS necesaria para evitar que se trate como una declaración de función normal.

El final `()` al final de la expresión - el `}) ()`;  - es lo que en realidad ejecuta la expresión de función referenciada inmediatamente antes de ella.

Eso puede parecer extraño, pero no es tan extraño como a primera vista. Considere las similitudes entre `foo` y `IIFE` aquí:

```js
function foo() { .. }

// `foo` function reference expression,
// then `()` executes it
foo();

// expresión de función `IIFE`,
// entonces `()` lo ejecuta
(function IIFE(){ .. })();
```

Como se puede ver, la lista de la función `(IIFE () {..})` antes de su ejecución `()` es esencialmente el mismo que incluye `foo` antes de su ejecución `()`; En ambos casos, la referencia de función se ejecuta con `()` inmediatamente después de ella.

Debido a que un `IIFE` es sólo una función y las funciones crean un ámbito variable, el uso de un `IIFE` de esta manera se utiliza a menudo para declarar variables que no afectarán al código circundante fuera del `IIFE`:

```js
var a = 42;

(function IIFE(){
    var a = 10;
    console.log( a );    // 10
})();

console.log( a );        // 42
```

Los `IIFE` también pueden tener valores de retorno:

```js
var x = (function IIFE(){
    return 42;
})();

x;    // 42
```

El valor `42` se devuelve de la función denominada `IIFE` que se ejecuta, y luego se asigna a `x`.

### Closure

El closure es uno de los conceptos más importantes, y a menudo menos comprendidos, en JavaScript. Puede pensar en el closure como una forma de "_**recordar**_" y seguir accediendo al ámbito de una función \(y sus variables\) incluso una vez que la función ha terminado de ejecutarse.

Considerar:

```js
function makeAdder(x) {
    // El parámetro `x` es una variable interna

    // función interna `add ()` usa `x`, así que
    // tiene un "closure" sobre él
    function add(y) {
        return y + x;
    };

    return add;
}
```

La referencia a la función interna `add(..)` que se retorna con cada llamada al `makeAdder(..)` externo es capaz de recordar cualquier valor `x` que se pasó en `makeAdder(..)`. Ahora, vamos a usar `makeAdder(..)`:

```js
// `plusOne` obtiene una referencia a la función `add(..)` interna
//  con closure sobre el parámetro `x` de
// el exterior de `makeAdder(..)`
var plusOne = makeAdder( 1 );

// `plusTen` obtiene una referencia a la función `add (..)`interna
// con closure sobre el parámetro `x` de
// el exterior `makeAdder (..)`
var plusTen = makeAdder( 10 );

plusOne( 3 );        // 4  <-- 1 + 3
plusOne( 41 );        // 42 <-- 1 + 41

plusTen( 13 );        // 23 <-- 10 + 13
```

Más información sobre cómo funciona este código:

1. Cuando llamamos `makeAdder(1)`, obtenemos una referencia a su `add(..)` interna que recuerda `x` como `1`. Llamamos a esta referencia de función `plusOne(..)`.
2. Cuando llamamos `makeAdder(10)`, obtenemos otra referencia a su `add(..)` interna que recuerda `x` como `10`. Llamamos a esta referencia de función `plusTen(..)`.
3. Cuando llamamos `plusOne(3)`, añade `3` \(su `y` interno\) al `1` \(recordado por `x`\), y obtenemos `4` como el resultado.
4. Cuando llamamos `plusTen(13)`, añade `13` \(su `y` interno\) al `10` \(recordado por `x`\), y obtenemos `23` como el resultado.

Es una de las técnicas más poderosas y útiles en toda la programación. Definitivamente vale la pena el esfuerzo para dejar que su cerebro cocine a fuego lento los *closures* poco a poco.
