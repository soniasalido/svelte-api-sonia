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