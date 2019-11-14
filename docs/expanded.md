# JavaScript In The Nick Of Time

**This section is unfinished and unedited. Examples and content will change or be removed without notice.**

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

#### Custom Element to Web Component Upgrade Path

![Diagram showing a web components upgrade path](/images/custom-element-to-web-component.png)

#### Web Component Life Cycle

![Diagram explaining a web components life cycle](/images/web-component-lifecycle.png)

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

The explanation of web modules will rely on an understanding of [ES Modules](https://v8.dev/features/modules), state managers, and [node modules](https://www.npmjs.com/). You can think of a web module as having tow primary roles.

First, a web module can act as a state manager. When two web components need to share information or need to keep their states aligned a web module should be used. The general concept is that web components should be built to solve one problem or do one job, and they should do that job as quickly and efficiently as possible. There is no need to create a single component that tracks the event listeners of several elements with one stylesheet containing all the CSS. Get into the mindset of splitting your code into the smallest reusable pieces just like you would if you were building an interface with [React](https://reactjs.org/) or [Atomic Design](http://atomicdesign.bradfrost.com/table-of-contents/).

Let's look at an example of how a cart drawer could work on an e-commerce website. There could be three individual web components that all need to manage the drawers open state. One is a cart icon button in the header, one is the close icon button in the cart, and finally, there is the cart drawer itself. Now that we have our functionality split into three web components, let's define a web module that will manage the state.

[Click here](https://examples.jintmethod.dev/cart-manager/) to view the live demo of the following example.

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

#### Custom Events

Another option for managing communication between web modules and web components is the use of the [Custom Events API](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent). Instead of having to explicitly call public methods on every instance of a possible web component (as defined in the Public Methods option above) we can create a custom event the fires on the `document` and contains an instance of the current state. Then any web component on the page that needs to react to state changes can do so when they receive the event. This is the preferred method because it follows the idea that each web component should be responsible for managing its functionality. Below is an example of how the custom event could be created by the `CartManager` class.

```typescript
class CartManager
{
    public toggleDrawer(forcedState:boolean = null) : void
    {
        const updatedState:Partial<CartManagerState> = {};
        if (forcedState !== null)
        {
            updatedState.isDrawerOpen = forcedState;
        }
        else
        {
            updatedState.isDrawerOpen = (this.state.isDrawerOpen) ? false : true;
        }

        this.setState(updatedState);
    }

    private setState(updatedState:Partial<CartManagerState>) : void
    {
        Object.keys(updatedState).forEach((key:string) => {
            if (this.state[key] !== null && this.state[key] !== undefined)
            {
                if (updatedState[key] !== this.state[key])
                {
                    this.state[key] = updatedState[key];
                }
            }
            else
            {
                this.state[key] = updatedState[key];
            }
        });
        const updateEvent = new CustomEvent('cart:update', {detail: { state: { ...this.state } } });
        document.dispatchEvent(updateEvent);
    }
}
```

In the code above we create an empty updated state object and only set the values that need to change. We then added a new private `setState()` method that takes a partial state object. We then proceed to update just the changed values of the state with the values that are set in the updated state object. Then an update event is created that contains an instance of the state object before being fired on the `document`. Then each component that needs to react can simply listen for the update event.

```typescript
class CartIconButton extends HTMLElement
{
    connectedCallback()
    {
        document.addEventListener('cart:update', (event:CustomEvent) => {
            const cartState = event.detail.state;
            if (cartState.isDrawerOpen)
            {
                this.style.transform = 'translateX(0)';
            }
            else
            {
                this.style.transform = 'translateX(-100%)';
            }
        });
    }
}
```

### Web Modules Continued

Now that we've covered how web modules can be used to manage the state and communication between several web components let's discuss their second primary function. Web modules can also act as node modules/npm packages that you could use on the front-end, like [animejs](https://github.com/juliangarnier/anime/). This section will contain two examples of how web modules can be used as a controller that manipulates a model (see [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)) or could define it's own MVC structure.

#### Manipulating the DOM

Let's say that only on a specific page we want the body's background color to randomly change as the user scrolls. As previously stated, web components should be designed to do one specific job and to do it efficiently. To expand upon that, web components should be restricted to manipulate their view (as defined by the custom element the component is attached to). So, how do we manipulate the body? We use a web module.

[Click here](https://examples.jintmethod.dev/background-changer/) to view the live demo of the following example.

```typescript
class BackgroundChanger
{
    constructor()
    {
        window.addEventListener('scroll', this.handleScrollEvent, { passive: true });
    }

    private handleScrollEvent:EventListener = this.changeBackground.bind(this);
    
    private changeBackground() : void
    {
        document.body.style.backgroundColor = this.getRandomColor();
    }

    private getRandomColor() : string
    {
        const letters = '0123456789ABCDEF';
        let color = '#';
        for (var i = 0; i < 6; i++) {
            color += letters[Math.floor(Math.random() * 16)];
        }
        return color;
    }
}
new BackgroundChanger();
```

Now that we have our `BackgroundChanger` class that adds a [passive](https://developers.google.com/web/updates/2016/06/passive-event-listeners) `scroll` event listener, how do we actually include the file? Using a script tag. You could write `<script src="/background-changer.js" type="module">`.

# Client-Side Rendering

This section will cover a few of available options for handling client-side rendering along with showcasing an example of when client-side rendering can be leveraged in order to reduce the number of database queries required on every page HTTP request along with following the JINT model of only server-side rendering content that the user will actually see when the page initially loads.

### Dynamically Generated Elements

The first available option is to manually create all the elements using `document.createElement()`. Typically this method is preferred when creating small, one-time use, elements such as a [snackbar notification](https://material.io/components/snackbars/). It could be used to generate a [dialog modal](https://material.io/components/dialogs/) depending on how often and dynamic the modal needs to be. When confirming a users action it's possible to generate all the elements, however, when a dialog modal is used/generated several times on the page or used to an information modal such as a location modal on a map it might be better to use the Template method (see below).

For our example, we'll say that the user has a list of user accounts and we want to confirm their choice to delete the user. Since the confirmation modal doesn't exist within any web component we'll use a web module to generate, append, and remove the modals. Let's also assume that there will be other instances within our application where we want to confirm an action our user so we'll design our module to be dynamic and to generate dialog modals without a predefined context.

[Click here](https://examples.jintmethod.dev/dialog-maker/) to view the live demo of this example.

**Side note:** there are times where a web component could dynamically generate the elements. For example, if there was an interactive map filled with pins that need to open up a modal that displays information about the location the modal elements could be generated by the web component instead of a web module. An empty modal could appear with a loading spinner, the ID of the pin could be retrieved from the click event, and a request for the data could be sent to the server. Once the server responds with the data the new elements are created and appended before the content is revealed.

Now, back to our primary example. Let's define a web module that has a `makeModal()` method that takes a title, message, and an array of actions objects.

```typescript
interface Modal
{
    title: string,
    message?: string,
    actions: Array<ModalAction>,
}

interface ModalAction
{
    label: string,
    callback: Function,
    classes: Array<string>,
}

class DialogModalMaker
{
    public makeModal(modalObject:Modal) : void
    {
        const modal = document.createElement('dialog-modal-container');

        const modalBackdrop = document.createElement('dialog-modal-backdrop');
        modalBackdrop.addEventListener('click', () => { modal.remove(); });
        modal.append(modalBackdrop);

        const dialogModal = document.createElement('dialog-modal');

        if (modalObject.title)
        {
            const title = document.createElement('h3');
            title.innerText = modalObject.title;
            dialogModal.append(title);
        }

        if (modalObject.message)
        {
            const message = document.createElement('p');
            message.innerText = modalObject.message;
            dialogModal.append(message);
        }

        if (modalObject.actions)
        {
            const actionsContainer = document.createElement('dialog-actions-wrapper');
            modalObject.actions.map((action:ModalAction) => {
                const button = document.createElement('button');
                button.innerText = action.label;
                button.classList.add(...action.classes);
                button.addEventListener('click', () => {
                    modal.remove();
                    action.callback();
                });
                actionsContainer.append(button);
            });
            dialogModal.append(actionsContainer);
        }

        modal.append(dialogModal);
        document.body.append(modal);
    }
}
export const modalMaker:DialogModalMaker = new DialogModalMaker();
```

Now that we have our web module defined, let's import the module and use the `makeModal()` method in a web component.

```typescript
import { modalMaker } from './dialog-modal-maker.js';

class UserAccountComponent extends HTMLElement
{
    private promptDeleteDialog:EventListener = this.prompt.bind(this);

    private prompt() : void
    {
        modalMaker.makeModal({
            title: 'Confirm Account Deletion',
            message: 'Deleting a users account cannot be undone. Are you sure you want to delete this account?',
            actions: [
                {
                    label: 'close',
                    classes: ['-text', '-grey'],
                    callback: ()=>{}
                },
                {
                    label: 'delete',
                    classes: ['-solid', '-red'],
                    callback: this.deleteAccount.bind(this)
                }
            ]
        });
    }

    private deleteAccount() : void
    {
        /** Do account deletion logic */
    }

    connectedCallback()
    {
        const deleteButton = this.querySelector('button');
        deleteButton.addEventListener('click', this.promptDeleteDialog);
    }
}
customElements.define('user-account', UserAccountComponent);
```

With the example web component above we now have a user account web component that uses a web module to prompt the user asking if they're sure the account should be deleted. If they click the delete button and confirm the deletion of the account the `deleteAccount()` method is called.

### Content Template Element

The HTML Content Template `<template>` element is used to hold HTML that is not immediately rendered when the page is loaded. According to the [HTML spec](https://html.spec.whatwg.org/multipage/scripting.html#the-template-element), the template element is used to hold HTML that will be used to declare a document fragment within a script. Query selectors can then be used to access the HTML elements within the fragment so the content can be populated before the fragment is appended to the DOM.

The template element is the preferred method for handling client-side rendering. The example below will showcase how the template element could be used to generate a call to action cards for a project's blog.

[Click here](https://examples.jintmethod.dev/blog-cards/) to view the live demo of this example.

```html
<blog-cards-component>
    <blog-card-container></blog-card-container>
    <template>
        <blog-card>
            <a>
                <img>
                <h3></h3>
                <p></p>
                <button></button>
            </a>
        </blog-card>
    </template>
</blog-cards-component>
```

In the HTML above we define a blog card element that contains a cards container where the cards that are created will be appended to along with the template that will be used to generate the cards. When rendering the template element represents nothing so won't affect the layout and can be placed wherever it can be conveniently referenced. The `<blog-cards-component>` custom element will be upgraded to a web component using the following script.

```typescript
interface BlogCard
{
    title: string,
    copy: string,
    img: {
        url: string,
        alt: string,
    },
    link: {
        text: string,
        url: string,
    },
}

class BlogCardsComponent extends HTMLElement
{
    private _template : HTMLTemplateElement;
    private _cardContainer : HTMLElement;

    constructor()
    {
        super();
        this._template = this.querySelector('template');
        this._cardContainer = this.querySelector('blog-card-container');
    }

    private generateBlogCards(data:Array<BlogCard>) : void
    {
        data.map((card) => {
            const node = document.importNode(this._template.content, true);
            const img = node.querySelector('img');
            const title = node.querySelector('h3');
            const copy = node.querySelector('p');
            const link = node.querySelector('a');
            const button = node.querySelector('button');

            img.src = card.img.url;
            img.alt = card.img.alt;
            title.innerText = card.title;
            copy.innerText = card.copy;
            link.href = card.link.url;
            button.innerText = card.link.text;

            this._cardContainer.append(node);
        });
    }
}
customElements.define('blog-cards-component', BlogCardsComponent);
```

The code above hand-waves some of the functionality such as: where does the card data come from, is there a loading animation/state, can the user infinitely scroll to view more cards, is there/should there be a load more button, etc. However, the script should give a general idea of how the template element can be leveraged to quickly render content on the client's side.

Although the template element is the preferred method for client-side rendering, sometimes it is better to use a JavaScript templating engine especially when there is a vast amount of dynamic data that needs to be rendered.

### JavaScript Templating Engines

Throughout this section, we'll be referring to [handlebars](https://handlebarsjs.com/) as the JavaScript templating engine. Other libraries solve the same problem as handlebars, however, handlebars is used by 2.2 million projects and has 154 contributors on [GitHub](https://github.com/wycats/handlebars.js/) so can be considered a safe choice.

Let's start by asking why you're relying on a JavaScript templating engine. If the content you need to render is so complex or dynamic that it would be difficult to solve using the template element you should ask yourself why you're not utilizing the server-side templating engine. If your project is using a [headless CMS](https://en.wikipedia.org/wiki/Headless_content_management_system) then a JavaScript templating engine might make sense, however, if you're already serving your content from a server that could utilize PHP or a templating engine you might want to reconsider your content delivery strategy. An alternative option could be to AJAX in the section when it's needed. The default view could be a loading spinner and when the section becomes visible an AJAX request is sent to the server, the server renders the section using the dynamic and complex content and responds with the HTML to be injected into the section.

Since the goal of JINT is to provide a general solution for merging client-side rendering with server-side rendering this section will not be showcasing an example of how to utilize a library like handlebars. JINT is not a competitor to [React](https://reactjs.org/), [Angular](https://angular.io/), or [Vue](https://vuejs.org/). If your project is using a headless CMS and your content is so complex that the template element can't be used you might be using the JINT hammer when really what you need is one of the before-mentioned framework screwdrivers.

---

## Continued Reading

- [An Introduction to JINT](https://examples.jintmethod.dev/)
- [Implementation Guidelines](https://examples.jintmethod.dev/implementation-guidelines)
- [Interactive Demos](https://examples.jintmethod.dev/)