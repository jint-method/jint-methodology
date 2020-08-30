# Just In The Nick Of Time (JINT)

JINT is not a UI Framework, a Web Framework, and it's not a JavaScript library. JINT, at its core, is a question: **what would the web look like if we used the Just In Time (JIT) strategy for everything?**

Just in time has been used in [manufacturing since the 1960s](https://en.wikipedia.org/wiki/Just-in-time_manufacturing) and the earliest JIT compiler was created by [John McCarthy](https://en.wikipedia.org/wiki/Just-in-time_compilation) while he worked on the [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) programming language. JIT compiling is currently utilized by Google's [v8 JavaScript Engine](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) and Google Chrome, Chromium, Brave, and Opera along with Electron  and Nodejs.

The purpose of JINT is to provide a methodology for efficiently loading, parsing, and running websites & applications on devices ranging from the newest iPhones to the Nokia 2 with network conditions ranging from 5g to offline. What you load matters but when and how you load it matters more.

## Key Concepts:

- Progressive Enhancement
  - Enhance web components based on users device and network connection
  - Render first functionality second
- Main Thread ➞ UI Thread
  - Main Thread is used to manipulate the DOM
  - Main Thread is used to post messages to web workers, service workers, and servers
- Off Main Thread Architecture
  - Web Workers handle client-side business logic and state management
  - Service Workers handle resource fetching/caching
  - Focus on utilizing web servers and 3rd party APIs whenever possible
- Persistent State Management
  - One initial HTTP request
  - [Ajax](https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX) all content
  - [Pjax](https://pjax.herokuapp.com/) all pages
- Offline First
  - Service Workers follow an offline first content & resource policy
  - Utilize the [Backgorund Sync API](https://developers.google.com/web/updates/2015/12/background-sync)
  - Utilize the [IndexedDB API](https://developers.google.com/web/ilt/pwa/working-with-indexeddb)
- Just In Time
  - Only load what you need when you need it
  - Defer all JavaScript
  - Defer all CSS

## Why This Matters

JINT forces you to carefully consider how your product will load its resources. By devising a strategy you can directly impact your product's SEO rankings ([according to Google, search and ads are (partially) ranked based on speed](https://developers.google.com/web/updates/2018/07/search-ads-speed)). Managing load times can also help with retaining users. According to [this study]((https://www.imperva.com/blog/ecommerce-study/)) 62% of users will wait 5 seconds or less for a page to load before leaving. According to Google, [53% of mobile users will leave after 3 seconds](https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/). Studies also show that when users see a skeleton screen (when compared to spinners or blank screens) they [perceive the load time to be shorter](https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a).

## Overview

With JINT you bring your own framework. [Next](https://nextjs.org/), [Nuxt](https://nuxtjs.org/), [Twig](https://twig.symfony.com/), [Blade](https://laravel.com/docs/5.8/blade), [Handlebars](https://handlebarsjs.com/), [React](https://reactjs.org/), [Angular](https://angular.io/), or [Vue](https://vuejs.org/). It doesn't matter what you choose it's all about how you use it.

## Persistent State

One of the key concepts of JINT is the utilization of Pjax. Pjax is a term used when referring to the hijacking of traditional page navigation within a project where only one HTTP request used to load the initial page. The future page requests are loaded using [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) and the content of the page is dynamically swapped. By using Pjax JINT can maintain a persistent state while utilizing server-side rendering. Pjax can also be used to animate between page when simulating single-page applications. When Pjax is paired with the [Session Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage), [indexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API), and [Service Workers](https://developers.google.com/web/fundamentals/primers/service-workers) developers can merge the benefits of both modern web application frameworks alongside the benefits of traditional websites.

## Threads

Stop overworking the main thread and stop thinking of it as the main thread, it's the UI thread now.

![Diagram visualizing the concept of off-loading work to other threads](/images/sharing-the-workload.png)

#### What can I do on the UI thread?
Handle event listeners and DOM manipulation.

#### What shouldn't I do on the UI thread?
Fetch requests, state management, calculations, and anything that doesn't directly manipulate the DOM.

#### Where should I perform my fetch requests?
In a web worker, service workers, or anywhere that's not the UI thread.

#### Where should I manage my state?
In a web worker, web server, or anywhere that's not the UI thread.

#### Where should I handle my business logic?
In a web worker, web server, or anywhere that's not the UI thread.

---

### Continued Reading

- [Implementation Guidelines](https://jintmethod.dev/implementation-guidelines)
- [JINT Expanded](https://jintmethod.dev/expanded)
- [Interactive Demos](https://examples.jintmethod.dev/)
- [An Actor Model Implementation](https://github.com/jint-method/actor-model-prototype)

### Attribution

- [RAIL Model](https://developers.google.com/web/fundamentals/performance/rail)
- [App Shell Model](https://developers.google.com/web/fundamentals/architecture/app-shell)
- [Idle Until Urgent](https://philipwalton.com/articles/idle-until-urgent/)
- [Off Main-Thread State Management](https://dassur.ma/things/react-redux-comlink/)
- [The 9am Rush Hour](https://dassur.ma/things/the-9am-rush-hour/)
- [Main Thread is Overworked](https://youtu.be/7Rrv9qFMWNM)
- [Architecting Web Applications](https://youtu.be/Vg60lf92EkM)
- [Adaptive Loading](https://youtu.be/puUPpVrIRkc)
- [Speed Essentials](https://youtu.be/reztLS3vomE)
- [Lights, Camera, Action!](https://dassur.ma/things/lights-camera-action/)
- [An Actor, a model, and an architect walk into the web...](https://dassur.ma/things/actormodel/)
- [Headless Web Development](https://dassur.ma/things/headless-web-development/)
