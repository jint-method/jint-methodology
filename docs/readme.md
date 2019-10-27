# Just In The Nick Of Time Methodology

The JINT method is a front-end design paradigm created to focus on serving resouces using a Just In Time (JIT) stragety. If you're unfamilar with JIT spend a few minutes leaning how the term JIT was used for manufacturing and production in the 1960s by [Toyota](https://en.wikipedia.org/wiki/Just-in-time_manufacturing) and the earliest JIT compiler was created by [John McCarthy in 1960](https://en.wikipedia.org/wiki/Just-in-time_compilation). JIT compiling is used by Google's [v8 JavaScript Engine](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)).

### Why This Matters

Before diving into the nitty gritty details about implementing the JINT model we'll cover why it's worth experimenting with. One of the biggest reason to carefully consider how your product will load it's resources is that [Google will be ranking search and ads based on speed](https://developers.google.com/web/updates/2018/07/search-ads-speed), we also know that [62% of users will wait 5 seconds or less for a page to load before leaving](https://www.imperva.com/blog/ecommerce-study/) and according to Google [53% of mobile users will leave after 3 seconds](https://www.thinkwithgoogle.com/marketing-resources/data-measurement/mobile-page-speed-new-industry-benchmarks/). Studies also show that when users see a skeleton screen (when compared to spinners or blank screens) they [precieve the load time to be shorter](https://uxdesign.cc/what-you-should-know-about-skeleton-screens-a820c45a571a). Whether you're an in house team building a product or an agency working with clients, there are some solid reasons to consider shaping the way your products load resources.

### Required Knowledge

A basic understanding or awareness of Custom Elements, Web Componets, and ES Modules is required in order to understand some of the examples and possible solutions that will be presented through this document. Please consider familarizing yourself with the following:

- [Custom Elements](https://html.spec.whatwg.org/multipage/custom-elements.html)
- [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [ES Modules](https://v8.dev/features/modules)
