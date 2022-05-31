# State Management
States are some *__internal values__ maintained* by an element.<br/>
These are important because if these values *change*, the element gets *re-rendered*.<br/><br/>
You can *refer* to them in code, like in `innerHTML`, `css` or any other *property* of the element. *On change*, the element *automatically __updates their reference__* in the code.<br/><br/>

## **`$state` Method**
In SpuckJs, we *use the state* using `$state` method.<br/>
>`$state` method *returns* a **function** to *update* that state.

**<u>NOTE:</u> State can only be managed in a *rendered* element.**

Let's see how it works.

```js
/* 
    Lets create a new display element.
    Render it while initializing because
    State can only be managed in a rendered element.
*/
const Display = new Spuck({ type: 'h3', parent: '#app', id: 'dis' }).render();
const setCount = Display.$state('count', 0);
```
>**First Parameter**: name of state<br/>
>**Second Parameter**: initial value of state

Now to *refer this state* in the code, there are **two ways**:
### `.getState` method
```js
Element.prop = { text: Element.getState(stateName) }
```
When you refer to the state value using this method, it will <u>**not update**</u> the reference in the code when state changes.<br/>
It can **<u>only update</u>** if the *line of code* where this method is mentioned *runs again*.<br/>
That is, if *used in a __loop__*, *bind to an __event__* or used in an **effect**.<br/>In all these cases, the state value will be updated when the *line of code* runs again.

### `$-` reference
If you *truly want to see* the **state reference update** in the code, you use `$-` reference.
```js
Element.prop = { text: '$-count' };
```
This will **update** the reference in the code when state changes.
> **Syntax**: `$-stateName`<br>

**<u>NOTE</u>**: `$-` can only be used in **internal properties** of the element. You **can't do operations** using this, `{ text: '$-count + 1' }`.

Back to the example.

```js
Display.prop = { text: 'Count is $-count' };
Display.make('re');
```
*we are done with `Display` element, so we `make` it. We'll update the count using another element : `Button`*

### `setState` function
If you want to *update any state* of the element, you use the **method** returned by `$state` method.
*By convention, we call this method `set{StateName}`*

When you call it, it ***automatically re-renders*** the element after updating the state.

We **pass** a *new value* of state to this method.<br/>
There can be **two ways** for it:
> **Direct** <br/>
> **Functional**

#### Direct way
In this case, we pass the latest state value directly as parameter.
```js
// suppose we want to update color of the element
setState('red');

// suppose we want to increment any "Click State" by 1
setClick(Element.getState('click') + 1);
// see how we "used" the "getState" method
```

#### Functional way
Here, we pass state value with *respect to the __previous state value__* as parameter using a `functional` approach

We call a **function** as parameter, whose **argument is the previous state** value.<br/>
Then we **return** a **new value** for state by **manipulating previous** one.

```js
// suppose we want to update the state to double the previous value
setState(function(previousState) {
    return previousState * 2;
});

// this can be simplified using arrow function
setState(prevState => prevState * 2);
// see how we "ignored" the "getState" method
```
>⠀

**Back to our `Display` example, we can use `setState` to update count on a button click**
```js
/*
    Defining a button, we don't necessarily need to render 
    it at this point cause there is no state of the "Button" 
    to manage.
*/
const Button = new Spuck({ type: 'button', parent: '#app', id: 'btn' });
Button.prop = { text: 'Update Count' };

Button.events = {
    // on click, update count using functional approach
    click: () => setCount(prevCount => prevCount + 1)
};
Button.make();
```

### `Display` at once:
```js
const Display = new Spuck({ type: 'h3', parent: '#app', id: 'dis' }).render();

const setCount = Display.$state('count', 0);

Display.prop = { text: 'Count is $-count' };
Display.make('re');


const Button = new Spuck({ type: 'button', parent: '#app', id: 'btn' });
Button.prop = { text: 'Update Count' };

Button.events = {
    click: () => setCount(prevCount => prevCount + 1)
};
Button.make();
```
![DisplayExample](https://cdn.discordapp.com/attachments/956081993488154664/981030014843760650/Counter_State_Example_Docs.gif)

## **State Sharing**
In SpuckJs you can *easily share states* to other elements.<br/>
You can share states to **siblings**, **parent**, **children** or **any other elements**, it *does not matter*.

### **Pseudo Children/States**

SpuckJs does it by using a *concept* called **`pseudo-Children`**.<br/>
If you want to share state of `Element 1` with `Element 2`, you can do it by making **"Element 2"** a **`pseudo-Child`** of **"Element 1"**.<br/>
```js
const Element1 = new Spuck({ ... }).render();

const Element2 = new Spuck({ ... }).render();

Element1.init.pseudoChildren = [Element2];
Element1.render('re');
```
`pseudoChildren` is a property of **`init`** object of the element.<br/>
> It is an **array**, and can hold **multiple elements**.<br/>

Now **all the states** of `Element 1` are **accessible** to `Element 2` as **`pseudo-states`**.<br/>

**<u>NOTE</u>: When an element renders, all its *pseudo-children* are also rendered.**

### **Referencing them**

Just like *normal* states, you can refer to `pseudo-states` either using:
> **`.getPseudoState`** method, or<br/>
> **`$$-`** reference

**<u>NOTE:</u> `$-`: _states_ and `$$-`: _pseudo-states_**

### **Extending "Display" Example**
Suppose there is one more element, *say `Display2`*, below the `Button`, which shows the count of the `Display` element.<br/>
We will make `Display2` a **pseudo-child** of `Display`, to access the `count` state.

```js
const Display2 = new Spuck({ type: 'h3', parent: '#app', id: 'dis2' });

Display1.init.pseudoChildren = [Display2];
Display1.render('re');
```
Now we can access the `count` state of `Display` from `Display2` as **pseudo-state**.
```js
Display2.prop = {
    text: 'Display count is $$-count'
};
Display2.make('re');
```
![DisplayExample](https://cdn.discordapp.com/attachments/956081993488154664/981046673243504650/PseudoState_Example.gif)

## **Looking at the Object**
If we console log the `Display` element now, we can see how are properties managed.

![DisplayObject](https://cdn.discordapp.com/attachments/956081993488154664/981062411828215879/Spuck1.png)

You can see states are stored in `_state` property of the element.<br/>
Each state is an `array` => `[value, setter]`

![](https://cdn.discordapp.com/attachments/956081993488154664/981064546426966067/pseudostateobject.png)
`Display` : pseudo-child : `Display2`<br/>
It contains the whole virtual element of `Display2` in the `pseudoChildren` array.<br/>

>⠀

Looking at `Display2`<br/>
![](https://cdn.discordapp.com/attachments/956081993488154664/981062412096634970/Screenshot_2022-05-31_100828.png)<br/>
It does not differ much, but instead of `_state` property, its `_pseudoState` property is defined, which has the same structure as `_state`.<br/>

You can see the `el` property of both elements, that is the **physical element** *stored within* the **virtual element**.<br/>
If we console log `Display.el`, we can see the HTML element:
![](https://cdn.discordapp.com/attachments/956081993488154664/981066897300783124/unknown.png)<br/>
Its there if you ever have to *interact* with the **browser api**, you can use it.