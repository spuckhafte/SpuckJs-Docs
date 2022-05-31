# Inbuilt Features
Features like **conditional rendering**, **iterative rendering** etc.<br/>
These are new features, and still in development.

**<u>NOTE:</u> Only *conditional rendering* is implemented till now**.

## Conditional Rendering
You can use this feature to **mount** an element only when some condition is satisfied.<br/>
When the element re-renders, it checks if the condition is met or not.<br/>
If it is true, it **mounts** the element, otherwise it **unmounts** it.<br/>

Here on every **re-render**, `render` method checks if the condition is met or not, and it itself **mounts** or **unmounts** the element. <br/>

*This was the case we were talking about [earlier](../GettingStarted/#late-note), where `render` method also **mounts** or **unmounts** the element.*

There can be two cases:
> **Case 1:** When condition for rendering is external to the element.<br/>
**Case 2:** When condition for rendering is internal to the element.

## Syntax
```js
AnyElement.renderIf(condition:function:boolean); 
```
Condition is a **function** which returns a boolean value.<br/>
This conditions get evaluated on every **re-render**.<br/>

## Example
These two examples will cover both cases.
### Case 1: External Condition
First we'll create a basic **count** element.

```js
const Count = new Spuck({ type: 'span', parent: '#app' }).render();
const setCount = Count.$state('count', 0);
Count.prop = { text: '$-count' };
Count.make('re');
```
Let's create the condition function.

```js
const oddCondition = () => Count.getState('count') % 2 !== 0; // condition for odd element
const evenCondition = () => Count.getState('count') % 2 === 0; // condition for even element

const Div = new Spuck({ type: 'div', parent: '#app', class: 'parent' }).make();
```
The `Div` element will contain the `odd/even elements` based on the condition.

Now we will show two elements, depending on **odd/even** of the count.
```js
const EvenElement = new Spuck({ type: 'h3', parent: '.parent', id: 'even' });
// using pseudo-state, explained after the codeblock
EvenElement.prop = { text: '$$-count Even' };
EvenElement.renderIf(() => evenCondition());

const OddElement = new Spuck({ type: 'h2', parent: '.parent', id: 'odd' });
OddElement.prop = { text: '$$-count Odd' };
OddElement.renderIf(() => oddCondition());
```
**Note** that the conditions are external to the elements.

Now we will declare these two elements as **pseudo-children** of the `Count` element.<br/>
So when the `Count` element will **re-render**, it will also make the conditional elements to re-render.<br/>
Then the elements will check again, wheather to **mount or not**, because the state of `Count` would have been changed and **the conditions** depend on the state. So this will cause **the conditions** to change too.

```js
Count.init.pseudoChildren = [EvenElement, OddElement];
Count.render('re');

EvenElement.make('re');
OddElement.make('re');
```

Now we will create a **button** to increment the count.

```js
const Button = new Spuck({ type: 'button', parent: '#app', id: 'button' });
Button.prop = { text: 'Click Me' };
Button.events = { click: () => setCount(a => a + 1) };
Button.make();
```
When the **count state** will be an *even number*, only the `EvenElement` will be on the DOM and vice-versa.<br/>
On button click, **count state** is changing which is causing the two elements to check their conditions again.

![cr1](https://cdn.discordapp.com/attachments/956081993488154664/981139851556556811/ConditionalRendering1.gif)

### Case 2: Internal Condition
We will again create a **count** element and its **condition** would **depend on** its **own state**.<br/>
Though the **state** will be *updated by another element*.

```js
const ShowCount = new Spuck({ type: 'div', parent: '#app', id: 'show-count' }).render();

const setShowCount = ShowCount.$state('showCount', 0);
ShowCount.prop = { text: '$-showCount' }
```
> **Condition:** State should be an even number.

```js
const showCountCondition = () => ShowCount.getState('showCount') % 2 === 0;

ShowCount.renderIf(() => showCountCondition());
```
Now a very normal button
```js
const Click = new Spuck({ type: 'button', parent: '#app', class: 'click' }).render();
Click.props = { text: 'Click Me' };
Click.events = { click: () => setShowCount(a => a + 1) };
Click.make('re');

ShowCount.make('re'); 
// we are making it after, so that it appears after the button in the DOM
```
When button is clicked, **state** of `ShowCount` will change, and the condition will be checked again.

![cr2](https://cdn.discordapp.com/attachments/930454716272504912/981145519671894016/ConditionalRendering2.gif)

## To be continued...
*maybe*.