#  Funciones 
formas de definir funciones en js:
Definición básica:
 se realiza un *binding* / enlace entre un nombre  y una función 
```js
const square = function (x){
	return x*x;
};
//notar el punto y coma; ya que es una sola linea:
const square = function (x){return x*x;};
console.log(square(12));
// -> 144

```
## scope : alcance
	

# Funciones de orden superior.
## Concepto de abstraer -

es útil abstraer, permite reducir la posibilidad de errores, comprender mejor el codigo, y sobre todo reutilizar herramientas. Las funciones son utilies para ello, y auque así lo sean a veces se queda corto, es por eso que también se utiliza funciones de funciones: *Funciones de orden superior*

 Ejemplo: "repetir una acción 10 veces, por ejemplo imprimir 10 veces con 1  a 10"
 1) solución sin abstracción:
```js
for (let i = 0; i < 10; i++) {
  console.log(i);
}
```
2) solución con **ABSTRACCIÓN básica** : crear una funciones que haga eso:
```js
function repeatLog(n){
	for (let i = 0, i<n, i++){
		console.log(i);
	}
}
```

3) solución superior, utilizar **ABSTRACCIÓN AVANZADA** una función que repita algo n veces.
```js
function repeatSomething(n, accion){
	for (let i = 0, i< n, i++){
		acción(i);
	}
}

repeatSomething(3, console.log)
// → 0
// → 1
// → 2
```
no es necesario que `accion` sea algo predefinido, podemos definir una función en el lugar, pasarla como argumento:

```js
let arr = [];
repeatSomething(3, i => {
	arr.push(`Unit ${ i+1 }`);
})

console.log(arr);
//→ ["Unit 1", "Unit 2", "Unit 3"]
```


Son funcines que operan sobre otras funciones, es decir que sus argumentos son otras funcines y/o sus valores de retorno son funciones.

permiten abstraer no solo valores, sino acciones. Ejmplo:

```javascript

function greaterThan(n) {
  return m => m > n; //equivale a f(m){return m>n}
}

let greaterThan10 = greaterThan(10);
console.log(greaterThan10(11));
// → true
```
cambiar comportamiento de otras funciones:

```javascript
function noisy(f) {
  return (...args) => {
    console.log("calling with", args);
    let result = f(...args);
    console.log("called with", args, ", returned", result);
    return result;
  };
}
noisy(Math.min)(3, 2, 1);
// → calling with [3, 2, 1]
// → called with [3, 2, 1] , returned 1

```
notar es una funcion que toma como argumento otra funcion, en este caso toma `Math.min` y retorna una función que realiza algo antes de aplicarla.
(concepto de **patrón wrapper, o decorador**), en este caso imprime en pantalla pero podria ser cualquier otra acción.

se pueden implementar funcines que creen nuevos tipos de control de flujo:

```javascript
function amenosque(condicion, entonces){
if (!condicion) entonces()
}

repeat(3, n=> {
	amenosque(n % 2 == 1, () => { console.log(n, "es par") } )
})
// → 0 es par
// → 2 es par
```
"un numero es impar, **a menos que** al dividirlo por 2 su resto no sea = 1" notar como el 2do argumento de la funcion `repeat(veces, accion)` es una función anónima cuyo cuerpo utiliza la funcion de 2do orden `amenosque(condicion, entonces)`; donde la condición es ver si el resto de dividir por 2 resulta en 1, y `entonces` es una función anónima (sin argumentos) que imprime un mensaje en pantalla.
Javascript cuenta con funcines de 2do orden internas, por ejemplo el `forEach` para un array,:
```javascript
["A", "B", "C", "D"].forEach(l => console.log(l));
// → A
// → B
// → C
// → D
```

otro ejemplo una funcion que lee un archivo, toma su contenido y se lo pasa como argumento a una funcin `accion`
```js
function readTextFile(fileA, accion){
 //leer archivo fileA, y pasarlo a una variable.
 let contenido = String("EL contenido del archivo");
 //luego hacer la "accion" y pasarle de argumento el contenido.
 accion(contenido);
}
```
usar la funcion con una accion predefinida: `console.log`:
```js
readTextFile("nombre", console.log);
//-> "El contenido del archivo"
```

crear directamente una funcion anonima:
```js
readTextFile("nombre", (contenido) => { console.log(`hacemos otra cosa con el contenido com argumento, contenido: \n ${contenido}`) }
);
//-> hacemos otra cosa con el contenido com argumento, contenido: EL contenido del archivo
```
## Procesando conjuntos de datos.
Un ambito donde las funciones de orden superior son útiles es en el procesado de datos. Para ejemplificar usaremos como datos de ejemplo los **scripts** de Unicode.
**contexto Unicode**
cada carácter esta asociado a un script, el standart contiene 140 scripts

hay un archivo `scripts.js` que realiza el binding con una variable `SCRIPTS`, la cual contiene un array de objectos, cada uno de los cuales describen **un** `script`
```js
{
  name: "Coptic",
  ranges: [[994, 1008], [11392, 11508], [11513, 11520]],
  direction: "ltr",
  year: -200,
  living: false,
  link: "https://en.wikipedia.org/wiki/Coptic_alphabet"
}
```

### Filter
Generando un filtro genérico:
```js
function filtro(arreglo, condicion){
	let paso [];
	for (let elemento of arreglo){
		if (condicion(elemento)){ paso.push(elemento); }
	}
	return paso;
}
```

filtrar aquellos que estan activos:

```js
console.filter(SCRIPTS, script => script.living )
```
tomo como argumento el array `SCRIPTS`, y la condcion de filtrado es una función anonima, `f(script)=script.living` que evalua una de sus propiedades, (booleana)

Javascript ya cuenta con este tipo de funciones filtro, tales como `forEach, filter`  como **métodos de arrelgos**, para realizar lo anterior podría aplicarse directamente como:

```js
console.log(SCRIPTS.filter(s => s.living))
```

### Mapeando, transformaciones
Transforma cada elemento del arreglo , por lo tanto un array de entrada se convierte en otro, aplicando alguna transformación sobre cada uno de sus elementos:

```js
function mapear(arreglo, tranformación){
	let tranformado=[];
	for (let elemento of arreglo){
		transformado.push(tranformacion(elemento))
	}
	return tranformado;
}
```
Ejemplo  obtener un array con los nombres de aquellos alfabetos que se escriben de derecha a izquierda.
Solucion: primero filtrar aquellos que cumplen la condición, luego aplicar una tranformación para obtener el formato de salida deseado:
```
filtrar(se escriben de derecha a izq) -> tranformar(mostrar sus nombres)
```
```js
let derIzq_alfabetos = SCRIPTS.filter(s => s.direction=="rtl");
let salida = mapear(derIzq_alfabetos, el => el.name)
console.log(salida);
// → ["Adlam", "Arabic", "Imperial Aramaic", …]
```

### reduciendo
Aplica una tranforamción a un arreglo que produce un **unico** valor de salida. Ejemplo un promedio, una sumatoria, etc .

```js
function reducir(arreglo, combinar, inicioCon){
let actual=inicioCon;
	for (let elemento of arreglo){
		actual = combinar(actual, elemento)
	}
	return actual;
}
```
ejemplo Sumatoria:
```js
console.log(reducir([1, 2, 3, 4], (a, b) => a+b, 0)) 
/*internamente sucede:
loop1:actual= 0+1
loop2:actual= 1+2
loop3:actual= 3+3
loop4:actual= 6+4

return actual → 10
*/ 
```
El metodo standart de Javascritp para realizar esto es `reduce`, el cual tiene una caracteristica útil extra: si el array de entrada tiene al menos un elemento, se permite precidir el argumento `desde`, lo que hace que el metodo tome el primer elemento del array como `desde` y comience a reduir desde el 2do elemento.

```js
console.log([1, 2, 3, 4].reducir( (a, b) => a + b) ) 
// → 10
```

ejemplo contar la cantidad de caracteres de cada alfabeto:
solucion: crear una funcion que reduzca el array `ranges` a el valor equivalente a la cantidad de caracteres. Es decir una funcion cuyo valor de entrada sea el  objeto `script` que describe el alfabeto, y su salida sea la cantidad de caracteres.
```js
function contarCaracter(script){
	let rangos = script.ranges;
	// ranges es un arreglo de arreglos:
	// rangos= [ [ini,fin], [ini,fin], [ini,fin] ]
	let total = rangos.reduce( (acumu, elemento)=>{
						let fin = elemento[1];
						let ini = elemento[0];
						return acumu + (fin - ini))
						}, 0);
	return total;
}
```
Notar, se usa el método `reduce` el cual toma argumentos:
```js
reduce(
	function(acumulador, valorActual, índice, array ),
	, valorInicial]
)
```
podemos reescribirla mejor:
```js
function contarCaracter(script){
	let rangos = script.ranges;
	// ranges es un arreglo de arreglos:
	// rangos= [ [ini,fin], [ini,fin], [ini,fin] ]
	let total = rangos.reduce( (acumu, [ini, fin])=>{
						return acumu + (fin - ini))
						}, 0);
	return total;
}

```
Podemos usarla para obtener cual script es el que tiene el mayor cantidad de caracteres.
```js
//SCRIPTS  es un arreglo de objetos script0, script1, script2
console.log(SCRIPTS.reduce( (a, b)=>{
		//a tendra el primer valor devuelto()
		// b tendra el script 1
		let mayor = contarCaracter(a)>contarCaracter(b) ? a : b;
		return mayor;
		})   
)
```
### reconociendo  texto

Debido a que en el caso más generico, existen caracters que ocupan 2 unidades de codigo, un ejemplo son los emojis, esto puede llevar a que algunos programas "se rompan"
```js
let horseShoe = "🐴👟";
console.log(horseShoe.length);
// → 4 no 2 como uno podría esperar.
```
se requiere usar funciones utiles como `charCodeAt` y `codePointAt`:
```js
console.log(horseShoe[0]);
// → (Invalid half-character) // se rompe con los emojis
console.log(horseShoe.charCodeAt(0));
// → 55357 (Code of the half-character)
console.log(horseShoe.codePointAt(0));
// → 128052 (codigo de horse emoji)
console.log(horseShoe.codePointAt(2));
// → 128052 (codigo de horse emoji)
```
solución segura: recorrer con los `for/of` 
```js
let roseDragon = "🌹🐉";
for (let char of roseDragon) {
  console.log(char);
  console.log(roseDragon.codePointAt(0));
}
// → 🌹
// → 🐉
```


Detectar a que script (alfabeto unicode) pertenece un codigo dado. usamos el método de array `some` el cual comprueba si al menos un elemento del array cumple con la condición implementada por la función proporcionada.

```js
//characterScript(code)
function alfabetoScriptFromCode(codigo){
	for (let script of SCRIPTS){
		if(script.ranges.some( ([ini, fin]) =>{
				return code >= ini && code < fin
				} )){
		
			return script;
		}
	}
	return null;
}
```
contar cantidad de caracteres, versión abstracta:
```js
//contar cantidad de item por grupo, devuelve array de objetos
function countBy(items, groupName){
	let counts = [];
	for (let item of items){
		let name = groupName(item);
		let known = counts.find(elem => elem.name == name);
		if (!known){//create new object
			counts.push( {name, count: 1} );
		}
		else{
			known.count++; //update count
		}
	}
	return counts;
}

console.log(countBy([1, 2, 3, 4, 5], n => n > 2));
// → [{name: false, count: 2}, {name: true, count: 3}]
```
utilizando `countBy` se puede escribir una funcion que indique que scripts (afabetos )se utilizan en un texto: de tal manera que sea:
```js
console.log(textScripts('fgfgdf的狗说"woof", 俄´{}"')
// -> [ { name: 'Latin', count: 10 }, { name: 'Han', count: 4 } ]
```
se utilizan `countBy` y `characterScript`
```js
function textScripts(text) {
  let scripts = countBy(text, char => {
    let script = characterScript(char.codePointAt(0));
    return script ? script.name : "none";
  }).filter(({name}) => name != "none");
  return scripts;
```

se puede agregar unos pasoa extras para hacr uso de `reduce` y `map` , por ejemplo transformar a % la aparicion de cada alfabeto.

```js

function textScripts(text) {
  let scripts = countBy(text, char => {
    let script = characterScript(char.codePointAt(0));
    return script ? script.name : "none";
  }).filter(({name}) => name != "none");
//->scripts= [ { name: 'Latin', count: 10 }, { name: 'Han', count: 4 } ]...

//se usa reduce para sumar cada count y obtener total.
  let total = scripts.reduce((n, {count}) => n + count, 0);
  if (total == 0) return "No scripts found";
// se tranforma en varios string con el %, y luego se une con join
  return scripts.map( ({name, count}) => {
    return `${Math.round(count * 100 / total)}% ${name}`;
  }).join(", ");
}

console.log(textScripts('fgfgdf的狗说"woof", 俄´{}"'));
// → 61% Han, 22% Latin, 17% Cyrillic
```

## Ejercicios
### Flattening

Use the `reduce` method in combination with the `concat` method to “flatten” an array of arrays into a single array that has all the elements of the original arrays.
```js
let arrays = [[1, 2, 3], [4, 5], [6]];
// Your code here.
// → [1, 2, 3, 4, 5, 6]
```
solucion:
```js
function flattening(array){

let result = array.reduce( (acum, subarr) => {
	return acum.concat(subarr);
	}, []);
			 
return result;
}

```
usamos `reduce` donde la funcion recibe como función acumuladora una que concatena arrays `concat`, observar que se le pasan 2 parametros, `acum` y `subarr`, los cuales son, respectivamente,  un array vacio (ya que su valor inicial es el que se pasa cmo 3er argumento: `[]`) y un `elemento` del array . NOtar que en este caso el `elemento` tabmien es del tipo array.

### Your own loop

Write a higher-order function `loop` that provides something like a `for` loop statement. It should take a value, a test function, an update function, and a body function. Each iteration, it should first run the test function on the current loop value and stop if that returns false. It should then call the body function, giving it the current value, then finally call the update function to create a new value and start over from the beginning.
defining the function, you can use a regular loop to do the actual looping.
[link](https://eloquentjavascript.net/05_higher_order.html#c-Fv1rc97GEM)

```js
// Your code here.

loop(3, n => n > 0, n => n - 1, console.log);
// → 3
// → 2
// → 1
```
solucion:
```js
function loop(value, test, update, body){
	let current = value;
	while (test(current)){
		body(current);
		current = update(current);
	}
}

```

### Everything

[](https://eloquentjavascript.net/05_higher_order.html#p-mCva3ZA9gp)Arrays also have an `every` method analogous to the `some` method. This method returns true when the given function returns true for _every_ element in the array. In a way, `some` is a version of the `||` operator that acts on arrays, and `every` is like the `&&` operator.

[](https://eloquentjavascript.net/05_higher_order.html#p-m8iHZxZZwf)Implement `every` as a function that takes an array and a predicate function as parameters. Write two versions, one using a loop and one using the `some` method.

```js
function every(array, test) {
  // Your code here.
}

console.log(every([1, 3, 5], n => n < 10));
// → true
console.log(every([2, 4, 16], n => n < 10));
// → false
console.log(every([], n => n < 10));
// → true
```
loop version:
```js
function every(array, test) {
	for (let item of array){
		if (!test(item)) return false;		
	}
	return true;
}
```
`some` version:
leyes de morgan ` (a AND b) => -(-a OR -b) ` . 
`every = true` -> es que todos los elementos cumplen la condición `test` equivale a decir que no existe  alguno que  no la cumpla.

```js
function every(array, test) {
//si es vacio retornnar false:
if (array.length == 0) {return false};
// existe alguno que no cumpla?
let existe = array.some((elemento) => ! test(elemento));
// si existe:true entonces no se cumple el every:false
// si no exite:false entonces se cumple el every:true
return !existe;
}

```
versión comprimida:

```js
function every(array, test) {
return array.length == 0 ? false : ! array.some(item => ! test(item));
}
```
### Dominant writing direction

[](https://eloquentjavascript.net/05_higher_order.html#p-9kMfnY4I1g)Write a function that computes the dominant writing direction in a string of text. Remember that each script object has a `direction` property that can be `"ltr"` (left to right), `"rtl"` (right to left), or `"ttb"` (top to bottom).
The dominant direction is the direction of a majority of the characters that have a script associated with them. The `characterScript` and `countBy` functions defined earlier in the chapter are probably useful here.


```js
function dominantDirection(text) {
	if (text.length == 0) return null;
	// contar agrupando por nombre del atributo direction 'ltr' 'rtl' 'ttb'
	let groups = countBy(text, char => { 
		let alfabeto = characterScript(char.codePointAt(0)); //code to script
		return alfabeto ? alfabeto.direction : "none";
	// asignan "none" aquellos que no tengan un scrip asociado
	}).filter(({name}) => name != "none"); //limpia los none

  //groups →[{name: 'ltr', count: 2}, {name: 'rtl', count: 3}, {name: 'ttb', count: 5}]
	return  groups.reduce( (a, b) => {
				let mayor = a.count > b.count ? a : b;
				return mayor;
				});
}

console.log(dominantDirection("Hello!"));
// → ltr
console.log(dominantDirection("Hey, مساء الخير"));
// → rtl
console.log(dominantDirection(""));
// → null
```

#  The Secret Life of Objects

Concepto= en vez de *types* primitivos ahora se tiene *types* *objects*  como la unidad de organización del programa. 
*Object class*: es un subprograma que abstrae para su codigo interno y solo se interactua con el atra vez de metodos y propiedades.

## métodos
```js
function saludar(dice) {
  console.log(`La parsona de ${this.pais} saluda diciendo '${dice}'`);
}
let argPerson = {pais: "Argentina", saludar};
let italPerson = {pais: "Italia", saludar};

argPerson.saludar("buenas");
// → La persona de argentina saluda diciendo "buenas"'
italPerson.saludar("chiao");
// → La persona de italia saluda diciendo"chiao"'
```
`object.method()` *el binding* `this`  apunta automáticamente al objeto por el cual fue llamado.
alternativa:
`saludar.call(argPerson, "hola")`
## con función anónima
Una forma de definir un objeto con un metodo y una propiedad:

```js
//objeto finder
let finder = {
  find(array) {
    return array.some(v => v == this.value);
  },
  value: 5
};
console.log(finder.find([4, 5]));
// → true
```
OJO si se escribe el arguento de `some` usando la palabra `function` este codigo no funciona.
## Prototipos:
Todas las personas comparten el mismo metodo. `saludar`. Como crear un molde del objeto de tal manera que se le pase una pais, y un saludo, y se devuelva un objeto que mantenga la propiedad `pais` y el método `saludar`. Esto se hace con los **prototipos**
En javascript los obj puenden enlazarse a otros objetos para que magicamente *capturen* todas las propiedades que el objeto enlazado.
un **objeto plano**  se crea con `{}` y enlaza automaticamente con un objeto llamado **`Object.prototype`.**`

```js
let nada = {}; 
console.log(nada.toString); // se llamo a una propiedad
// → function toString(){…}
console.log(nada.toString()); // se llamo a un metodo
// → [object Object]
```
el objeto plano nada, capturo/heredo todas las propiedades del objeto `prototype` . cuando un objeto llama a una propiedad que no tiene, la buscara en el objeto `object.prototype`

```js
console.log(Object.getPrototypeOf({}) == Object.prototype);
// → true
console.log(Object.getPrototypeOf(Object.prototype));
// → null
```
En el codigo anterior vemos que **`Objetct.prototype`** no tiene prototipo, pero todos los objetos planos, capturan/heredan el prototipo `Object.prototype`
Así, `Functions` derivan del prototipo `function.prototype` y los `arrays` vienen del prototipo `Array.prototype`:

## creando objetos con prototipo:
Para crear un objeto con cierto prototipo se usa `Object.create`
```js
let protoPerson = {
  saludar(saludo) {
    console.log(`La persona de ${this.pais} saluda diciendo '${saludo}'`);
  }
};
let argPerson = Object.create(protoPerson);
argPerson.pais = "Argentina";
argPerson.saludar("buenas");
// → La persona de Argentina saluda diciendo 'buenas'
```
ENtonces, el prototipo Person actua como un "contenedor"  para  las propiedades que comparten todos las Personas

## el metodo COnstructor en JS
Para crear una instancia de una dada clase, se debe crear/construir un objecto que derive de cierto **prototipo** pero tambien que tenga sus valores específicos en sus **propiedades**
el constructor  sera la función:
```js
function makePerson(pais) {
  let arg = Object.create(protoPerson);
  arg.pais = pais;
  return arg;
}
```
### notación equivalnete `class`
JS cuenta con una ntoación que sigue el formato tipico de otros lenguajes POO:
```js
class Person {
  constructor(pais) {
    this.pais = pais;
  }
  saludar(saludo) {
    console.log(`The ${this.pais} rabbit says '${saludo}'`);
  }
}

// se la llama de tal:
let arg = new Person("Argentina");
```

Es importante distinguir la manera que un prototipo es **asociado** a un constructor (via propiedad) y la manera en que un objeto **tiene** un prototipo (hallado via `Object.getPrototypeOf`) El prototipo de un constructor es `Function.prototype` ya que un constructor es una función en sí. Mientras que la *property* de la función constructor mantiene el `prototype` usado para instanciar  a partir de él.

```js
console.log(Object.getPrototypeOf(Rabbit) ==
            Function.prototype); // constructor
// → true
console.log(Object.getPrototypeOf(killerRabbit) ==
            Rabbit.prototype); // prototipo de la instancia
// → true
```

### metodos privados:
se utiliza  #
```js
class SecretiveObject {
  #getSecret() {
    return "I ate all the plums";
  }
  interrogate() {
    let shallISayIt = this.#getSecret();
    return "never";
  }
}
```

## Mapas.

Pares clave valor.
```js
let ages = {
  Boris: 39,
  Liang: 22,
  Júlia: 62
};
```
Esa forma de declara hace que herede de `Object.prototype`, por lo tanto si preguntamos si 
```js
console.log("Is toString's age known?", "toString" in ages);
// → Is toString's age known? true
```
la clave `toString` si existe, una forma de solo obtener las claves del mapa y no las del `prototype` heredado es con `keys` o `hasown`
```js
console.log(Object.keys(ages));
// -> ["Boris", "Liang", "Júlia"]
```

```js
console.log(Object.hasOwn(ages, "Boris"));
// → true
console.log(Object.hasOwn(ages, "toString"));
//→ false
```

Una forma mas conveniente es utilizar una class:
```js
let ages = new Map();
ages.set("Boris", 39);
ages.set("Liang", 22);
ages.set("Júlia", 62);

console.log(`Júlia is ${ages.get("Júlia")}`);
// → Júlia is 62
console.log("Is Jack's age known?", ages.has("Jack"));
// → Is Jack's age known? false
console.log(ages.has("toString"));
// → false

```
## getters setter y statics.

Se definen como metodos se llaman como propiedades:
```js
let varyingSize = {
  get size() {
    return Math.floor(Math.random() * 100);///*
  }
};
console.log(varyingSize.size);
// → 73

//*Math.floor():devuelve el entero más grande menor o igual que un número dado.*
```

Puede ser util, para almacenar ciertas propiedades pero de manera calculada. Por ejemplo almacenamos una temperatura en celsius y creamos getters que permitan obtener en otras undades, sin necesidad de almacenar mas que su valor en celsius.

```js

class Temperature {
  constructor(celsius) {
    this.celsius = celsius;
  }
  get fahrenheit() {
    return this.celsius * 1.8 + 32;
  }
  set fahrenheit(value) {
    this.celsius = (value - 32) / 1.8;
  }
// creamos a partir de value dado en otra unidad 
  static fromFahrenheit(value) {
    return new Temperature((value - 32) / 1.8);
  }
}

let temp = new Temperature(22);
console.log(temp.fahrenheit);
// → 71.6
temp.fahrenheit = 86;
console.log(temp.celsius);
// → 30
```

## simbolos

Son una manera de definir una propiedad sin que  entre en conflicto con ninguna otra, sin importar que tenga su mismo nombre. Por lo tanto, una *interfaz* podria usar la propiedad `length` con diferentes significados para un string o un objeto que representa por ejemplo una ruta.:
```js
const longitud = Symbol("lenght");
Array.prototype[longitud] = 100;

console.log([1, 2].length);
// → 2
console.log([1, 2][length]);
// → 100
```
Notar que a diferencias de las propiedades normales, las propiedades *simbolos* se definen con metodo `Symbol` y se llaman con la notacion `[]`

```js
let viaje = {
  length: 5, // cant estaciones
  0: "Chivilcoy",
  1: "Mercedes",
  2: "Lujan",
  3: "Haedo",
  4: "Constitucion",
  [length]: 120 // distancia en km
};
console.log(`la distancia es ${viaje[length]} km, y tiene ${viaje.length} estaciones`);
// → la distancia es 120 km, y tiene 5 estaciones
```

# Programación Asincronica
La programación asincronica se trata de aquellas funciones que no retornan o resuelven en un tiempo inmediato, sino que demoran cierto tiempo y tiene la caracteristica de no detener el flujo del programa.
En la prog. asincronica se utiliza funciones que ejecutan en paralelo en su propio hilo de ejecución.

## Callback
Un enfoque de la programación asincrónica es hacer que las funciones que necesitan cierto tiempo para realizar una tarea tengan una, una _función de devolución de llamada_ mejor conocida como ***Callback***. 
Así una vez que la función principal, que logre completar su tarea, llama a la función Callback 

- Ejemplo 1: Leer un archivo (podria demorar cierto tiempo) y luego colocar su contenido en la variable: `content`
	1) Se define e implementa la función que sera utilizada de manera sincronizar (aquellas funciones que creemos que puede demorar tiempo, y no queremos retrasar la ejecución)
	2) Utilizamos la función asincronica, la llamamos (como cualqueier fucnión, con sus correspodientes parametros), y le decimos cual sera su función`accion` la cual denomidada **callback** , en este caso un simple `console.log`
	3) UTilizamos la función asincronica, ahora declaramos una función in-situ (directamente como el argumento) :
		```content => { console.log(`Shopping List:\n${content}`) }```

```js

function readTextFile(fileA, accion){
 //leer archivo fileA, y pasarlo a una variable.
 let contenido = 
 //luego hacer la "accion" y pasarle de argumento el contenido.
 accion(contenido);
}
```


```js
//1) definicion de la función asicronica
function readTextFile(fileA, accion){
 //leer archivo fileA, y pasarlo a una variable.
 let contenido = String("EL contenido del archivo");//puede demorar;
 accion(contenido);//ejecutar la "accion" pasando como arg el contenido.
}

//2) usarala caso 1: accion: funcion libreria js
readTextFile("nombre", console.log);
//-> "contenido del archivo"

//3) usarla caso 2: crear directamente una funcion anonima:
readTextFile("nombre", (contenido) => { console.log(`la lista contiene\n ${contenido}`) }
);
```


- Ejemplo 2: Una función asincrónica  compara dos archivos  que indica si su contenido es el mismo o no. Puede implementarse como una cadena de  `readTextFile` y ser algo como:

```js
//1) definirla e implementarla
function compareFiles(fileA, fileB, callback) {
  readTextFile(fileA, contentA => {
    readTextFile(fileB, contentB => {
      callback(contentA == contentB);
    });
  });
}
//2)usarla
const esigual = comparefiles("filaA", "fileB",console.log)
if (esigual)
 console.log("son iguales")
 else 
 console.log("no son iguales")
```
Explicación ejemplo anterior: la funcion utiliza previamente una funcion `readTextFile` que se encarga de colocar en `contentA` y `contentB` el contenido de los archivos `fileA fileB` respectivamente, luego llama a la función `callback` con el argumento booleano, resultante de comparar los contenidos del archivo.
1) `readTexFile` la función externa se ejecuta y obtiene  el contenido y lo vuelca en `fileA`, su función de `callback` es llamar otra vez a una funcion `readTextfile` con entrada `fileB`
2) La función interna `readText` se evalua, vuelca en `fileB` el contenido, luego su función de `callback` es llamar a la función de callback externa y pasarle como argumento un boleano de comparar `contentA` y `contentB`


**Esto es dificil de entender y desarrollar ya que se van encadenando cosas y es facil romper el codigo y cometer errores.**



##  Promesas
Otra manera de contruir programas asincronicos es tener una función asincronica que devuelva un objeto que representa su resultado futuro, en vez de pasarlo a una función de *callback*.

*Metafora, un artista avisa que publicara una nueva canción en un futuro, y que para aquellos fanes que se quierean enterar deben suscribirse a una lista, así, cuando la canción se lance todos recibiran el aviso.*
*SIn embargo, el resultado final de esta situación podria ser 2 alternativas:*
1) *El tema sale, y todos lo fans son notificados.*
2) *Ocurre algun imprevisto, la canción no sera producida por el artista, y avisará que el nuevo tema se cancela.

* *Productor de código, el cantante* :Es algo que puede llevar cierto tiempo.
- *Consumidor de código. los fanes*: Es aquello que necesita el resultado del productor de codigo .
- *==Promesa== de resultado, objeto de javascript: la lista de suscripción*:  conecta a productor y consumidor, la promeda avisara a todos los consumidores cuando el resultado se termine de obtener.
-
*Nota, OJO: la analogía no es del todo precisa, ya que las promesas son aun más versatiles que una lista de suscripción (ej: la promesa igual avisa a aquellos consumidores que se suscriban pese a que el resultado ya halla ocurrido antes, esto no sucede en una lista de suscripción, donde los fanes que se suscriban tarde ya no serán notificados)*
### Creando función asincronica usando Promesas

Entonces, en vez de *callback* se utiliza un mecanismo de *promises* .Esta promesa es un objeto que tiene el resultado futuro de una tarea asincrónica:

- una promesa representa un resultado que aun no esta disponible, pero lo estará en el futuro.
```js
let promise = new Promise(function(resolve, reject) {
  // executor (the producing code, "singer")
});
```

- El objeto promesa se crea como cualquier objeto, usando su constructor `new Promise(arg)`, su argumento sera la función ejecutora `executor`, es decir cualquier tarea que lleve determinado tiempo de realizar. En nuestra analogía sera el cantante, el productor.
- La función ejecutora tiene  2 argumentos que son provistos por Javascript (no hay que definirlas), se trata de 2 funciónes de **callback**. 
- Cuando el `executor` obtenga un resultado será llamada una y solo 1 de estas 2 **callbacks**, dependiendo de si hubo un resultado, o algo fallo:

	- `resolve(value)`: si el trabajo termina satifactoriamente el resultado  se vuelca en `value`
	- `reject(error)`: Si ocurre un error el objeto del  tipo `Error`  se vuelca en `error`

> Resumiendo: El `executor` comienza a ejecutarse automaticamente e intetara relizar una determinada tarea, una vez finaliado el intento, se llamá a `resolve` si el intento fue satifactorio, o se llama a `reeject` si ocurrio un error.

#### Objeto Promesa, estado y resultado
El objeto `Promise`  retornado por el constructor `new Promise(...` tiene 2 propiedades:
- `state`: 
	- inicialmente tiene el valor `pending` pendiente, la función `executor` esta llevando a cabo su tarea asincronicamente. Luego podra evolucionar a solo 1 de los siguientes 2 estados:
		- Puede cambiar a `fullfiled` si  se llama a `resolve` 
		- Puede cambiar a `rejected` si se llama a `reject`
- `result`: alojara el resultado final de la promesa, por lo tanto:
	- inicialmente es `undefined`, ya que la función ejecutora aun no finaliza.
	- Pasa a tener el `value` pasado a `resolve(value)` (si el estado fue `fullfilled`)
	- Pasa a tener el `error` pasado a `reject(error)` (si el estado fue `error`)
	
	
![[Pasted image 20240811214022.png]]

**Simulando una Promise:**
Usamos un `setTimeout`, que es una función predefinida en Javascript, la cual ejecuta cierta tarea luego de que corra una cantidad de milisegundos definida con su 2do argumento:
`setTimeout(<funcion a ejecutar>, <cantidad de milisedundos>, )`
Simulamos 2 promesas, que resulta en estado `fullfilled` y otra en estado `rejected`
```js
let promiseFull = new Promise(function (resolve, reject){
	//la promesa es ejecutada automaticamente una vez que es construida
	// esperar 3 segundos, y finalizar satifactoriamente:
	setTimeout(() => resolve("done"), 3000);	
});

```
- el codigo ejecutor es automaticamente ejecutado al realizar `new Promise`
- el ejecutor recibe 2 parámetros: `resolve` y `reject` (recordar: son `callbacks`)
- La promesa se definie para q resuleva satifactoriamente, por lo tanto su estado migra de `pending` -> `fullfilled`
![[Pasted image 20240811214957.png]]
Notar, en estado `pending` aun no hay resultado `result = undefined`, luego de llamar a `resolve` la propiedad `result= "done"`

```js
let promiseError = new Promise(function (resolve, reject){
	//la promesa es ejecutada automaticamente una vez que es construida
	// esperar 3 segundos, y finalizar en error:
	setTimeout(() => rejected(new Error("fallé")), 3000);	
});
```
- En este caso, la Promesa se define de tal manera que termine en estado `rejected`, por lo que en la variable `result` contendra un objeto del tipo `Error`
![[Pasted image 20240812011548.png]]

De forma mas general, a una  promesa que finalizó ya sea  en ***resolved*** o ***rejected*** se llama la llama ***settled*** , en contraste a una promesa que esta inicialmente ***pending***.

Nota:
- Las funciones argumento `reject` y `resolve`, pueden recibir a lo sumo **1 argumento**
- Si bien `reject` puede recibir cualquier argumento (como `resolve`) es recomendable que reciba un objeto del tipo `Error`
- Las propiedades `state` y `result` de una promesa son privadas, es decir, no se pueden acceder directamnete a sus valores, para ello es necesario recurrir a metodos especiales: `then()` `catch` que se ven a continuación.
### Métodos then, catch...y finally
Volviendo a la metafora/analogía. La promesa conecta  el codigo productor, "el cantante", con el consumidor, "los fans" los cuales aguardan el resultado (satifactorio o error). AHora, los fans se deben "suscribir" o registrar para recibir el rsultado, eso se hace mediante `then` y `catch`

#### then ()

```js
promise.then(
  function(result) { /* handle a successful result */ },
  function(error) { /* handle an error */ }
);
```
Then puede recibir 2 argumentos, **donde el orden es importante**
El primero será una función que se ejecuta para el caso que la promesa resuleva en `fullfilled` y
El segundo  argumento sera una función que se ejecuta si la promesa resuelve en `rejected`

Ejemplo: Promesa se resuleve satifactoriamente:
```js
let promiseOK = new Promise(function(resolve, reject) {
  setTimeout(() => resolve("resolvi satifactoriamente!"), 1000);
});

// se ejecuta solo la primer función!
promiseOK.then(
  result => alert(result), // se ejecuta esto
  error => alert(error) // no se ejecuta nunca
)
```

```js
promiseOK.then(result => alert(result))
promiseOK.then(alert) //es equivalente a lo anterior
promiseOK.then(zxy => alert(zxy)) //es equivalente a lo anterior

promiseOK.then(alert(result)) //ERROR, result no esta definido
```
Ejemplo Promesa se resuleve con error.

```js
let promiseErr = new Promise(function(resolve, reject) {
  setTimeout(() => reject(new Error ("fallé")), 1000);
});

// se ejecuta solo la 2da función!
promiseErr.then(
  result => alert(result), // no se ejecuta nunca
  error => alert(error) // Se ejecuta esto
)

```
Que pasa si estamos interesados en ejecutar algo solo cuando la promesa resuelve bien?
- pasamos  solo el primer argumento.
```js
promiseOK.then(alert); // veremos la alerta, ya que la promesa resuelve fullfilled
promiseErr.then(alert)//no la veremos, ya que la promesa resuelve en error
```

#### catch()
Es funcionalmente  igual `then(null, fun)` Es decir, si solo queremos realizar una acción cuando la promesa resuelve con error.

```js

promiseErr.catch(alert) // veremos la alerta  "Error: fallé"
promiseErr.then(null, alert) //equivalente a lo anterior.
promiseErr.catch(error => alert(error))//equival. muestra alerta  "Error: fallé".
promiseErr.catch(error => alert(error.message)) // muestra alerta  "fallé"

promiseOK.catch(alert) // no muestra la alerta.
promiseErr.then(alert)// Lanza una excepción, ya que no se "captura" la promesa que finaliza en error
```
#### finally()
Analogo al rol que se cumple en aquellas estructuras `try{...} catch{...} finally{...}`
Ahora en el contexto de promesas.
Es muy similar a `then(f, f)` es decir es una función que se ejecutará sí o sí, cuando la promesa este `settled` sin importar que sea `fullfilled` o `rejected`

Ejemplos de uso reales: cerrar una conexión, parar indicadores de carga. etc

```js
new Promise((resolve, reject) => {
  /* hacer algo que lleva tiempo, y llamar a resolve, o reject,  */
})
  // corre cuando la promesa finaliza, sin importar como finaliza
  .finally(() => alert("Promesa resuelta"))
  // manejar que ocurre segun como resuelve
  .then(result => alert(result), err => alert(err));
```
OJO:
- `finally(f)` no es exactamente un alias de `then(f, f)`, notar que `finally()` no recibe argumentos, ya que no sabemos como termina la promesa, ni tampoco nos importa.
- `finally()` no deberia retornar nada, si lo hiciera, lo que devuelve es ignorado.


#### Ejercicio 1: 
que retorna el sigueinte codigo:
```js
let promise = new Promise(function(resolve, reject) {
  resolve(1);

  setTimeout(() => resolve(2), 1000);
});

promise.then(alert);
```
Rta: la promesa se resuelve inmediatamente, y termina en estado `fullfilled` , en `result` tendra el valor `1` la alerta muestar ese numero.

#### Ejercicio2:
La funcion `setTimeout` utiliza `callback`, implementar una función equivalente utilizando `Promise` llamada `delay(ms)`

```js
function delay(ms){
// codigo
}
//uso:
delay(3000).then( () => alert('corre luego de los 3 segundos') );
// solución:

function delay(arg){
	return new Promise(function(resolve){
			setTimeOut(resolve, arg);
			});
}

delay(3000).then( () => alert('corre luego de los 3 segundos') );
//simplifcando
// no es necesario function

function delay(arg){
	return new Promise(resolve => setTimeOut(resolve, arg));
}

```

### Cadenas de Promesas

```js
new Promise(function(resolve, reject) {
  setTimeout(() => resolve(1), 1000); // (*)
  
}).then(function(result) { // (**)
  alert(result); // 1
  return result * 2;

}).then(function(result) { // (***)
  alert(result); // 2
  return result * 2;

}).then(function(result) {//(****)


  alert(result); // 4
  return result * 2;

});
```
La idea de este código es que `result` (iniciado en la `Promise`, primera) ira pasando a través de la cadena manjadores/metodos `.then`
\* La promesa se resuelve luego de 1 segundo, queda en estado `fullfilled` y en `result:1`

\*\* El primer `then` es llamado, y ejecuta la función que recibe el `result` anterior, lo muestra
con un `alert` y lo modifica duplicandolo. Entonces lo que ocurre aqui son 3 cosas:
	 - muestra el `alert` con el `result` de la Promesa origen.
	 - se duplica el valor de `result`  
	 - se retorna una nueva `Promise` YA resuelta con `result=2`
	
\*\*\* El 2do `then` es llamado ensequida (ya que la promesa anterior se resolvió inmediatamente), esta muestra el `result:2` lo duplica y devuelve una nueva Promesa, tambien ya resuelta con el `value=4`

\*\*\*\*  El 3er `then` es llamado, repite lo mismo que lo anteriores:
	- muestra el `alert` con `result:4`
	- duplica el valor de `result` dejandolo en `result: 8`
	- retorna una promesa. (nadie la usa, pero se podria seguir encadenando cosas)

Graficamente ocurre esto:
	![[Pasted image 20240812035721.png]]
Otra forma de escribr la cadena:
```js
let p1 = new Promise(resolve => { // p1 es una promesa
	setTimeout(resolve(1),1000);
})

let p2 = p1.then(result => { // p2 es una promesa a partir de p1
	alert(result);
	return result*2;
})

let p3 = p2.then(result => { // p3 es una promesa a partir de p2
	alert(result);
	return result*2;
})

let p4 = p3.then(result => { // p4 es una promesa a partir de p3
	alert(result);
	return result*2;
})

// retorna 3 alertas sucesivas 1 -> 2 -> 4

//notar p4 modifica value y  lo deja en 8, pero no es manejada, (no hay otro then)
```
#### Retorno  objeto Promise

**La clave esta en entender que el método `.then(f)` puede crear y retornar una Promesa** 
a continuación escribimos la cadea de una forma mas explicita, pero tiene el mismo efecto que la cadena anterior!, notar como se llama al constructor en cada `return` de las funciones `f` de los `.then(f)`

```js
new Promise(function(resolve, reject) {
  setTimeout(() => resolve(1), 1000);
  
}).then(function(result) {
  alert(result); // 1
  return new Promise((resolve, reject) => { 
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) { 
  alert(result); // 2
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) {
  alert(result); // 4
});

```

se hizo  lo mismo, la unica diferencia es que ahora la cadena de creación de promeesas es más explicita (por el `new Promi..`) y entre cada "eslabón"   hay un delay de 1 segundo. Y que el ultimo `then(f)` no retorna una nueva promesa.

ejemplo leer archivo asincrnico

```js
function leerArchivoAsyncVersion(name) {
  return new Promise(
    resolve => {
    leerTextoArchivo(name, text => resolve(text));
  }); // dentro de leerTextoArchivo se obtiene text
}

// usandola:
leerArchivoAsyncVersion("plans.txt").then(console.log);
```


### Ejemplo Encadenamiento `then`
Una función que lee el contenido de un archivo que elije de manera random de una lista de nombres, la cual esta dentro de un archivo. hacemos uso de la función asincronica que cramos anteriormente `leerArchivoAsyncVersion`

```js
function randomArchivo(listaArchivo) {
  return leerArchivoAsyncVersion(listaArchivo)//resuelve en 'content'
    .then(content => content.trim().split("\n"))// devuelve obj promise
    .then(ls => ls[Math.floor(Math.random() * ls.length)])// devuelve obj promise
    .then(filename => leerTextoArchivo(filename)); // promesa q resuelve devolviendo el contenido com `value`
}
```
La función retorna el resultado de una cadena de llamadas a `then` :
1. la promesa inicial obtiene la  lista de nombres de archivos como string en `content`
2. la primera `then` transforma el string en un array y produce una nueva promesa
3. la segunda `then` elije aleatoriamente un elemento del array y genera una nueva promesa
4. la tecer y ultima `then` llama a la función que lee el archivo elejido.
5. por lo anterior, la función `radomeArchivo` devuelve una promesa que resuelve su `value` con el contenido de un archivo elegido aleatoriamente.
**observación importante**
- En rigor los 2 primeros `then` si bien son promesas, se resuelven inmediatamente (no hay asincronia real) retornando  un valor (primero un array, luego un string) que es pasada a la promesa siguiente. 

- la ultima llamada `then` devuelve una promesa  `(leerTextoArchivo(filename)` la cual es el paso asincronico realmente.

- Entonces, Es posible realizar todos estos pasos dentro de una sola devolución de llamada `then`, ya que solo el último paso es realmente asíncrono.


### Falla
- Las promesas permiten manejar fácilmente la ocurrencia de excepciones/errores. en una cadena de `then` si alguna falla, se corta la serie completa.
- Así como cuando una promesa que resuelve satisfactoriamente retorna en `value`  la falla retorna tambien arroja un valor llamado *reason* .

- Así, la excepción que ocurre en alguna promesa y/o sus `then`genera una falla y el `value` de la excepción es utilizada como *reason* .
- con la función `Promise.reject`  crea una nueva promesa que es inmediatamente rechazada (falla)
## Axios

*obtenido con copilot*
### que devuelve la solicitud exitosa de axios?

Después de realizar una solicitud HTTPS satificatoria (200, 201, etc) se retorna un objeto   de clase: **response** ,   que contiene varias propiedades útiles que proporcionan información detallada sobre la respuesta del servidor. Aquí tienes una descripción de las principales propiedades:

- `data`: Contiene el cuerpo de la respuesta proporcionado por el servidor. ==Este es el dato principal que esperas recibir.==
- `status`: El código de estado HTTP de la respuesta (por ejemplo, 200 para éxito, 404 para no encontrado, etc).
- `statusText`: El mensaje de estado HTTP de la respuesta (por ejemplo, "OK" para 200).
- `headers`: Los encabezados HTTP que el servidor respondió. Todos los nombres de los encabezados están en minúsculas y se pueden acceder usando la notación de corchetes (por ejemplo, `response.headers['content-type'] `).
- `config`: La configuración que se proporcionó a Axios para la solicitud. Esto incluye la URL, los encabezados, los parámetros, etc.
- `request`: El objeto de solicitud que generó esta respuesta(no proviene del servidor) . Es una instancia de XMLHttpRequest en el navegador o una instancia de ClientRequest en Node.js.

Ejemplo de uso de cómo podrías usar estas propiedades:
```js
axios.get('https://api.example.com/data')
  .then(response => {
    console.log('Data:', response.data);
    console.log('Status:', response.status);
    console.log('Status Text:', response.statusText);
    console.log('Headers:', response.headers);
    console.log('Config:', response.config);
  })
  .catch(error => {
    console.error('Error:', error);
  });
```

En este ejemplo, se realiza una solicitud GET a una URL y se imprimen en la consola las diferentes propiedades del objeto response.

### que devuelve la solicitud fallida de axios?
Se retorna un objeto de la clase **Error** y contiene varias propiedades que te proporcionan información detallada sobre el error ocurrido durante una solicitud HTTP. Aquí tienes una descripción de las principales propiedades del objeto error:

- `response`: Si el servidor respondió con un código de estado HTTP que indica un error (por ejemplo, 404 o 500), esta propiedad contendrá el objeto `response` es la misma clase de objeto que una respuesta exitosa (`data, status, statusText, headers, config, request`).
- `message`: Un mensaje de error que describe lo que salió mal.
- `name`: El nombre del error (por ejemplo, "Error").
- `config`: La configuración que se utilizó para la solicitud que generó el error. Esto incluye la URL, los encabezados, los parámetros, etc.
- `code`: Un código de error específico (por ejemplo, "ECONNABORTED" si la solicitud fue abortada).
- `request`: El objeto de solicitud que se utilizó para generar la solicitud (no proviene del servidor). Es una instancia de XMLHttpRequest en el navegador o una instancia de ClientRequest en Node.js.

Ejemplo de uso de cómo podrías manejar un error en Axios:
```js
axios.get('https://api.example.com/data')
  .then(response => {
    console.log('Data:', response.data); //notar que es analogo que respuestas exitosas
  })
  .catch(error => {
    if (error.response) {
      // El servidor respondió con un código de estado fuera del rango 2xx
      console.log('Data:', error.response.data);
      console.log('Status:', error.response.status);
      console.log('Headers:', error.response.headers);
    } else if (error.request) {
      // La solicitud fue hecha pero no se recibió respuesta
      console.log('Request:', error.request);
    } else {
      // Algo pasó al configurar la solicitud que desencadenó un error
      console.log('Error Message:', error.message);
    }
    console.log('Config:', error.config);
  });
```

En este ejemplo:
- `error.response`: Se utiliza para manejar errores de respuesta del servidor.
- `error.request`: Se utiliza para manejar casos en los que no se recibió respuesta del servidor.
- `error.message`: Se utiliza para manejar errores de configuración o de red.




# Modulos 

Para que grandes programas sean mantibles, entendibles y organizados es necesario contar con un sistema de modoulos,

*" Un modulo es una pieza de un programa que especifica que funcionalidad  proporciona a otros mudulos (su interfa) cuales son provistas por otros modulos que él usa"*

Los modulos son muy similares a las interfaces de objetos, tienen una parte pública, disponible para utilizar, donde brindan cierta funcionalidad o servicio, y una parte privada, donde se lleva a cabo la implementación y no es accesible desde fuera.

Además un buen sistema de modulos, debe especificar qué codigo usa de otros modulos, es decir, sus **dependencias**. Un modulo A que usa un modulo B, significa que A depende de B. Así, cuando un modulo especifica correctamente sus dependencias, este puede usarse y permitir que las dependencias se encuentre automaticamente.

## Sistema de Modulos ECMAScript 2015

Antes de este sistema JS no contaba con un sistema de modulos, lo que provocaba problemas y dificultaba la reutilización de codigo, esto cambio gracias a la actualización  ECMA2015 (ES6).
Desde esta update, JS soporta 2 tipos de código:
 1) Los *script*s que se comportan como era el codigo antes de la actualización sus enclaces solo tienen un espacio global, y no hay una forma directa de referenciar otros scripts. 
 2) Los **ES Modules** donde cada uno tiene su propio alcance de dominio (*scope*) y soporta las las instruccioens **import** y **export**
 Entonces, un programa modular sera aquel que este compuesto por varios modulos conectandose entre ellos por medio de exports and imports

Ejemplo: Unmodulo que brinde la funcionalidad de convertir de numero a nombre del dia y viceversa.
```js
//file: dayname.js
//parte privada
const names = ["Sunday", "Monday", "Tuesday", "Wednesday",
               "Thursday", "Friday", "Saturday"];
               
// parte publica "interface"

// export a constant
export const STANDARD_YEAR = 2015;

// export an array
export let months = ['Jan', 'Feb', 'Mar','Apr','May', 'Jun',
					 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];


export function dayName(number){
	return names[number];
}

export function dayNumber(name){
	return names.indexOf(name);
}

export function sayHi(user) {
  alert(`Hello, ${user}!`);
}
```

La palalbra `export` puede anteponerse a una `function`, `const`, `class` o un `binding` e indica que parte sera publicas y accesibles desde otros modulos (su interface). 

### imports

Usar el modulo: existen algunas variantes para importar:
```js
//forma A
import {dayName} from '/dayname.js'
let now = new Date();
let ahora = now.getDay();
console.log (`Hoy es ${dayName(ahora)}`);

//forma B
import {dayName as diaNombre} from "./dayname.js "
let now = new Date();
let ahora = now.getDay();
console.log (`Hoy es ${diaNombre(ahora)}`);

//forma C
import * from '/dayname.js'
let now = new Date();
let ahora = now.getDay();
console.log (`Hoy es ${dayname.dayName(ahora)}`);

//forma D
import * as algo from './dayname.js';
let now = new Date();
let ahora = now.getDay();
console.log (`Hoy es ${algo.dayName(ahora)}`);
//--> Hoy es Wednesday
```

(Notar que usar el comodín para importar todo \* obliga a ser mas extenso al usar las funciones exportads, ademas que dificulta el entendimiento del codigo.)

**La interface** en sí no es mas que una colección de nombres de *binding*

## el `default` export

un tipo especial de `export` es el `default`. En este exports, solo expone UNA  entidad en la interface. es decir "se publica una sola cosa por modulo" . Además no serán necesarias las llaves `{}` cuando se haga uso de ellos:

```js
// file: say.js
export default class User {...}

//file: main.js
import User from "./say.js" //no se requieren llaves en User
```

NOTA: si bien se puede tener ambos tipos de `export` (`export` y `export default`) en un modulo, no es lo habitual no es una buena práctica.