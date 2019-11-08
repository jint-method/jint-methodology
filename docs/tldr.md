# TL;DR JINT Method

So you don't want to read the full, thoughtfully crafted, and example-rich version of the JINT method? That's fine. Here's the tldr version. If you want to read the full version it's [available here](https://jintmethod.dev).

## General Overview

The only `<script>` tag should be your runtime, and it should be deferred.

The only `<style>` or `<link rel="stylesheet">` tags should be critical CSS and other CSS should be inline, eagerly loaded, or lazy-loaded.

## Web Components

Use [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html). Custom elements are custom HTML elements where the tag name is lowercase, [kebab-case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles), and contains at least 1 hyphen.

When JavaScript is needed, upgrade a custom element into a [Web Component](https://developer.mozilla.org/en-US/docs/Web/Web_Components). Below is a diagram explaining how custom elements become web components.

![Diagram showing a web components upgrade path](/images/custom-element-to-web-component.png)

**How do you get the web components JavaScript with only one script tag?** Lazy load the JavaScript. Use [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to watch the custom elements that need to be upgraded and when the custom element intersects with the viewport fetch the script or use the [dynamic import syntax](https://v8.dev/features/dynamic-import).

**What if I know I'll need a web component right away?** Eager load the JavaScript. It's the same as lazy-loading except it doesn't use the Intersection Observer.

**What can web components communicate with?** In short, web Modules and sometimes web components. Web components can communicate with other web components if the DOM hierarchy structure is a parent/child relationship and no outside components will communicate with the component. Separated web components should **NOT** directly communicate with one another.

## Web Modules

Web modules take on several roles but are primarily used as state managers.

Web modules usually consist of a class and always have at least one export. Modules can be imported using the import statement or with [dynamic importing](https://v8.dev/features/dynamic-import). When a module is used for state management, any web components can access the module via an import statement.

State management web modules never call methods on or pass information directly to web components. Instead, they create and dispatch a [Custom Event](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) on the `document`, allowing any web component within the DOM to receive the event and react accordingly.

When not used as state managers, web modules can do anything as long as their functions, variables, or classes are exported. Other components and web modules can then import the web module as needed.

## Stylesheets

Any stylesheet marked as eager loaded should be loaded after the `DOMContentLoaded` event has fired on the `window`. Once all eager stylesheets have loaded, the DOM state should change from hard loading to soft loading and the full-screen loading animation should be hidden/stopped. Typically, the eager loaded stylesheets would contain the critical css needed for the initial user experince. 

After the DOM state changes to soft loading, all lazy-loaded stylesheets should be loaded. Once all lazy stylesheets have loaded the DOM state should change from soft loading to idling and any loading animations should be hidden/stopped.

## Ideal JINT Loading Strategy

![Diagram showcasing the ideal JINT loading strategy](/images/idea-jint-loading-strategy.png)
