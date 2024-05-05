# Funciones de orden superior.

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

# Ejercicios
## Flattening

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

## Your own loop

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

## Everything

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

```