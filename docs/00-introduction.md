---
title: Before we begin
---

This page contains detailed API reference documentation. It's intended to be a resource for people who already have some familiarity with Svelte.

If that's not you (yet), you may prefer to visit the [interactive tutorial](/tutorial) or the [examples](/examples) before consulting this reference.

Don't be shy about asking for help in the [Discord chatroom](https://svelte.dev/chat).

Using an older version of Svelte? Have a look at the [v2 docs](https://v2.svelte.dev).


Svelte no tiene virtual DOM --> Es más rápido trabajar sin DOM. Svelte tiene punteros a los nodos por lo que no necesita el DOM para actualizar los nodos.

Svelte es verdaderamente reactivo.

Svelte cambia los nodos justo en el momento en el que cambia el estado.

React/Vue hacen la mayor parte del trabajo en el navegador. Svelte lleva ese trabajo a un paso de compilación. 

Svelte actualiza quirúrgicamente el DOM cuando cambia el estado de la aplicación.

#### Las librerías y los frameworks se arrastran al navegador, como si fuera una mochila
Esto implica que:
1.- Se arrastran librerías de regalo y lo que parsear ese código.
2.- La aplicación se tiene que adaptar a la sintáxis de javascript.
3.- Los componentes se atan a versiones específicas de React, Angular... o nos ponemos a crear webs components arrastrando "mochilla" de librerías en cada uno.
4.- Los creadores de librerías tiene que medir muy bien qué características nuevas incluir --> Se añade peso.

#### Cambio de chip con Svelte
Con svelte no se lleva la librería al navegador. La aplicación se consume en tiempo de compilación y se transforma el código a javascript. En tiempo de bundling, svelte inyecta código en la aplicación y genera la aplicacióm compilada como código javascript. Y todo eso es lo que se lleva al navegador.

#### La magia de Svelte
Svelte en realidad es un compilador. Hace la mayor parte del trabajo antes de que su código se cargue en el navegador. Svelte analiza el código y lo transforma en javascript normal.

#### Ya no arrastrmos mochilla
En compilación se transforma el código y ya no lo tenemos que llevar al navegador. Ya no nos tenemos que adaptar a la sintáxis de javascript, podemos introducir azúcar sintáctico.

### Los componentes son javascript plano
Los componentes son interoperables con cualquier librería, incluso puede tirar con web components --> Podemos escribir código con svelte que termine siendo web components.

Ya los creadores de librerías no tienen que preocuparse de las características a introducir: si no se usa, no se añade peso.

#### Difing Virtual DOM
Cuando se modifica algún trozo del DOM, con React se re-renderizaba todos los componente hijos, había que hacer optimizaciones para que sólo se repintara las partes con modificaciones, react.Memo, callbacks, o virtualizar. Con svelte no se necesitan re-renders.

#### CSS
React no tiene opinión sobre cómo se definen los estilos. También existen problemas de colisiones y mantinibilidad, había que usar css modules...

Svelte sí define una forma de gestionar CSS. Las reglas css que definamos en un componente svelte, tienen como ámbito sólo ese componente, ya no hay colisiones. También se puede definir un css global, o usar SASS, o CSS in JS.


#### Código Reactivo
Svelte define el código que va a ser reactivo con:

```
$: {
    // código reactivo a los cambios. Los cambios del código que esté dentro de este bloque. Como una dependencia.
}
```

#### El closure de la muerte
En React cuando tenemos un callback, podemos estar consultando valores del pasado. Esto no sucede en svelte.
En react se usa javascript directamente. En svelte se inventa un lenguaje: #if....
React usa el contexto. En svelte se pueden usar, stores y contexto. En svelte podemos tener datos globales en nuestra aplicación de una manera sencilla.

#### Crear un proyecto svelte con REPL (Read Eval Print Loop)
Es un entorno interactico que permite modificar código y ver inmediatemente el resultado. Parecido a codesandbox
[enlace](https://svelte.dev/repl/hello-world?version=3.49.0)


#### Svelte usa vite como bundler

#### Crear un proyecto svelte con el template de javascript
```
npm create vite@latest nombreProyecto -- --template svelte

// Otra forma
npm create vite
```

### Extensiones recomendadas para svelte con IDE
1.- Svelte for Visual Code
2.- Svelte intellisense - VS Code
3.- Prettier.
4.- Svelte - JetBrains


