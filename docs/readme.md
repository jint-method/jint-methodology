# Just In The Nick Of Time

The JINT method is a front-end design paradigm focused on serving resouces using the Just In Time (JIT) stragety. JIT has been used in manufacturing by [Toyota](https://en.wikipedia.org/wiki/Just-in-time_manufacturing) since the 1960s and the earliest JIT compiler was created at the same time by [John McCarthy](https://en.wikipedia.org/wiki/Just-in-time_compilation) while he worked on the [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) programming language. JIT compiling is currently utilized by Google's [v8 JavaScript Engine](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) which in turn is used by Google Chrome, Chromium, Brave, and Opera browsers along with Electron applicaitons and Nodejs.

### Why This Matters

Before diving into the nitty gritty details about implementing the JINT model we'll cover some of the biggest reasons it can benifit you and your users. JINT forces you to carefully consider how your product will load its resources which can directly impact products SEO considering [Google ranks search and ads based on speed](https://developers.google.com/web/updates/2018/07/search-ads-speed). Managing load times can also help retain users according to studies that have showen [62% of users will wait 5 seconds or less for a page to load before leaving](https://www.imperva.com/blog/ecommerce-study/) and, according to Google, [53% of mobile users will leave after 3 seconds](https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/). Studies also show that when users see a skeleton screen (when compared to spinners or blank screens) they [precieve the load time to be shorter](https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a).

Whether you're an in house team focused on enhancing your users experience or an agency helping clients market themselfs, there are some solid reasons to consider shaping the way your product loads.

### Required Knowledge

A basic understanding of Custom Elements, Web Componets, and ES Modules is required in order to understand some of the examples and possible solutions that will be presented through this document. Please consider familarizing yourself using the following links:

- [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html)
- [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [ES Modules](https://v8.dev/features/modules)

Although not required a basic understanding of how a component's state works in [Reactjs](https://reactjs.org/docs/state-and-lifecycle.html) along with the [Model-view-controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) software design pattern will provide additional insights into some of the examples and diagrams that will be presented throughout this document.
