---
title: Component format
---

---

Los componentes son los componentes básicos de las aplicaciones Svelte. Están escritos en archivos .svelte, utilizando un superconjunto de HTML.

Las tres secciones (script, estilos y marcado) son opcionales.
```sv
<script>
	// logic goes here
</script>

<!-- markup (zero or more items) goes here -->

<style>
	/* styles go here */
</style>
```


### &lt;script&gt;

Un bloque `<script>` contiene el código JavaScript que se ejecuta cuando se crea una instancia de componente. Las variables declaradas (o importadas) en el nivel superior son 'visibles' desde el marcado del componente. Hay cuatro reglas adicionales:


#### 0. ¿Cómo pasamos información del padre al hijo? --> PROPS
Las props tienen que ser definidas como exportables en el hijo, para que el hijo pueda recibir la prop del padre.
Podemos dar un valor por defecto a la prop en el componente hijo, por si no hubiera sido definido el valor en el componente padre.

El valor por defecto que tenga la prop en el componente hijo es sobreescrita por el valor de esa prop que tuviera en el componente padre. El padre manda.


#### 1. `export` crea una prop del componente.

---

Svelte usa la palabra clave `export` para marcar una declaración de variable como una *propiedad* o *prop*, lo que significa que se vuelve accesible para los consumidores del componente (consulte la sección [atributos y props](/docs#template-syntax-attributes-and-props) para más información).


```sv
<script>
	export let foo;

	// Values that are passed in as props
	// are immediately available
	console.log({ foo });
</script>
```

---

Puede especificar un valor inicial predeterminado para una prop. Se usará si el consumidor del componente no especifica la propiedad en el componente (o si su valor inicial es `undefined`) al instanciar el componente. Tenga en cuenta que cada vez que el consumidor elimina una propiedad, su valor se establece en `undefined` en lugar del valor inicial.

En el modo de desarrollo (consulte [compiler options](/docs#compile-time-svelte-compile)), se imprimirá una advertencia si no se proporciona un valor inicial predeterminado y el consumidor no especifica un valor. Para silenciar esta advertencia, asegúrese de especificar un valor inicial predeterminado, incluso si es `undefined`.


```sv
<script>
	export let bar = 'optional default initial value';
	export let baz = undefined;
</script>
```

---

Si exporta una `const`, `class` or `function`, será de solo lectura desde fuera del componente. Sin embargo, Function *expressions* son props válidas.

Props de sólo lectura pueden ser accedidas como propiedades en el elemento, vinculados al componente usando. [`bind:this` syntax](/docs#template-syntax-component-directives-bind-this).

```sv
<script>
	// these are readonly
	export const thisIs = 'readonly';

	export function greet(name) {
		alert(`hello ${name}!`);
	}

	// this is a prop
	export let format = n => n.toFixed(2);
</script>
```

---

Puede utilizar palabras reservadas como nombres de props.

```sv
<script>
	let className;

	// creates a `class` property, even
	// though it is a reserved word
	export { className as class };
</script>
```

#### 2. Las asignaciones son 'reactivas'

---

Svelte inyecta código que invalida el estado actual y realiza una actualización del estado por las asignaciones. Cuando se hace una asignación, svelte sabe que eso es un estado y que tiene reactividad.

Asignación = asignar un valor a una variable.

En react hay que encapsular código en bolsas (useEffect o useMemo) para que se renderice cuando se cambia un estado. En svelte usamos el bloque $:{} para definir el bloque que será reactivo a cambios.

Para cambiar el estado del componente y desencadenar un re-render, sólo tenemos que hacer una asignación en una variable declarada localmente.


Svelte vigila cuando se hace una asignación en counter para re-ejecutar el bloque de código reactivo:
```
$:{
	console.log(counter);
}
```

Si el bloque de código reactivo tiene una sola línea, se puede omitir las {}
```
$: console.log(counter);
```

##### Declaración Reactiva
No necesita declarar la variable con let double = 0;
```
let counter=0;
$: double = counter*2;
```

Si lo pasamos a bloque, entonces sí que hay que declarar la variable double, ya que en caso contrario se generaría un error: 
```
let counter=0;
let doubel=0;
S:{
	double = counter*2;
}
```


Actualizar la expresión (`count += 1`) y la asignación de la propiedad (`obj.x = y`) tiene el mismo efecto.


```sv
<script>
	let count = 0;

	function handleClick () {
		// calling this function will trigger an
		// update if the markup references `count`
		count = count + 1;
	}
</script>
```

---

La reactividad se dispara por las asignaciones. Los métodos que mutan matrices u objetos no disparan la reactividad. Una manera de solucionarlo es asignarse números a sí mismos para indicar al compilador de que hubo un cambio y que tiene que dispararse la reactividad. 

Debido a que la reactividad de Svelte se basa en asignaciones, utilizar los métodos de matriz como `.push()` y `.splice()` no activará automáticamente las actualizaciones. Se requiere una asignación posterior para activar la actualización. Este y más detalles también se pueden encontrar en [tutorial](/tutorial/updating-arrays-and-objects).

```sv
<script>
	let arr = [0, 1];

	function handleClick () {
		// this method call does not trigger an update
		arr.push(2);
		// this assignment will trigger an update
		// if the markup references `arr`
		arr = arr
	}
</script>
```

Usando el operador spread:
```
function addNumber(){
	numers = [...numbers, numbers.length+1]
}
```

##### Activar la reactividad en objetos: pop, shift, splice, map.set, set.set, etc
Para que funcione la reactividad necesitamos *obj = obj*
Regla: la variable que se actualiza debe directamente aparecer en el lado izquierdo de la asignación.


#### 3. `$:` marca una sentencia como reactiva

---

Cualquier declaración de nivel superior (es decir, que no esté dentro de un bloque o una función) puede volverse reactiva prefijándola con el `$:` [JS label syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label). Las declaraciones reactivas se ejecutan inmediatamente antes de que se actualice el componente, siempre que los valores de los que dependen hayan cambiado.

```sv
<script>
	export let title;

	// this will update `document.title` whenever
	// the `title` prop changes
	$: document.title = title;

	$: {
		console.log(`multiple statements can be combined`);
		console.log(`the current title is ${title}`);
	}
</script>
```

---

Sólo los valores que aparecen directamente en el `$:` bloque se convertirá en dependencias de la instrucción reactiva. Por ejemplo, en el siguiente código `total` se actualizará sólo cuando `x` cambie, pero no cuando cambie `y`.

```sv
<script>
	let x = 0;
	let y = 0;
	
	function yPlusAValue(value) {
		return value + y;
	}
	
	$: total = yPlusAValue(x);
</script>

Total: {total}
<button on:click={() => x++}>
	Increment X
</button>

<button on:click={() => y++}>
	Increment Y
</button>
```

---

Si una declaración consiste completamente en una asignación a una variable no declarada, Svelte inyectará una declaración `let`. No hará falta declararla con let, lo hace svelte por nosotros.

```sv
<script>
	export let num;

	// we don't need to declare `squared` and `cubed`
	// — Svelte does it for us
	$: squared = num * num;
	$: cubed = squared * num;
</script>
```

#### 4. Poner el prefijo `$` a los stores para acceder a su valor

---

Un *store* es un objeto que permite el acceso reactivo a un valor a través de un simple *contrato del store*. El [`svelte/store` module](/docs#run-time-svelte-store) contiene implementaciones mínimas del store para que cumpla ese contrato.

Cada vez que tenga una referencia a un store, podemos acceder a su valor dentro de un componente prefijándolo con el carácter `$`. Esto hace que Svelte declare la variable prefijada, se suscriba al store en la inicialización del componente y cancele la suscripción cuando corresponda.

Asignaciones a `$`-variables prefijadas requieren que la variable sea un `writable store`, y resultará en una llamada al método `.set` del store.

Tenga en cuenta que el store debe declararse en el nivel superior del componente, no dentro de un bloque `if` o una función, por ejemplo.
Las variables locales (que no representan valores almacenados) *no* deben tener un prefijo `$`.

```sv
<script>
	import { writable } from 'svelte/store';

	const count = writable(0);
	console.log($count); // logs 0

	count.set(1);
	console.log($count); // logs 1

	$count = 2;
	console.log($count); // logs 2
</script>
```

##### Contrato del Store

```js
store = { subscribe: (subscription: (value: any) => void) => (() => void), set?: (value: any) => void }
```

Puedes crear tus propios stores sin depender de [`svelte/store`](/docs#run-time-svelte-store), implementando el *contrato del store*:

1. Un store debe contener método `.subscribe`, que debe aceptar como argumento una función de suscripción. Esta función de suscripción debe llamarse de forma inmediata y sincrónica con el valor actual del store al llamar a `.subscribe`. Todas las funciones de suscripción activa de una tienda deben llamarse sincrónicamente cada vez que cambie el valor de la tienda.
2. El método `.subscribe` debe devolver una función de cancelación de suscripción. La llamada a una función de cancelación de suscripción debe detener su suscripción al store y la función de suscripción correspondiente. no debe ser volver a ser llamada por el store.
3. Un store puede *opcionalmente* contener un método `.set`, el cual debe aceptar como argumento un nuevo valor para la tienda, y que llama sincrónicamente a todas las funciones de suscripción activas de la tienda. Este tipo de almacenamiento se denomina *writable store*.

Para la interoperabilidad con RxJS Observables, el método `.subscribe` también puede devolver un objeto con un método `.unsubscribe`, en lugar de devolver la función de cancelación de suscripción directamente. Tenga en cuenta, sin embargo, que a menos que `.subscribe` llame sincrónicamente a la suscripción (which is not required by the Observable spec), Svelte verá el valor de la tienda como `undefined` hasta que lo haga.


### &lt;script context="module"&gt;

---

A `<script>` tag with a `context="module"` attribute runs once when the module first evaluates, rather than for each component instance. Values declared in this block are accessible from a regular `<script>` (and the component markup) but not vice versa.

You can `export` bindings from this block, and they will become exports of the compiled module.

You cannot `export default`, since the default export is the component itself.

> Variables defined in `module` scripts are not reactive — reassigning them will not trigger a rerender even though the variable itself will update. For values shared between multiple components, consider using a [store](/docs#run-time-svelte-store).

```sv
<script context="module">
	let totalComponents = 0;

	// this allows an importer to do e.g.
	// `import Example, { alertTotal } from './Example.svelte'`
	export function alertTotal() {
		alert(totalComponents);
	}
</script>

<script>
	totalComponents += 1;
	console.log(`total number of times this component has been created: ${totalComponents}`);
</script>
```


### &lt;style&gt;

---

CSS inside a `<style>` block will be scoped to that component.

This works by adding a class to affected elements, which is based on a hash of the component styles (e.g. `svelte-123xyz`).

```sv
<style>
	p {
		/* this will only affect <p> elements in this component */
		color: burlywood;
	}
</style>
```

---

To apply styles to a selector globally, use the `:global(...)` modifier.

```sv
<style>
	:global(body) {
		/* this will apply to <body> */
		margin: 0;
	}

	div :global(strong) {
		/* this will apply to all <strong> elements, in any
			 component, that are inside <div> elements belonging
			 to this component */
		color: goldenrod;
	}

	p:global(.red) {
		/* this will apply to all <p> elements belonging to this 
			 component with a class of red, even if class="red" does
			 not initially appear in the markup, and is instead 
			 added at runtime. This is useful when the class 
			 of the element is dynamically applied, for instance 
			 when updating the element's classList property directly. */
	}
</style>
```

---

If you want to make @keyframes that are accessible globally, you need to prepend your keyframe names with `-global-`.

The `-global-` part will be removed when compiled, and the keyframe then be referenced using just `my-animation-name` elsewhere in your code.

```html
<style>
	@keyframes -global-my-animation-name {...}
</style>
```

---

There should only be 1 top-level `<style>` tag per component.

However, it is possible to have `<style>` tag nested inside other elements or logic blocks.

In that case, the `<style>` tag will be inserted as-is into the DOM, no scoping or processing will be done on the `<style>` tag.

```html
<div>
	<style>
		/* this style tag will be inserted as-is */
		div {
			/* this will apply to all `<div>` elements in the DOM */
			color: red;
		}
	</style>
</div>
```
