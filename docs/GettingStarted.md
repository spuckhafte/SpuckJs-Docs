# Getting Started
This section will show you how to get started with SpuckJs.

## **new Spuck()**
We know that every **object** of class **Spuck** is a *virtual element* that has to be put in the **DOM**.
```js
const Element = new Spuck();
```
If we console log this `Element` we can see different properties and methods.

```js
Spuck = {
    init: undefined
    prop: undefined
    events: undefined
    attr: undefined
    _pseudoState: {}
    _state: {}
    #_CSP: "$$-"
    #_SP: "$-"
    #_alterState: #_alterState(_finVal, _name)
    #_deps: Object
    #_effects: Object
    #_getStateName: ƒ #_getStateName(_stateName)
    #_partialEffects: Object
}
```
First 4 properties are the important ones for basic operations, others are managed by the library<br/>
>* **init**: This will be an *object*, and will define *type, parent, class and id* ***(this is necessary)***.  
>* **prop**: ...*object* and will define **text, value and css** of the element.
>* **events**: ...*object* and will define **events**.
>* **attr**: ...*object* and will define **attributes**.

Creating a **Heading** element.
```js
const Heading = new Spuck();
Heading.init = {
    type: "h1",
    parent: "#app", // a div defined in the html file
    class: "heading",
    id: "heading"
};
```
Doing the initalizing part in *one line* is handy.
```js
const Heading = new Spuck({ type: 'h1', parent: '#app', class: 'heading', id: 'heading' });

Heading.prop = { text: 'Hello Spuck' };
```
Here we defined the **text** of element, in `prop` property.

This element will be put in the *DOM*, but it is not yet ready to be *mounted*.

## **render()**
Our `Heading` element is just a *virtual element* that has to be put in the **DOM**.

To convert it into a *real DOM element* we use `render` method.
```js
Heading.render();
```
This method **adds** bunch of *properties defined* into the **physical element**.

**<u>NOTE:</u>** `render` does not **mount** the element, it just **creates or updates** an element with some properties.

When you *render* an element more than once you *pass a parameter* to the method, i.e., `'re'`
```js
AnyElement.render('re')
```
`render` with **no parameter** creates a *new element* with some properties, while **`render('re')`** *updates* the element with some properties.

### Late Note
`render` does **mount** an element in a ***case***, we'll look at it [later](../InbuiltFeatures/#conditional-rendering).

## **DOM Methods**
### mount()
`mount` is a method that **puts** an element in the **DOM**.
### unMount()
`unMount` is a method that **removes** an element from the **DOM**.
### isMount()
`isMount` is a method that **checks** if an element is *mounted or not*.
### make()
`make` just combines `render` and `mount` methods.
It also accepts a parameter to be `re`, when element is already **rendered**. It just passes the parameter to the `render` method.

**So in our case of Heading, we should probably use `make`, as it is a very simple element.**<br/>
You don't have to `render` it if you use `make`.
```js
...
Heading.make();
```
Now the element is in the DOM.

## Heading at once
```js
const Heading = new Spuck({ type: 'h1', parent: '#app', class: 'heading', id: 'heading' });
Heading.prop = { text: 'Hello Spuck' };
Heading.make();
```
![heading](https://cdn.discordapp.com/attachments/956081993488154664/980888857954377758/Heading_Example.png)
