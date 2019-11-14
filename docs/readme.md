# JavaScript In The Nick Of Time (JINT)

JINT is not a UI Framework or a Web Framework, and it's not a JavaScript library. JINT, at its core, is a question: what would the web look like if we used the Just In Time (JIT) strategy for everything?

JIT has been used in manufacturing by [Toyota](https://en.wikipedia.org/wiki/Just-in-time_manufacturing) since the 1960s. The earliest JIT compiler was created around the same time by [John McCarthy](https://en.wikipedia.org/wiki/Just-in-time_compilation) while he worked on the [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) programming language. JIT compiling is currently utilized by Google's [v8 JavaScript Engine](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) which is used by Google Chrome, Chromium, Brave, and Opera browsers along with Electron applications and Nodejs.

The purpose of JINT is to provide a methodology for efficiently loading, parsing, and running web sites & applications on devices ranging from the newest iPhone to the government-subsidized Nokia 2 on a 2g network.

## Why This Matters

JINT forces you to carefully consider how your product will load its resources. By devising a strategy you can directly impact your product's SEO rankings ([according to Google, search and ads are (partially) ranked based on speed](https://developers.google.com/web/updates/2018/07/search-ads-speed)). Managing load times can also help with retaining users. According to [this study]((https://www.imperva.com/blog/ecommerce-study/)) 62% of users will wait 5 seconds or less for a page to load before leaving. According to Google, [53% of mobile users will leave after 3 seconds](https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/). Studies also show that when users see a skeleton screen (when compared to spinners or blank screens) they [perceive the load time to be shorter](https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a).

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

With JINT you can use any UI/Web Framework. Select a server-side rendering engine, then a client-side rendering engine. Then use both rendering engines together to render content based on the context, desired functionality, network conditions, and the user's hardware. Developers are able to use frameworks like [Next](https://nextjs.org/) or [Nuxt](https://nuxtjs.org/) or can use templating engines such as [Twig](https://twig.symfony.com/) or [Blade](https://laravel.com/docs/5.8/blade). When tackling client-side rendering a developer can select the best tool for the job. Usually utilizing the [Template element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) is preferred, however, sometimes using a JavaScript templating engine like [Handlebars](https://handlebarsjs.com/) or selecting a UI Framework like [React](https://reactjs.org/), [Angular](https://angular.io/), or [Vue](https://vuejs.org/) would be the better choice.

## Persistent State

One of the key concepts of JINT is the utilization of Pjax. Pjax is a term used when referring to the hijacking of traditional page navigation within a project where only one HTTP request used to load the initial page. The future page requests are loaded using [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) and the content of the page is dynamically swapped. By using Pjax JINT can maintain a persistent state while utilizing server-side rendering. Pjax can also be used to animate between page when simulating single-page applications. When Pjax is paired with the [Session Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage), [indexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API), and [Service Workers](https://developers.google.com/web/fundamentals/primers/service-workers) developers can merge the benefits of both modern web application frameworks alongside the benefits of traditional websites.

# Implementation Guidelines

The only `<script>` tag should be your runtime, and it should be deferred. The only `<style>` or `<link rel="stylesheet">` tags should be critical CSS and other CSS should be inline, eagerly loaded, or lazy-loaded.

## Web Components

Use [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html). Custom elements are custom HTML elements where the tag name is lowercase, [kebab-case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles), and contains at least 1 hyphen.

When JavaScript is needed, upgrade a custom element into a [Web Component](https://developer.mozilla.org/en-US/docs/Web/Web_Components). Below is a diagram explaining how custom elements become web components.

![Diagram showing a web components upgrade path](/images/custom-element-to-web-component.png)

**How do you get the web components JavaScript with only one script tag?**
Lazy load the JavaScript. Use [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to watch the custom elements that need to be upgraded and when the custom element intersects with the viewport fetch the script or use the [dynamic import syntax](https://v8.dev/features/dynamic-import).

**What if I know I'll need a web component right away?**
Eager load the JavaScript. It's the same as lazy-loading except it doesn't use the Intersection Observer API.

**What can web components communicate with?**
In short, web Modules and sometimes web components. Web components can communicate with other web components if the DOM hierarchy structure is a parent/child relationship and no outside components will communicate with the component. Separated web components should **NOT** directly communicate with one another.

## Threads

Stop overworking the main thread and stop thinking of it as the main thread, it's the UI thread now.

![Diagram visualizing the concept of off-loading work to other threads](/images/sharing-the-workload.png)

**What can I do on the UI thread?**
Handle event listeners and DOM manipulation.

**What shouldn't I do on the UI thread?**
Fetch requests, state management, calculations, and anything that doesn't directly manipulate the DOM.

**Where should I perform my fetch requests?**
In a web worker, service workers, or anywhere that's not the UI thread.

**Where should I manage my state?**
In a web worker, web server, or anywhere that's not the UI thread.

**Where should I handle my business logic?**
In a web worker, web server, or anywhere that's not the UI thread.

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

## JINT Continued

A full, example-rich version of the JINT method is [available here](https://jintmethod.dev/expanded).

## JINT Examples

[Click here](https://examples.jintmethod.dev/) to view the JINT methodology demos.
