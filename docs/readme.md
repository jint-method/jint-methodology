# Just In The Nick Of Time

The JINT method is a front-end design paradigm focused on serving resources using the Just In Time (JIT) strategy. JIT has been used in manufacturing by [Toyota](https://en.wikipedia.org/wiki/Just-in-time_manufacturing) since the 1960s and the earliest JIT compiler was created at the same time by [John McCarthy](https://en.wikipedia.org/wiki/Just-in-time_compilation) while he worked on the [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) programming language. JIT compiling is currently utilized by Google's [v8 JavaScript Engine](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) which in turn is used by Google Chrome, Chromium, Brave, and Opera browsers along with Electron applications and Nodejs.

### Why This Matters

Before diving into the nitty-gritty details about implementing the JINT model we'll cover some of the biggest reasons it can benefit you and your users. JINT forces you to carefully consider how your product will load its resources which can directly impact products SEO considering [Google ranks search and ads based on speed](https://developers.google.com/web/updates/2018/07/search-ads-speed). Managing load times can also help retain users according to studies that have shown [62% of users will wait 5 seconds or less for a page to load before leaving](https://www.imperva.com/blog/ecommerce-study/) and, according to Google, [53% of mobile users will leave after 3 seconds](https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/). Studies also show that when users see a skeleton screen (when compared to spinners or blank screens) they [precieve the load time to be shorter](https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a).

Whether you're an in house team focused on enhancing your user's experience or an agency helping clients market themselves, there are some solid reasons to consider shaping the way your product loads.

### Required Knowledge

A basic understanding of Custom Elements, Web Components, and ES Modules are required to understand some of the examples and possible solutions that will be presented through this document. Please consider familiarizing yourself using the following links:

- [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html)
- [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [ES Modules](https://v8.dev/features/modules)

Although not required a basic understanding of how a component's state works in [Reactjs](https://reactjs.org/docs/state-and-lifecycle.html) along with the [Model-view-controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) software design pattern will provide additional insights into some of the examples and diagrams that will be presented throughout this document.

# Overview

Let's begin with clearly and boldly stating that **this is not the solution to all your problems**, you need to find the right tool for the job. If you have a nail, the JINT hammer might work perfectly, however, when you have a screw, sometimes you'll want to check to see if [React](https://reactjs.org/), [Angular](https://angular.io/), or [Vue](https://vuejs.org/) screwdriver would work better. Use the best tool for the job. This is **NOT** a turn-key solution for every project.

Listed below are the primary benefits of using the JINT method:

- Choose Your Server-Side Rendering Method
- Project/Functionality Specific Client-Side Rendering
- Persistent State Management
- Malleable Project Framework

### Mix & Match Your Rendering

JINT is structured around helping developers solve their project-specific problems. Developers don't have to choose between server-side rendering and client-side rendering, they can choose both without being restricted to any specific frameworks or templating engines.

Developers are able to use frameworks like [Next](https://nextjs.org/) or [Nuxt](https://nuxtjs.org/) or can use templating engines such as [Twig](https://twig.symfony.com/), [Blade](https://laravel.com/docs/5.8/blade), or even good ol' [PHP](https://www.php.net/).

When tackling client-side rendering a developer can select the best tool for the job. Usually utilizing the [Template element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) is preferred, however, sometimes using a JavaScript templating engine like [Handlebars](https://handlebarsjs.com/) would work better, especially when large sections of dynamic content need to be rendered.

### Persistent State

One of the key features of JINT is the ability to utilize Pjax. It's a term used when referring to hijacking of traditional page navigation within a project where only one HTTP request is sent when loading the initial page. The future requests are loaded using [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) and the content of the page is dynamically swapped. By using Pjax JINT can maintain a persistent state while utilizing server-side rendering. Pjax can also be used to animate between page when simulating single page applications. When Pjax is paired with the [Session Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) developers can merge the benefits of both modern web application frameworks alongside the benefits of traditional websites.

### JINT Pitfalls

Choosing to build your project framework to maintain the ability to mix & match how content is rendered along with what tools you'll use may seem like a no-brainer, however, the JINT method comes at a cost: documentation. Even though JINT provides a general structure that helps define functionality, rendering, and the methods of communication every project is unique and will require a developer to think through the problem, architect a solution, and document how the gears will work together. The role of JINT is to provide a general structure for loading critical HTML and CSS before lazy loading everything else.

# Order of Operations

JINT is structured into two phases. Please note that some minor details may be missing and there will be some hand-waving when it comes to functionality that's defined by the [HTML spec](https://html.spec.whatwg.org/). In the following subsections, the terms server-side rendering and client-side rendering will be used when discussing when and where content should be rendered. These terms will be used within the context of dynamic content that's fed by a model, typically from a database. Static elements such as a footer can be server-side rendered and shipped with every page. The goal of JINT is to load dynamic content in an optimized manner, not to ship an empty frame that lazy loads every HTML element.

### Phase I

Phase one is structured around optimizing the initial page load. When thinking through how you'll implement JINT consider how much data you're queuing from the database along with what the impact of running your templating engine will be. Generally, anything above the fold that's not behind a user interaction should be server-side rendered.

1. An HTTP request is sent to the server
1. Server receives request
1. Query the minimal amount of data needed
1. Server-side render the critical HTML elements
1. Ship the lightest version of the document to the client
1. The client receives the document
1. [HTML parsing begins](https://html.spec.whatwg.org/multipage/parsing.html#parsing)
1. Inline loading animations `<style>` element is parsed by [CSSOM](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Object_Model)
1. DOM parses the main application script element with `type="module"` (modules are [deferred by default](https://v8.dev/features/modules#defer))
1. DOM asynchronously requests the application script
1. No more bytes are available to be parsed -- `Document` object created & `load` event is [queued](https://html.spec.whatwg.org/multipage/webappapis.html#queue-a-task) on the [networking task source](https://html.spec.whatwg.org/multipage/webappapis.html#networking-task-source)
1. Session history is updated
1. Scripts are executed
1. The `Document` becomes visible to the user, loading animation is running
1. The main application script (hereinafter referred to as App) sets it's state to `loading`
1. The App collects all critical CSS filenames
1. The App parses out any duplicate CSS filenames
1. App asynchronously fetches all critical CSS files using the [Fetch API](https://fetch.spec.whatwg.org/#fetch-api)
1. All critical CSS file request have a response
1. All critical CSS request responses are extracted as [Blobs](https://w3c.github.io/FileAPI/#dfn-Blob)
1. Object URLs are created for all critical CSS blobs using the [URL API](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL)
1. Dynamically generated `<link>` elements with a `rel="stylesheet"` attribute are generated for each object URL
1. All `<link>` elements receive a `load` event listener
1. All `<link>` elements are appended to the documents `<head>`
1. All `<link>` elements `load` events have fired
1. App sets it's state to `idling`
1. Loading animation is hidden

### Phase II

Phase two is structured around lazy loading functionality, non-critical content, client-side rendering, and persistent state management.
