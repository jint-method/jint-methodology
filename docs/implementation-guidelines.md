# Implementation Guidelines

## Scripts

The only `<script>` tag should be your runtime, and it should be deferred.

## Web Components

Web Components are just [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html) with superpowers.

Custom Elements are custom HTML elements where the tag name is lowercase, [kebab-case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles), and contains at least 1 hyphen.

When JavaScript is needed, upgrade a custom element into a [Web Component](https://developer.mozilla.org/en-US/docs/Web/Web_Components). Below is a diagram explaining how custom elements become web components.

![Diagram showing a web components upgrade path](/images/custom-element-to-web-component.png)

#### How do you get the web components JavaScript with only one script tag?

Lazy load the JavaScript. Use [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to watch the custom elements that need to be upgraded and when the custom element intersects with the viewport fetch the script or use the [dynamic import syntax](https://v8.dev/features/dynamic-import).

#### What if I know I'll need a web component right away?

Eager load the JavaScript. It's the same as lazy-loading except it doesn't use the Intersection Observer API.

## Controllers

A controller is used to access a model that's maintained off the main thread. The controller can be accessed by web components and other controllers.

## Packages and Libraries

Try to limit your use of NPM packages and 3rd party libraries. Use tools like [rollup](https://rollupjs.org/guide/en/#introduction) to import the 3rd party code into your project. Don't assume 3rd party libraries are built to be imported using [ES Modules](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/) and don't assume they'll be easy to implement in your project.

## Stylesheets

The only `<style>` or `<link rel="stylesheet">` tags should be critical CSS. All other CSS should lazy-loaded.

After the `DOMContentLoaded` event has fired on the `window` all lazy-loaded stylesheets should be loaded. Once all lazy stylesheets have loaded the DOM state should change from soft loading to idling and any loading animations should be hidden/stopped.

Stylesheets hidden behind a user interaction should be lazy-loaded when the interaction occurs.

## Communication

Use a modified version of the [Actor Model](https://www.brianstorti.com/the-actor-model/) where not everything is an Actor and Actors can have more than one inbox.

Not all Web Components and Controllers are Actors.

Web Components and Controllers do not have to be an Actor to send a message.

---

### Continued Reading

- [An Introduction to JINT](https://jintmethod.dev/)
- [Interactive Demos](https://examples.jintmethod.dev/)
- [An Actor Model Messaging System Example](https://github.com/jint-method/actor-model-prototype)
