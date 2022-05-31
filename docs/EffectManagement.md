# Effects
Effects are **functions** that can run on ***every or particular renders*** of an element.<br/>
We give a `dependency array` to an effects, these can be `states / pseudo-states` or `partial dependencies` of the element.

On **every render**, if these **dependencies change**, the **effect will run**.

> **<u>Partial dependencies</u>** are, when you want the effect to run on ***every or first render*** of the element.

## Syntax
We use the `$effect` method to create an effect.
```js
const Element = new Spuck({ ... }).render();

const setState = Element.$state('state', 0);
Element.prop = { text: '$-state' }
Element.events = {
    click: () => setState(p => p + 1)
}

Element.$effect(() => {
    console.log('Effect ran');
}, ['$-state'])

const Child = new Spuck({ ... }).render();

Element.init.pseudoChildren = [Child];
Element.render('re');

Child.$effect(() => {
    console.log('Child Effect ran');
}, ['$$-state'])

Element.make('re')
Child.make('re')
```
## Working
In the above example, we created an element with a `state` and a `click` event.<br/>
Whenever the element is clicked, the state is updated, and the element re-renders.<br/>

When the *re-render is almost done*, library checks if **any dependency** of **any effect** has **changed**.<br/>
If it has, the effect runs.

In case of `Child`, it is a **pseudo-child** of `Element`, and its **effect** depends on its **pseudo-state**, `$$-state`.

**<u>NOTE:</u> An effect will *always run* on its *first render***.<br/> 
**<u>NOTE:</u> There can be *multiple dependencies* of an effect, if *any changes*, the *effect runs*.**<br/>

![](https://cdn.discordapp.com/attachments/956081993488154664/981097552994775060/Effect_Basic.gif)

## Example
We'll create a UI with a **heading** element that will *show product of two numbers*.<br/>
The two numbers will be managed by two **states**.<br/>
We will be able to increment both numbers by clicking on buttons.
The **product** will be managed by an **effect**.<br/>

```js
const H1 = new Spuck({ type: 'h1', parent: '#app', class: 'head' }).render();

const setCount = H1.$state('count', 0);
const setMultiplier = H1.$state('by', 1);
```
Now to multiply we will create a separate function.
```js
const product = () => H1.getState('count') * H1.getState('by');
H1.prop = { 
    text: `Product = ${product}`,
    css: { color: 'red' } // maybe a bit of css
};
```
You see we are using `getState`, because **product** will be managed by an **effect** => that line of code will run again, so the value of `product` will be updated.

Now we will create an **effect** on the `H1` element that will depend on both `count` and `by` states.
```js
H1.$effect(() => {
    H1.prop.text = `$-count x $-by = ${product()}`;
    H1.render('re');
}, ['$-count', '$-by']);

H1.make('re');
```
So whenever one of the states changes, the effect will run and **update** the `H1` element's **text**.<br/>
We are done with the **Heading**.

Now we will create two buttons to increment the `count` and `by` states.
As buttons will have *similar structure*, leaving few traits to be different, **we will create button as function**
```js
Button({ class: 'count', text: 'Counter', update: setCount });
Button({ class: 'by', text: 'Multiplier', update: setMultiplier });

function Button(properties) {
    const Button = new Spuck({ type: 'button', parent: '#app', class: properties.class });
    Button.prop = {
        text: properties.text,
        css: { cursor: 'pointer' }
    };
    Button.events = {
        click: () => properties.update(prevCount => prevCount + 1)
    };
    Button.make();
};
```

![](https://cdn.discordapp.com/attachments/956081993488154664/981092216770527252/Effect_Product.gif)

## Partial Dependencies
These dependencies let you run your effects **independent** of any `state / pseudo-state`.<br/>
There are **two types** of partial dependencies:
>* `['f']` This runs the effect only on the first render of the element.
>* `['e']` This runs the effect on every render of the element.
```js
AnyElement.$effect(() => f(), ['f']); // on first render

AnyElement.$effect(() => e(), ['e']); // on every render
```