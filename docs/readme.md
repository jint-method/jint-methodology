# JavaScript In The Nick Of Time

The JINT method is a project architecture design paradigm focused on serving resources using the Just In Time (JIT) strategy. JIT has been used in manufacturing by [Toyota](https://en.wikipedia.org/wiki/Just-in-time_manufacturing) since the 1960s and the earliest JIT compiler was created at the same time by [John McCarthy](https://en.wikipedia.org/wiki/Just-in-time_compilation) while he worked on the [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) programming language. JIT compiling is currently utilized by Google's [v8 JavaScript Engine](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) which in turn is used by Google Chrome, Chromium, Brave, and Opera browsers along with Electron applications and Nodejs.

The purpose of JINT is to provide a methodology for efficiently loading, parsing, and running web sites & applications on devices ranging from the newest iPhone to the government-subsidized Nokia 2 on a 2g network. JINT is not a framework, it's a concept. You, the developer, need to think through you specific problem and provide the solution that works well for you and your team.

## Why This Matters

Before diving in we'll cover some of the benefits.

JINT forces you to carefully consider how your product will load its resources. By devising an efficient strategy you can directly impact your product's SEO rankings ([according to Google, search and ads are (partially) ranked based on speed](https://developers.google.com/web/updates/2018/07/search-ads-speed)). Managing load times can also help with retaining users. According to [this study]((https://www.imperva.com/blog/ecommerce-study/)) 62% of users will wait 5 seconds or less for a page to load before leaving. According to Google, [53% of mobile users will leave after 3 seconds](https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/). Studies also show that when users see a skeleton screen (when compared to spinners or blank screens) they [precieve the load time to be shorter](https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a).

Whether you're an in house team focused on enhancing your user's experience or an agency helping clients market themselves, there are some solid reasons to consider shaping the way your product loads and performs.

## Required Knowledge

Please consider familiarizing yourself using the following links:

- [ES6 Overview](https://ponyfoo.com/articles/es6)
- [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html)
- [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [ES Modules](https://v8.dev/features/modules)
- [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)
- [Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)
- [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)
- [Actor Model](https://en.wikipedia.org/wiki/Actor_model)

## Attribution

JINT was constructed using ideas from the following videos and articles:

- [RAIL Model](https://developers.google.com/web/fundamentals/performance/rail)
- [App Shell Model](https://developers.google.com/web/fundamentals/architecture/app-shell)
- [Off main-thread state management](https://dassur.ma/things/react-redux-comlink/)
- [Main thread is overworked](https://youtu.be/7Rrv9qFMWNM)
- [Architecting web applications](https://youtu.be/Vg60lf92EkM)
- [Adaptive loading](https://youtu.be/puUPpVrIRkc)
- [Speed essentials](https://youtu.be/reztLS3vomE)

## Overview

Let's begin with clearly stating that **this is not a framework**, with JINT you can use any framework. Start by selecting a server-side rendering engine, then a client-side rendering engine. Use both rendering engines together to render content based on the context, desired functionality, network conditions, and the user's hardware.

You could use [React](https://reactjs.org/), [Angular](https://angular.io/), [Vue](https://vuejs.org/), or any other popular front-end frameworks, however, Webpack is not optimal for workers ([refer to this issue for additional details](https://github.com/webpack/webpack/issues/6472)) so additional work/setup may be required to fully implement the JINT methodology.

Listed below are the primary benefits of using the JINT method:

- Choose any server-side rendering engine
- Project/functionality specific client-side rendering
- Persistent state management
- Code splitting
- Off the main thread architecture

### Mix & Match Rendering

JINT is structured around helping developers solve their project-specific problems. Developers don't have to choose between server-side rendering and client-side rendering, they can choose both without being restricted to any specific frameworks or templating engines.

Developers are able to use frameworks like [Next](https://nextjs.org/) or [Nuxt](https://nuxtjs.org/) or can use templating engines such as [Twig](https://twig.symfony.com/), [Blade](https://laravel.com/docs/5.8/blade), or [PHP](https://www.php.net/).

When tackling client-side rendering a developer can select the best tool for the job. Usually utilizing the [Template element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) is preferred, however, sometimes using a JavaScript templating engine like [Handlebars](https://handlebarsjs.com/) would work better, especially when large sections of dynamic content need to be rendered.

### Persistent State

One of the key features of JINT is the ability to utilize Pjax. It's a term used when referring to hijacking of traditional page navigation within a project where only one HTTP request is sent when loading the initial page. The future requests are loaded using [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) and the content of the page is dynamically swapped. By using Pjax JINT can maintain a persistent state while utilizing server-side rendering. Pjax can also be used to animate between page when simulating single page applications. When Pjax is paired with the [Session Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) or [indexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) developers can merge the benefits of both modern web application frameworks alongside the benefits of traditional websites.

## General Overview

The only `<script>` tag should be your runtime, and it should be deferred.

The only `<style>` or `<link rel="stylesheet">` tags should be critical CSS and other CSS should be inline, eagerly loaded, or lazy-loaded.

## Web Components

Use [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html). Custom elements are custom HTML elements where the tag name is lowercase, [kebab-case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles), and contains at least 1 hyphen.

When JavaScript is needed, upgrade a custom element into a [Web Component](https://developer.mozilla.org/en-US/docs/Web/Web_Components). Below is a diagram explaining how custom elements become web components.

![Diagram showing a web components upgrade path](/images/custom-element-to-web-component.png)

**How do you get the web components JavaScript with only one script tag?** Lazy load the JavaScript. Use [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to watch the custom elements that need to be upgraded and when the custom element intersects with the viewport fetch the script or use the [dynamic import syntax](https://v8.dev/features/dynamic-import).

**What if I know I'll need a web component right away?** Eager load the JavaScript. It's the same as lazy-loading except it doesn't use the Intersection Observer API.

**What can web components communicate with?** In short, web Modules and sometimes web components. Web components can communicate with other web components if the DOM hierarchy structure is a parent/child relationship and no outside components will communicate with the component. Separated web components should **NOT** directly communicate with one another.

## Threads

Stop overworking the main thread and stop thinking of it as the main thread, it's the UI thread.

**What can I do on the UI thread?** Handle event listeners and DOM manipulation.

**What shouldn't I do on the UI thread?** Fetch requests, state management, calculations, and anything that doesn't directly manipulate the DOM.

**Where should I perform my fetch requests?** In a web worker, service workers, or anywhere that's not the UI thread.

**Where should I manage my state?** Web worker, web server, or anywhere that's not the UI thread.

## OMT Controllers

An OMT (off the main thread) controller is used as an API for accessing a model that's maintained off the main thread. It can be accessed via an import statement and will consist of public functions that pass information to an OMT controller (web server, web worker, etc).

When communicating with a state manager the OMT controller is responsible for informing web components of the updated state. Web components never receive information directly. Instead, they are informed of state changes by a [Custom Event](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) that is dispatched on the `document`.

## Web Modules

Web modules typically consist of a class that has at least one export. Modules can be imported using the import statement or [dynamic importing](https://v8.dev/features/dynamic-import). A web module's role is to directly manipulate the DOM. [Click here](https://examples.jintmethod.dev/dialog-maker/) for an example of a dialog modal generator.

## Stylesheets

Any stylesheet marked as eager loaded should be loaded after the `DOMContentLoaded` event has fired on the `window`. Once all eager stylesheets have loaded, the DOM state should change from hard loading to soft loading and the full-screen loading animation should be hidden/stopped. Typically, the eagerly loaded stylesheets would contain the critical CSS needed for the initial user experience.

After the DOM state changes to soft loading, all lazy-loaded stylesheets should be loaded. Once all lazy stylesheets have loaded the DOM state should change from soft loading to idling and any loading animations should be hidden/stopped.

Stylesheets hidden behind a user interaction should be lazy-loaded when the interaction occurs.

## Ideal JINT Loading Strategy

![Diagram showcasing the ideal JINT loading strategy](/images/idea-jint-loading-strategy.png)

## JINT Continued

A full, example-rich version of the JINT method is [available here](https://jintmethod.dev/expanded).

## JINT Examples

[Click here](https://examples.jintmethod.dev/) to view the JINT methodology demos.
