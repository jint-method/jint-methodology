# Implementation Guidelines

The only `<script>` tag should be your runtime, and it should be deferred. The only `<style>` or `<link rel="stylesheet">` tags should be critical CSS and other CSS should be inline, eagerly loaded, or lazy-loaded.

## Web Components

Use [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html). Custom elements are custom HTML elements where the tag name is lowercase, [kebab-case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles), and contains at least 1 hyphen.

When JavaScript is needed, upgrade a custom element into a [Web Component](https://developer.mozilla.org/en-US/docs/Web/Web_Components). Below is a diagram explaining how custom elements become web components.

![Diagram showing a web components upgrade path](/images/custom-element-to-web-component.png)

#### How do you get the web components JavaScript with only one script tag?
Lazy load the JavaScript. Use [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to watch the custom elements that need to be upgraded and when the custom element intersects with the viewport fetch the script or use the [dynamic import syntax](https://v8.dev/features/dynamic-import).

#### What if I know I'll need a web component right away?
Eager load the JavaScript. It's the same as lazy-loading except it doesn't use the Intersection Observer API.

#### What can web components communicate with?
In short, web Modules and sometimes web components. Web components can communicate with other web components if the DOM hierarchy structure is a parent/child relationship and no outside components will communicate with the component. Separated web components should **NOT** directly communicate with one another.

## Controllers

A controller is used to access a model that's maintained off the main thread. The controller can be accessed by web components and other controllers via import statements and will consist of public functions that pass information onto an OMT (off the main thread) controller (such as a web server, web worker, etc).

When communicating with a state manager the controller is responsible for informing web components of the updated state. Web components never receive information directly. Instead, they are informed of state changes by a [Custom Event](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) that is dispatched on the `document`.

## Web Modules

Web modules typically consist of a class that has at least one export. Modules can be imported using the import statement or [dynamic importing](https://v8.dev/features/dynamic-import). A web module's role is to directly manipulate the DOM. [Click here](https://examples.jintmethod.dev/dialog-maker/) for an example of a dialog modal generator.

## Stylesheets

Any stylesheet marked as eager loaded should be loaded after the `DOMContentLoaded` event has fired on the `window`. Once all eager stylesheets have loaded, the DOM state should change from hard loading to soft loading and the full-screen loading animation should be hidden/stopped. Typically, the eagerly loaded stylesheets would contain the critical CSS needed for the initial user experience.

After the DOM state changes to soft loading, all lazy-loaded stylesheets should be loaded. Once all lazy stylesheets have loaded the DOM state should change from soft loading to idling and any loading animations should be hidden/stopped.

Stylesheets hidden behind a user interaction should be lazy-loaded when the interaction occurs.

## Ideal JINT Loading Strategy

![Diagram showcasing the ideal JINT loading strategy](/images/idea-jint-loading-strategy.png)

---

### Continued Reading

- [An Introduction to JINT](https://jintmethod.dev/)
- [JINT Expanded](https://jintmethod.dev/expanded)
- [Interactive Demos](https://examples.jintmethod.dev/)