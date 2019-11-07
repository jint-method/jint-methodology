# TL;DR JINT Method

So you don't want to read the full, thoughtfully crafted, example-rich version of the JINT method? That's fine. Here's the tldr version.

## General Overview

The only `<script>` tag should be your runtime, and it should be deferred. The only `<style>` or `<link rel="stylesheet">` tags should be critical CSS. All other CSS should be inline, eagerly loaded, or lazy-loaded.

## Web Components

Use [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html). Custom elements are custom HTML elements where the tag name is lowercase, [kebab-case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles), and contains at least 1 hyphen.

When JavaScript is needed to add functionality to a custom element, upgrade the element into a [Web Component](https://developer.mozilla.org/en-US/docs/Web/Web_Components). Below is a diagram explaining how custom elements become web components.

![Diagram showing a web components upgrade path](/images/custom-element-to-web-component.png)

*How do you get the web components JavaScript?* Lazy load it. The runtime script should use [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to watch the custom elements and when the custom element is intersecting with the viewport fetch the script or use [dynamic importing](https://v8.dev/features/dynamic-import).

*What can web components communicate with?* Web Modules, and sometimes parent/child web components.

Web components can communicate with other web components as long as the other components exist within the base component's view. Two separate web components should **not** communicate with each other directly. A web component (like a button) within another web component (like a drawer) can communicate with each other as long as no outside web component also needs to communicate with them.

## Web Modules

Web modules take on several roles but are primarily used for state management. Web modules usually consist of a class and are always exported. Modules can be imported using the import statement or with [dynamic importing](https://v8.dev/features/dynamic-import).

When a module is used for state management any web components that need to communicate with the module will access the module using an import statement. State management web modules never call methods on or pass information directly to a web component, instead, they create and dispatch a [Custom Event](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) on the `document` allowing any web component within the DOM to react accordingly.

When modules are not used as state managers they can do anything as long as their functions, variables, or classes are exported and only imported when needed by a web component or another web module.

## Stylesheets

Any stylesheet marked as eager loaded should be loaded after the `DOMContentLoaded` event has fired on the `window`. Once all eager stylesheets have loaded the DOM state should change from hard loading to soft loading and the full-screen loading animation should be hidden/stopped.

After the DOM state changes to soft loading, all lazy-loaded stylesheets should be loaded. Once all lazy stylesheets have loaded the DOM state should change from soft loading to idling and any loading animations should be hidden/stopped.

## Ideal JINT Loading Strategy

![Diagram showcasing the ideal JINT loading strategy](/images/idea-jint-loading-strategy.png)
