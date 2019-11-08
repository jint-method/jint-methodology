# JavaScript In The Nick Of Time

The JINT method is a front-end design paradigm focused on serving resources using the Just In Time (JIT) strategy. JIT has been used in manufacturing by [Toyota](https://en.wikipedia.org/wiki/Just-in-time_manufacturing) since the 1960s and the earliest JIT compiler was created at the same time by [John McCarthy](https://en.wikipedia.org/wiki/Just-in-time_compilation) while he worked on the [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) programming language. JIT compiling is currently utilized by Google's [v8 JavaScript Engine](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) which in turn is used by Google Chrome, Chromium, Brave, and Opera browsers along with Electron applications and Nodejs.

The purpose of JINT is to provide a structured methodology explaining how resources can be efficiently loaded. Nothing is written in stone and the framework is malleable. You, the developer, **need to think through the problem and provide the solution**.

## Why This Matters

Before diving into the nitty-gritty details about implementing the JINT method we'll cover some of the biggest reasons it can benefit you and your users.

JINT forces you to carefully consider how your product will load its resources, directly impacting your products SEO ([Google ranks search and ads based on speed](https://developers.google.com/web/updates/2018/07/search-ads-speed)). Managing load times can also help with retaining users according to a study that discovered [62% of users will wait 5 seconds or less for a page to load before leaving](https://www.imperva.com/blog/ecommerce-study/) and, according to Google, [53% of mobile users will leave after 3 seconds](https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/). Studies also show that when users see a skeleton screen (when compared to spinners or blank screens) they [precieve the load time to be shorter](https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a).

Whether you're an in house team focused on enhancing your user's experience or an agency helping clients market themselves, there are some solid reasons to consider shaping the way your product loads.

## Required Knowledge

A basic understanding of Custom Elements, Web Components, and ES Modules are required to understand some of the examples and possible solutions that will be presented through this document. Please consider familiarizing yourself using the following links:

- [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html)
- [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [ES Modules](https://v8.dev/features/modules)

Although not required a basic understanding of how a state works in [Reactjs](https://reactjs.org/docs/state-and-lifecycle.html) along with the [Model-View-Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) software design pattern will provide additional insights.

## Overview

Let's begin with clearly and boldly stating that **this is not the solution to all your problems**, you need to find the right tool for the job. If you have a nail, the JINT hammer might work perfectly, however, when you have a screw, sometimes you'll want to check to see if [React](https://reactjs.org/), [Angular](https://angular.io/), or [Vue](https://vuejs.org/) screwdriver would work better. Use the best tool for the job. This is **NOT** a turn-key solution for every project.

Listed below are the primary benefits of using the JINT method:

- Choose Your Server-Side Rendering Method
- Project/Functionality Specific Client-Side Rendering
- Persistent State Management
- Malleable Project Framework

### Mix & Match Your Rendering

JINT is structured around helping developers solve their project-specific problems. Developers don't have to choose between server-side rendering and client-side rendering, they can choose both without being restricted to any specific frameworks or templating engines.

Developers are able to use frameworks like [Next](https://nextjs.org/) or [Nuxt](https://nuxtjs.org/) or can use templating engines such as [Twig](https://twig.symfony.com/), [Blade](https://laravel.com/docs/5.8/blade), or [PHP](https://www.php.net/).

When tackling client-side rendering a developer can select the best tool for the job. Usually utilizing the [Template element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) is preferred, however, sometimes using a JavaScript templating engine like [Handlebars](https://handlebarsjs.com/) would work better, especially when large sections of dynamic content need to be rendered.

### Persistent State

One of the key features of JINT is the ability to utilize Pjax. It's a term used when referring to hijacking of traditional page navigation within a project where only one HTTP request is sent when loading the initial page. The future requests are loaded using [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) and the content of the page is dynamically swapped. By using Pjax JINT can maintain a persistent state while utilizing server-side rendering. Pjax can also be used to animate between page when simulating single page applications. When Pjax is paired with the [Session Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) or [indexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) developers can merge the benefits of both modern web application frameworks alongside the benefits of traditional websites.

### JINT Pitfalls

Choosing to build a project framework that maintains the ability to mix & match how content is rendered along with what tools will use may seem like a no-brainer, however, the JINT method comes at a cost: documentation. Even though JINT provides a general structure that helps define functionality, rendering, and the methods of communication every project is unique and will require a developer to think through the problem, architect a solution, and document how the gears will work together. The role of JINT is to provide a general structure for loading critical HTML and CSS before lazy loading everything else. I will not and can not save you from poor project architecture or half-baked solutions.

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

## JINT Continued

A full, example-rich version of the JINT method is [available here](https://jintmethod.dev/expanded).

## JINT Examples

[Click here](https://examples.jintmethod.dev/) to view the JINT methodology demos.