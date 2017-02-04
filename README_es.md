# es6-cheatsheet

Apuntes de repaso para ES2015 [ES6], trucos mejores practivas y codigos de
ejemplo para tu trabajo diario. Se aceptan aportes.

## Tabla de contentido

- [var, let o const](#var-versus-let--const)
- [Reemplazar IIFEs (Expresiones funcionales invocadas de manera automatica) por bloques](#replacing-iifes-with-blocks)
- [Funciones Flecha (Arrow Functions)](#arrow-functions)
- [Cadenas de texto (Strings)](#strings)
- [Destructores](#destructuring)
- [Modulos](#modules)
- [Parametros](#parameters)
- [Clases](#classes)
- [Simbolos](#symbols)
- [Mapas](#maps)
- [Objetos Clave/Valor(WeakMaps)](#weakmaps)
- [Promesas (Promises)](#promises)
- [Generadores (Generators)](#generators)
- [Async Await](#async-await)
- [Functiones de enlazamiento Getter/Setter](#getter-and-setter-functions)

## var versus let / const

> Ademas de `var`, ahora tenemos accesso a dos nuevos operadores para guardar valores
—`let` y `const`. a diferencia de `var`, `let` y `const` no son hacen (hoisting), esto significa que 
no se adieren al principio del contexto de visibilidad.

Un ejemplo del uso de `var`:

```javascript
var snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        var snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

Encambio, mira lo que sucede cuande se reemplaze `var` por `let`:

```javascript
let snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        let snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // 'Meow Mix'
```

Este cambio en el comportamiento resalta que debemos ser cuidadosos cuando se 
reescribe (refactoring) codigo antiguo que usa `var`. Reemplazar ciegamente las instancias
de `var` por `let` puede conllevar a comportamientos inesperados.

> **Note**: `let` y `const` tienen visibilidad de bloque "block-scoped" . Por tanto, hacer referencia a 
declaraciones que tienen visibilidad de bloque producira un error de tipo `ReferenceError`.

```javascript
console.log(x); // ReferenceError: x is not defined (x no se encuentra definido)

let x = 'hi';
```

> **Note**: Dejar declaraciones de variables usando `var` en codigo antiguo denota, 
que debe ser reescrito cuidadosamente. Cuando se trabaja en un nuevo codigo, usa `let`
para variables que cambian de valor a medida que pasa el tiempo, y `const` para variables que
no van ser reasignadas.

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Reemplazar IIFEs (Expresiones funcionales invocadas de manera automatica) por bloques


> Un uso comun de **Expresiones funcionales invocadas de manera automatica** es el de encapsular
valores dentro del espacio de visibilidad. En ES6, podemos crear visibilidad basada en bloques
(block-based scopes) y por ello ahora no nos vemos limitados unicamente a visiblidad 
basada en funciones.

```javascript
(function () {
    var food = 'Meow Mix';
}());

console.log(food); // Reference Error
```

Usando bloques en ES6

```javascript
{
    let food = 'Meow Mix';
};

console.log(food); // Reference Error
```

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Funciones Flecha (Arrow Functions)

Muchas veces tenemos funciones anidadas en las que deseamos preservar 
el contexto de `this` de su visibilidad lexica. Un ejemplo de ello debajo:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character; // Cannot read property 'name' of undefined - no se puede leer la propiedad 'name' de undefined
    });
};
```


Una solucion muy comun para este problema es el de guardar el contexto de `this` usando
una variable;

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    var that = this; // Guarda el contexto de `this` en `that`
    return arr.map(function (character) {
        return that.name + character;
    });
};
```

Tambien podemos pasar el contexto de `this` como parametro:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }, this); // Se envia `this` como parametro de la funcion
};
```

Tambien se puede enlazar el contexto usando la metodo `bind`:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }.bind(this)); // Enlaze del contexto
};
```
Usando **Funciones Flecha** (Arrow Functions),  el valor lexico de `this` no se 
ensombra (shadowed) y podemos reescribir lo mostrado arriba de la siguiente manera:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
};
```

> **Buena Practica**: Usar **Funciones Flecha** siempre que queramos preservar el 
valor de `this`.

Las Funciones Flecha son tambien mas concisas las expresiones de funciones que retornan un unico valor;

```javascript
var squares = arr.map(function (x) { return x * x }); // Expresion de funcion
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // Abreviacion de la expresion por medio de funciones flecha
```

> **Buena Practica**: Usar **Funciones Flecha** en lugar de expresiones de funciones siempre y
cuando sea posible.

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Cadenas de texto

Con ES6, la libreria standard ha crecido de manera considerable. Dentro de las adiciones
se encuentran nuevos metodos para el manejo de strings, como `.includes()` y `.repeat()`.

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';

console.log(string.indexOf(substring) > -1);
```

En vez de validar el contendio de un string usando el valor retornado con `> -1`,
podemos sencillament usar `.includes()` el cual retornara un boleano.

```javascript
const string = 'food';
const substring = 'foo';

console.log(string.includes(substring)); // verdadero
```

### .repeat( )

```javascript
function repeat(string, count) {
    var strings = [];
    while(strings.length < count) {
        strings.push(string);
    }
    return strings.join('');
}
```

En ES6, ahora tenemos acceso a una implementacion reducida:

```javascript
// String.repeat(numeroDeRepeticiones)
'meow'.repeat(3); // 'meowmeowmeow'
```

### Literales de Plantillas

Usando **Literales de Plantillas**, ahora podemos construir strings que tienen
caracteres especiales dentro de ellos sin necesidad de usar el caracter de 
escape ** \ **. 

```javascript
var text = "Esta cadena contiene \"comillas dobles\" usando el caracter de escape.";
```

```javascript
let text = `Esta cadena contiene "comillas dobles" sin necesidad de usar el caracter de escape`;
```

Los **Literales de Plantillas** tambien soportan interpolacion, que se encarga de
concatenar cadenas de texto (strings) y valores:

```javascript
var nombre = 'Tigre';
var edad = 13;

console.log('Mi gato se llama ' + nombre + ' y tiene ' + edad + ' años.');
```

Mucho mas simple:

```javascript
var nombre = 'Tigre';
var edad = 13;

console.log(`Mi gato se llama ${nombre} y tiene ${edad} años.`);
```
En ES5, manejabamos nuevas lineas de texto de la siguiente manera:

```javascript
var texto = (
    'gato\n' +
    'perro\n' +
    'nickelodeon'
);
```

O:

```javascript
var texto = [
    'gato',
    'perro',
    'nickelodeon'
].join('\n');
```

Los **Literales de Plantillas** preservan los saltos de lineas
sin que tengamos que colocarlos explicitamente en la cadena de texto:

```javascript
let texto = ( `gato
perro
nickelodeon`
);
```

Los **Literales de Plantillas** tambien aceptan aceptan expresiones:

```javascript
let hoy = new Date();
let texto = `El dia y la hora son: ${hoy.toLocaleString()}`;
```

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Destructuring

Destructuring allows us to extract values from arrays and objects (even deeply
nested) and store them in variables with a more convenient syntax.

### Destructuring Arrays

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

```javascript
let [a, b, c, d] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // 2
```

### Destructuring Objects

```javascript
var luke = { occupation: 'jedi', father: 'anakin' };
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

```javascript
let luke = { occupation: 'jedi', father: 'anakin' };
let {occupation, father} = luke;

console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Modules

Prior to ES6, we used libraries such as [Browserify](http://browserify.org/)
to create modules on the client-side, and [require](https://nodejs.org/api/modules.html#modules_module_require_id)
in **Node.js**. With ES6, we can now directly use modules of all types
(AMD and CommonJS).

### Exporting in CommonJS

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

### Exporting in ES6

With ES6, we have various flavors of exporting. We can perform
**Named Exports**:

```javascript
export let name = 'David';
export let age  = 25;​​
```

As well as **exporting a list** of objects:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

We can also export functions, objects and values (etc.) simply by using the `export` keyword:

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

And lastly, we can **export default bindings**:

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

let api = {
    sumTwo,
    sumThree
};

export default api;

/* Which is the same as
 * export { api as default };
 */
```

> **Best Practices**: Always use the `export default` method at **the end** of
the module. It makes it clear what is being exported, and saves time by having
to figure out what name a value was exported as. More so, the common practice
in CommonJS modules is to export a single value or object. By sticking to this
paradigm, we make our code easily readable and allow ourselves to interpolate
between CommonJS and ES6 modules.

### Importing in ES6

ES6 provides us with various flavors of importing. We can import an entire file:

```javascript
import 'underscore';
```

> It is important to note that simply **importing an entire file will execute
all code at the top level of that file**.

Similar to Python, we have named imports:

```javascript
import { sumTwo, sumThree } from 'math/addition';
```

We can also rename the named imports:

```javascript
import {
    sumTwo as addTwoNumbers,
    sumThree as sumThreeNumbers
} from 'math/addition';
```

In addition, we can **import all the things** (also called namespace import):

```javascript
import * as util from 'math/addition';
```

Lastly, we can import a list of values from a module:

```javascript
import * as additionUtil from 'math/addition';
const { sumTwo, sumThree } = additionUtil;
```
Importing from the default binding like this:

```javascript
import api from 'math/addition';
// Same as: import { default as api } from 'math/addition';
```

While it is better to keep the exports simple, but we can sometimes mix default import and mixed import if needed.
When we are exporting like this:

```javascript
// foos.js
export { foo as default, foo1, foo2 };
```

We can import them like the following:

```javascript
import foo, { foo1, foo2 } from 'foos';
```

When importing a module exported using commonjs syntax (such as React) we can do:

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

This can also be simplified further, using:

```javascript
import React, { Component, PropTypes } from 'react';
```

> **Note**: Values that are exported are **bindings**, not references.
Therefore, changing the binding of a variable in one module will affect the
value within the exported module. Avoid changing the public interface of these
exported values.

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Parameters

In ES5, we had varying ways to handle functions which needed **default values**,
**indefinite arguments**, and **named parameters**. With ES6, we can accomplish
all of this and more using more concise syntax.

### Default Parameters

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

In ES6, we can simply supply default values for parameters in a function:

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

```javascript
addTwoNumbers(2, 4); // 6
addTwoNumbers(2); // 2
addTwoNumbers(); // 0
```

### Rest Parameters

In ES5, we handled an indefinite number of arguments like so:

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

Using the **rest** operator, we can pass in an indefinite amount of arguments:

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

### Named Parameters

One of the patterns in ES5 to handle named parameters was to use the **options
object** pattern, adopted from jQuery.

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

We can achieve the same functionality using destructuring as a formal parameter
to a function:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        // Use variables height, width, lineStroke here
    }
```

If we want to make the entire value optional, we can do so by destructuring an
empty object:

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        // ...
    }
```

### Spread Operator

In ES5, we could find the max of values in an array by using the `apply` method on `Math.max` like this:
```javascript
Math.max.apply(null, [-1, 100, 9001, -32]); // 9001
```

In ES6, we can now use the spread operator to pass an array of values to be used as
parameters to a function:

```javascript
Math.max(...[-1, 100, 9001, -32]); // 9001
```

We can concat array literals easily with this intuitive syntax:

```javascript
let cities = ['San Francisco', 'Los Angeles'];
let places = ['Miami', ...cities, 'Chicago']; // ['Miami', 'San Francisco', 'Los Angeles', 'Chicago']
```

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Classes

Prior to ES6, we implemented Classes by creating a constructor function and
adding properties by extending the prototype:

```javascript
function Person(name, age, gender) {
    this.name   = name;
    this.age    = age;
    this.gender = gender;
}

Person.prototype.incrementAge = function () {
    return this.age += 1;
};
```

And created extended classes by the following:

```javascript
function Personal(name, age, gender, occupation, hobby) {
    Person.call(this, name, age, gender);
    this.occupation = occupation;
    this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.incrementAge = function () {
    Person.prototype.incrementAge.call(this);
    this.age += 20;
    console.log(this.age);
};
```

ES6 provides much needed syntactic sugar for doing this under the hood. We can
create Classes directly:

```javascript
class Person {
    constructor(name, age, gender) {
        this.name   = name;
        this.age    = age;
        this.gender = gender;
    }

    incrementAge() {
      this.age += 1;
    }
}
```

And extend them using the `extends` keyword:

```javascript
class Personal extends Person {
    constructor(name, age, gender, occupation, hobby) {
        super(name, age, gender);
        this.occupation = occupation;
        this.hobby = hobby;
    }

    incrementAge() {
        super.incrementAge();
        this.age += 20;
        console.log(this.age);
    }
}
```

> **Best Practice**: While the syntax for creating classes in ES6 obscures how
implementation and prototypes work under the hood, it is a good feature for
beginners and allows us to write cleaner code.

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Symbols

Symbols have existed prior to ES6, but now we have a public interface to using
them directly. Symbols are immutable and unique and can be used as keys in any hash.

### Symbol( )

Calling `Symbol()` or `Symbol(description)` will create a unique symbol that cannot be looked up
globally. A Use case for `Symbol()` is to patch objects or namespaces from third parties with your own
logic, but be confident that you won't collide with updates to that library. For example,
if you wanted to add a method `refreshComponent` to the `React.Component` class, and be certain that
you didn't trample a method they add in a later update:

```javascript
const refreshComponent = Symbol();

React.Component.prototype[refreshComponent] = () => {
    // do something
}
```


### Symbol.for(key)

`Symbol.for(key)` will create a Symbol that is still immutable and unique, but can be looked up globally.
Two identical calls to `Symbol.for(key)` will return the same Symbol instance. NOTE: This is not true for
`Symbol(description)`:

```javascript
Symbol('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol.for('foo') // true
```

A common use case for Symbols, and in particular with `Symbol.for(key)` is for interoperability. This can be
achieved by having your code look for a Symbol member on object arguments from third parties that contain some
known interface. For example:

```javascript
function reader(obj) {
    const specialRead = Symbol.for('specialRead');
    if (obj[specialRead]) {
        const reader = obj[specialRead]();
        // do something with reader
    } else {
        throw new TypeError('object cannot be read');
    }
}
```

And then in another library:

```javascript
const specialRead = Symbol.for('specialRead');

class SomeReadableType {
    [specialRead]() {
        const reader = createSomeReaderFrom(this);
        return reader;
    }
}
```

> A notable example of Symbol use for interoperability is `Symbol.iterator` which exists on all iterable
types in ES6: Arrays, strings, generators, etc. When called as a method it returns an object with an Iterator
interface.

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Maps

**Maps** is a much needed data structure in JavaScript. Prior to ES6, we created
**hash** maps through objects:

```javascript
var map = new Object();
map[key1] = 'value1';
map[key2] = 'value2';
```

However, this does not protect us from accidentally overriding functions with
specific property names:

```javascript
> getOwnProperty({ hasOwnProperty: 'Hah, overwritten'}, 'Pwned');
> TypeError: Property 'hasOwnProperty' is not a function
```

Actual **Maps** allow us to `set`, `get` and `search` for values (and much more).

```javascript
let map = new Map();
> map.set('name', 'david');
> map.get('name'); // david
> map.has('name'); // true
```

The most amazing part of Maps is that we are no longer limited to just using
strings. We can now use any type as a key, and it will not be type-cast to
a string.

```javascript
let map = new Map([
    ['name', 'david'],
    [true, 'false'],
    [1, 'one'],
    [{}, 'object'],
    [function () {}, 'function']
]);

for (let key of map.keys()) {
    console.log(typeof key);
    // > string, boolean, number, object, function
}
```

> **Note**: Using non-primitive values such as functions or objects won't work
when testing equality using methods such as `map.get()`. As such, stick to
primitive values such as Strings, Booleans and Numbers.

We can also iterate over maps using `.entries()`:

```javascript
for (let [key, value] of map.entries()) {
    console.log(key, value);
}
```

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## WeakMaps

In order to store private data versions < ES6, we had various ways of doing this.
One such method was using naming conventions:

```javascript
class Person {
    constructor(age) {
        this._age = age;
    }

    _incrementAge() {
        this._age += 1;
    }
}
```

But naming conventions can cause confusion in a codebase and are not always
going to be upheld. Instead, we can use WeakMaps to store our values:

```javascript
let _age = new WeakMap();
class Person {
    constructor(age) {
        _age.set(this, age);
    }

    incrementAge() {
        let age = _age.get(this) + 1;
        _age.set(this, age);
        if (age > 50) {
            console.log('Midlife crisis');
        }
    }
}
```

The cool thing about using WeakMaps to store our private data is that their
keys do not give away the property names, which can be seen by using
`Reflect.ownKeys()`:

```javascript
> const person = new Person(50);
> person.incrementAge(); // 'Midlife crisis'
> Reflect.ownKeys(person); // []
```

A more practical example of using WeakMaps is to store data which is associated
to a DOM element without having to pollute the DOM itself:

```javascript
let map = new WeakMap();
let el  = document.getElementById('someElement');

// Store a weak reference to the element with a key
map.set(el, 'reference');

// Access the value of the element
let value = map.get(el); // 'reference'

// Remove the reference
el.parentNode.removeChild(el);
el = null;

// map is empty, since the element is destroyed
```

As shown above, once the object is destroyed by the garbage collector,
the WeakMap will automatically remove the key-value pair which was identified
by that object.

> **Note**: To further illustrate the usefulness of this example, consider how
jQuery stores a cache of objects corresponding to DOM elements which have
references. Using WeakMaps, jQuery can automatically free up any memory that
was associated with a particular DOM element once it has been removed from the
document. In general, WeakMaps are very useful for any library that wraps DOM
elements.

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Promises

Promises allow us to turn our horizontal code (callback hell):

```javascript
func1(function (value1) {
    func2(value1, function (value2) {
        func3(value2, function (value3) {
            func4(value3, function (value4) {
                func5(value4, function (value5) {
                    // Do something with value 5
                });
            });
        });
    });
});
```

Into vertical code:

```javascript
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```

Prior to ES6, we used [bluebird](https://github.com/petkaantonov/bluebird) or
[Q](https://github.com/kriskowal/q). Now we have Promises natively:

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Failed to fulfill Promise')))
        .catch(reason => console.log(reason));
```

Where we have two handlers, **resolve** (a function called when the Promise is
**fulfilled**) and **reject** (a function called when the Promise is **rejected**).

> **Benefits of Promises**: Error Handling using a bunch of nested callbacks
can get chaotic. Using Promises, we have a clear path to bubbling errors up
and handling them appropriately. Moreover, the value of a Promise after it has
been resolved/rejected is immutable - it will never change.

Here is a practical example of using Promises:

```javascript
var request = require('request');

return new Promise((resolve, reject) => {
  request.get(url, (error, response, body) => {
    if (body) {
        resolve(JSON.parse(body));
      } else {
        resolve({});
      }
  });
});
```

We can also **parallelize** Promises to handle an array of asynchronous
operations by using `Promise.all()`:

```javascript
let urls = [
  '/api/commits',
  '/api/issues/opened',
  '/api/issues/assigned',
  '/api/issues/completed',
  '/api/issues/comments',
  '/api/pullrequests'
];

let promises = urls.map((url) => {
  return new Promise((resolve, reject) => {
    $.ajax({ url: url })
      .done((data) => {
        resolve(data);
      });
  });
});

Promise.all(promises)
  .then((results) => {
    // Do something with results of all our promises
 });
```

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Generators

Similar to how [Promises](https://github.com/DrkSephy/es6-cheatsheet#promises) allow us to avoid
[callback hell](http://callbackhell.com/), Generators allow us to flatten our code - giving our
asynchronous code a synchronous feel. Generators are essentially functions which we can
[pause their execution](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)
and subsequently return the value of an expression.

A simple example of using generators is shown below:

```javascript
function* sillyGenerator() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
}

var generator = sillyGenerator();
> console.log(generator.next()); // { value: 1, done: false }
> console.log(generator.next()); // { value: 2, done: false }
> console.log(generator.next()); // { value: 3, done: false }
> console.log(generator.next()); // { value: 4, done: false }
```

Where [next](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)
will allow us to push our generator forward and evaluate a new expression. While the above example is extremely
contrived, we can utilize Generators to write asynchronous code in a synchronous manner:

```javascript
// Hiding asynchronousity with Generators

function request(url) {
    getJSON(url, function(response) {
        generator.next(response);
    });
}
```

And here we write a generator function that will return our data:

```javascript
function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
}
```

By the power of `yield`, we are guaranteed that `entry1` will have the data needed to be parsed and stored
in `data1`.

While generators allow us to write asynchronous code in a synchronous manner, there is no clear
and easy path for error propagation. As such, as we can augment our generator with Promises:

```javascript
function request(url) {
    return new Promise((resolve, reject) => {
        getJSON(url, resolve);
    });
}
```

And we write a function which will step through our generator using `next` which in turn will utilize our
`request` method above to yield a Promise:

```javascript
function iterateGenerator(gen) {
    var generator = gen();
    (function iterate(val) {
        var ret = generator.next();
        if(!ret.done) {
            ret.value.then(iterate);
        }
    })();
}
```

By augmenting our Generator with Promises, we have a clear way of propagating errors through the use of our
Promise `.catch` and `reject`. To use our newly augmented Generator, it is as simple as before:

```javascript
iterateGenerator(function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
});
```

We were able to reuse our implementation to use our Generator as before, which shows their power. While Generators
and Promises allow us to write asynchronous code in a synchronous manner while retaining the ability to propagate
errors in a nice way, we can actually begin to utilize a simpler construction that provides the same benefits:
[async-await](https://github.com/DrkSephy/es6-cheatsheet#async-await).

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>

## Async Await

While this is actually an upcoming ES2016 feature, `async await` allows us to perform the same thing we accomplished
using Generators and Promises with less effort:

```javascript
var request = require('request');

function getJSON(url) {
  return new Promise(function(resolve, reject) {
    request(url, function(error, response, body) {
      resolve(body);
    });
  });
}

async function main() {
  var data = await getJSON();
  console.log(data); // NOT undefined!
}

main();
```

Under the hood, it performs similarly to Generators. I highly recommend using them over Generators + Promises. A great resource
for getting up and running with ES7 and Babel can be found [here](http://masnun.com/2015/11/11/using-es7-asyncawait-today-with-babel.html).

<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>
## Getter and setter functions

ES6 has started supporting getter and setter functions. Using the following example:

```javascript
class Employee {

    constructor(name) {
        this._name = name;
    }

    get name() {
      if(this._name) {
        return 'Mr. ' + this._name.toUpperCase();  
      } else {
        return undefined;
      }  
    }

    set name(newName) {
      if (newName == this._name) {
        console.log('I already have this name.');
      } else if (newName) {
        this._name = newName;
      } else {
        return false;
      }
    }
}

var emp = new Employee("James Bond");

// uses the get method in the background
if (emp.name) {
  console.log(emp.name);  // Mr. JAMES BOND
}

// uses the setter in the background
emp.name = "Bond 007";
console.log(emp.name);  // Mr. BOND 007  
```

Latest browsers are also supporting getter/setter functions in Objects and we can use them for computed properties, adding listeners and preprocessing before setting/getting:

```javascript
var person = {
  firstName: 'James',
  lastName: 'Bond',
  get fullName() {
      console.log('Getting FullName');
      return this.firstName + ' ' + this.lastName;
  },
  set fullName (name) {
      console.log('Setting FullName');
      var words = name.toString().split(' ');
      this.firstName = words[0] || '';
      this.lastName = words[1] || '';
  }
}

person.fullName; // James Bond
person.fullName = 'Bond 007';
person.fullName; // Bond 007
```
<sup>[(Volver a la tabla de contenidos)](#tabla-de-contenidos)</sup>
