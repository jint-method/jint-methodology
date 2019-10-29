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

Phase two is structured around lazy loading functionality, non-critical content, client-side rendering, and persistent state management. Typically content that is rendered in phase two is hidden behind a user interaction or below the fold. Usually, data for the content below the fold will be fetched when the component becomes visible and the content will be client-side rendered when the data arrives. Depending on the type of content a loading animation or skeleton frame should be used. When components have several moving parts it can be helpful to display the loading bar as a progress bar informing the user how for along the loading process is. Phase two is split into two parts. Phase 2a loads all non-critical stylesheets and Phase 2b handles web components.

#### Stylesheets

1. Collect all non-critical CSS filenames
1. Purge duplicate filenames
1. Dynamically generated `<link>` elements with a `rel="stylesheet"` attribute are generated for each object URL
1. Verify for each `<link>` that the stylesheet hasn't already loaded -- if it has don't append the link
1. All `<link>` elements receive a `load` event listener
1. All `<link>` elements are appended to the documents `<head>`
1. All `<link>` elements `load` events have fired

#### Web Components

1. Collect all [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html) that will be upgraded into [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
1. Observe all custom elements using the [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver)
1. IO callback fired
1. Verify that [IntersectionObserverEntry](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry) is intersecting
1. Verify the web components script hasn't already been loaded -- if it has already loaded only unobserve the entry
1. Dynamically generate a `<script>` element with a `type="module"` attribute where the `src` attribute uses the web components filename
1. Attach a `load` event listener to the script element
1. Append the script to the `<head>`
1. Unobserve the entry

#### Phase II Expanded

There are several ways to expand upon phase 2 to enhance the user experience. For example, setting at custom `state` attribute on the custom elements that change from `unseen` to `loading` to `mounted` could then be used in CSS to manage how the web component appears. In the example below the component doesn't show it's button elements until the web component has been mounted.

```scss
custom-element
{
    &[state=mounted]
    {
        button
        {
            visibility: visible;
            opacity: 1;
        }
    }

    button
    {
        visibility: hidden;
        opacity: 0;
        transition: all 150ms ease;
    }
}
```

You could also create a system where the stylesheets for web components are not fetched until the components become visible.

*What if I need actual critical CSS/JS?*

Write it inline. JINT is not here to restrict developer's ability to craft the solution needed for their unique situation, it's a guideline.

If a script has to be loaded first and immediately for whatever reason, load it. Write the `<script>` tag and choose `async`, `defer`, or `type="module"` as needed. If you need to use a script that supports an older browser use the `type="text/javascript" nomodule`  attributes.

If you need CSS before the initial paint write a `<link>` tag, or even a `<style>` tag. If it's something minor such as setting a drawer to be `transform: translateX(-100%)` by default write an inline style using the `style` attribute.

# The Art of Communication

This section will cover the basics of how web components should communicate with one another along with information about how to manage state and will introduce the concept of *web modules*.

### Web Modules

The explanation of web modules will rely on an understanding of [ES Modules](https://v8.dev/features/modules), state managers, and [node modules](https://www.npmjs.com/). You can think of a web module as having to key roles.

First, a web module can act as a state manager. When two web components need to share information or need to keep their states aligned a web module should be used. The general concept is that web components should be built to solve one problem or do one job, and they should do that job as quickly and efficiently as possible. There is no need to create a single component that tracks the event listeners of several elements with one stylesheet containing all the CSS. Get into the mindset of splitting your code into the smallest reusable pieces just like you would if you were building an interface with [React](https://reactjs.org/) or [Atomic Design](http://atomicdesign.bradfrost.com/table-of-contents/).

Let's look at an example of how a cart drawer could work on an e-commerce website. There could be three individual web components that all need to manage the drawers open state. One is a cart icon button in the header, one is the close icon button in the cart, and finally, there is the cart drawer itself. Now that we have our functionality split into three web components, let's define a web module that will manage the state.

```typescript
interface CartManagerState
{
    isDrawerOpen : boolean,
}

class CartManager
{
    private state : CartManagerState;

    constructor()
    {
        this.state = {
            isDrawerOpen: false,
        };
    }

    public toggleDrawer(forcedState:boolean = null) : void
    {
        if (forcedState !== null)
        {
            this.state.isDrawerOpen = forcedState;
        }
        else
        {
            this.state.isDrawerOpen = (this.state.isDrawerOpen) ? false : true;
        }
    }
}

export const cart:CartManager = new CartManager();
```

In the code above we define a `CartManager` class that has a public `toggleDrawer()` method that can take a boolean. At the bottom of the file, we used the `export` keyword as it's defined in [ES2015](https://www.ecma-international.org/ecma-262/6.0/). Now let's define the cart icon buttons functionality.

```typescript
import { cart } from './cart-manager.js';

class CartIconButton extends HTMLElement
{
    connectedCallback()
    {
        this.addEventListener('click', ()=>{
            cart.toggleDrawer();
        });
    }
}
customElements.define('cart-icon-button', CartIconButton);
```

At this point, we've created a web component that imports the `CartManager` and calls it's public `toggleDrawer()` method when clicked. At this point in the example, the key things to understand are that web modules are exported. This means that we don't have to load the web modules script since the `import` keyword will handle fetching the script when it's needed, the fewer resources loaded before they're needed, the better.

Now that we have a web component communicating with a web module, how would we tell the cart drawer to open? There are a few options available and we'll take a moment to explain what method works the best.

#### Public Methods

With web components, we're able to use a query selector to find the element within the DOM and call its public methods. Let's assume the `cart-drawer` web component has a `setDrawerState()` method that takes a boolean. We could do the following in the `CartManager` class:

```typescript
class CartManager
{
    ...snip...

    public toggleDrawer(forcedState:boolean = null) : void
    {
        if (forcedState !== null)
        {
            this.state.isDrawerOpen = forcedState;
        }
        else
        {
            this.state.isDrawerOpen = (this.state.isDrawerOpen) ? false : true;
        }

        const drawerElement = document.body.querySelector('cart-drawer');
        drawerElement.setDrawerState(this.state.isDrawerOpen);
    }
}
```

There are a few key problems with this method. First, `setDrawerState()` could be `undefined`. Just because the element is in the DOM doesn't mean that the web component has been mounted. When using the JINT method the custom element is likely sitting in an unseen state and the script that contains the `setDrawerState()` method hasn't been requested/loaded yet.

Now let's assume you're not using the JINT method and all script are render-blocking so you *"know"* that the method will be defined. What happens if this component was dynamic or if several instances existed? Are you going to use `document.body.querySelectorAll('cart-drawer')` every time the drawer is toggled just so you can call `setDrawerState()` within a try-catch block hoping everything works?

What would happen if you have different web components? Let's pretend that instead of sending the drawers open state that we need to send an array of line items to our cart. Now, let's say the user is on the cart page. We need to dynamically keep two cart instances in sync. One cart is in the cart drawer that the user can toggle open and the other instance exists on the cart page. When the user modifies the quantity of a line item in the cart drawer are you going to write a bunch of query selectors to see if the elements are defined before trying to *"blindly"* call public methods hoping everything syncs up correctly? No, you're not, or at least you shouldn't.