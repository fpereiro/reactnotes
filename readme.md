I'm learning React. Here are my notes.

## [Official tutorial](https://reactjs.org/tutorial/tutorial.html)

I'd like to thank [Novisci](https://novisci.com) for giving me the time to learn React and write these notes.

- React is a js (javascript) library for building user interfaces (UIs). It lets you build complex UIs by combining isolated pieces of code called components.

- This is the boilerplate you should put at the top of your react application. The third line is optional and allows you to add a CSS stylesheet.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
```

- If you installed react, the three lines above should suffice to run any of the examples here.

- If your entire application is contained within a component named `Board` (for example), here's how you mount that component onto the DOM. To do so, enter this line at the *bottom* of your file.

```javascript
ReactDOM.render (<Board />, document.getElementById ('root'));
```

- There are a few types of components. We start with React.Component.

- A *React Component Class* (or *React Component Type*) takes in parameters (called props) and returns a hierarchy of views to display via the render method.

- The render method returns a description of the view you want to show. This description returned by render is a React element.

- Here's an example of a React element:

```javascript
class ShoppingList extends React.Component {
   render () {
      return (
         // Contents go here
      );
   }
}
```

- The contents of a React element can be written in plain js or in JSX, which is a XML-like syntax that is automatically converted to plain js.
This JSX:

```jsx
<div className="shopping-list">
   <h1>Shopping List for {this.props.name}</h1>
   <ul>
      <li>Instagram</li>
   </ul>
</div>;
```

- Is converted by React to this js:

```javascript
React.createElement ('div', {className: 'shopping-list'},
   React.createElement ('h1', null, 'Shopping List for ', this.props.name),
   React.createElement ('ul', null,
      React.createElement ('li', null, 'Instagram')
   )
);
```

- Normally, JSX is used instead of plain js.

- Within curly brackets (`{}`) you can put any valid js expression.

- A React element can be stored in a variable and also passed as an argument.

- While the component above only returned built-in DOM components (like `<div>` and `<ul>`), other components can return <ShoppingList>, making use of the component we just defined.

- `this` refers to the component itself (ShoppingList, in this case), which holds props, among other things. I believe this implies that only the components holds state, and not the built-in DOM components that are part of it.

- Properties can be passed from one component to another. Consider this code:

```javascript
class Board extends React.Component {
   render () {
      return <Square value={0} />;
   }
}
class Square extends React.Component {
   render () {
      return <button className='square'>{this.props.value}</button>
   }
}
```

- Notice how `<Square value={i} />` passes the value `i` to `Square`, and how `Square` makes use of this value through `this.props.value`. This is how properties are passed between components in React, or more precisely, from parent component to child component.

- The `className` above will determine the actual class attribute of the button, so you'll see this on your browser `<button class="square">` if you inspect the element.

To add event handlers to allow user interaction, React also uses properties. For example, `onClick`:

```jsx
<button onClick={function () {alert ('click');}} />
```

- Or we can write the same with the arrow function format for writing functions in js ES6. Writing functions like this has the advantage in that the value of `this` is more amenable to object-oriented programming.

```jsx
<button onClick={() => alert ('click')} />
```

- When inspecting the generated button, the `onClick` doesn't appear like a normal `onclick` event handler bound to the DOM element. The event is handled by React internally and is not accessible from the DOM (as far as I can see).

- The state of the program is held within the component, in the component's state. This state can be accessed by invoking `this.state`.

To initialize the state of the component, here's the invocation you should enter the following code. This code in particular will set the state to `{value: null}`:

```javascript
constructor (props) {
   super (props);
   this.state = {value: null};
}
```

- In the invocation above, the only changeable part is what goes within `this.state`. The call to `super` is mandatory and must be done at the top of the constructor.

- To update the state of the component, use the function `this.setState ({...})`. This can also be done from within a DOM element within the component. For example, this code will set `this.props.value` to `'X'` when the element is clicked:

```javascript
<button onClick={() => this.setState ({value: 'X'})} />
```

- After clicking on said button, `this.state.value` will be set to X. To see it in action, let's place `this.state.value` as the contents of the button. When you click on the button for the first time, `this.state.value` will change and you'll see an `'X'` within the button.

```javascript
class Square extends React.Component {
   constructor (props) {
      super (props);
      this.state = {value: null};
   }
   render () {
      return (
         <button onClick={() => this.setState ({value: 'X'})}>
            {this.state.value}
         </button>
      );
   }
}
```

- It is **crucial to understand** that `this.props` and `this.state` are distinct objects, despite both being objects that hold state and affect the components. `this.props` is used to pass data from a component to its children, whereas `this.state` is used to hold state that changes over time and belongs to the component itself.

- Internally, when `this.setState` is invoked, the component (and its children) are updated automatically.

- To share state between a parent component and its child, or among child components at the same level, said state must be created and held by the parent component. The parent component can then pass this state to the children. For example:

```javascript
class Square extends React.Component {
   render () {
      return (
         <button>{this.props.value}</button>
      );
   }
}
class Board extends React.Component {
   constructor (props) {
      super (props);
      this.state = {squares: ['first']};
   }
   render () {
      return <Square value={this.state.squares[0]} />;
   }
}
```

- If a child element must modify the state of the parent, this is done by passing a function to modify the parent's state. This function is passed from the parent to the child element, so it must be defined on the parent element.

```javascript
class Square extends React.Component {
   render () {
      return (
         <button onClick={() => this.props.handleClick ()}>{this.props.value}</button>
      );
   }
}
class Board extends React.Component {
   constructor (props) {
      super (props);
      this.state = {squares: []};
   }
   handleClick (i) {
      if (squares[i]) return;
      // This creates a copy of this.state.squares
      const squares = this.state.squares.slice ();
      squares[i] = 'X';
      this.setState ({squares: squares});
   }
   render () {
      return <Square
         value={this.state.squares[0]}
         handleClick={() => this.handleClick (0)}
      />;
   }
}
```

- Notice how `Square` now defines a function `handleClick`. We then pass `handleClick` as an attribute to `Square` (also named `handleClick`). Then, in `Square`, we associate the `onClick` property with a function invocation that calls `this.props.handleClick`; effectively invoking the function passed by `Board` to `Square`.

- React recommends to create copies of the state, instead of modifying it directly. This is why we created a copy of `squares` before updating the `Board`'s state.

- Since `Square` no longer holds state, we can rewrite it as a functional component. A functional component only takes `props` as input and returns what should be rendered.

```javascript
function Square (props) {
   return (
      <button onClick={() => props.handleClick ()}>{props.value}</button>
   );
}
```

- Notice how we changed `this.props` to `props` and how we got rid of the `render` method, instead directly returning JSX. The component can be further simplified as regards `onClick/handleClick`:

```javascript
function Square (props) {
   return (
      <button onClick={props.handleClick}>{props.value}</button>
   );
}
```

- If you're iterating an array or object and returning a set of elements based on the list, it is strongly suggested you pass an unique property called `key`, which helps React redraw views more efficiently:

```javascript
   <li key={user.id}>{user.name}: {user.taskCount} tasks left</li>
```

## [Main concepts](https://reactjs.org/docs/hello-world.html)

- JSX makes you close void tags (or non-void tags that happen to have no content) by adding a trailing slash before closing the tab:

```javascript
const element = <img src={user.avatarUrl} />;
```

- Normally, React is rendered within a DOM element with id `root`. However, you can make React render more than one component at the same time, as long as they belong to different DOM elements.

- React elements are immutable. To change things, you need to create them anew and re-render. Interestingly enough, you can call `ReactDOM.render` multiple times.

```javascript
function tick () {
   const element = (
      <div>
         <h1>Hello, world!</h1>
         <h2>It is {new Date ().toLocaleTimeString ()}.</h2>
      </div>
   );
   ReactDOM.render (element, document.getElementById ('root'));
}

setInterval (tick, 1000);
```

- "All React components must act like pure functions with respect to their props."

- "State allows React components to change their output over time in response to user actions, network responses, and anything else, without violating this rule."

- `componentDidMount` and `componentWillUnmount` are symmetrical methods. The former runs after a component has been rendered; the latter runs when the component has been removed. These are both called *lifecycle hooks*.

- State must be changed through `setState`. "The only place where you can assign this.state is the constructor."

- The two main lifecycle hooks (which are functions) are `componentDidMount` and `componentWillUnmount`, the former when the component is placed and the latter when it is removed. Consider the following example:

```javascript
class Clock extends React.Component {
   constructor (props) {
      super (props);
      this.state = {date: new Date ()};
   }

   componentDidMount () {
      this.timerID = setInterval (() => this.setState ({date: new Date ()}), 1000);
   }

   componentWillUnmount () {
      clearInterval (this.timerID);
   }

   render () {
      return (
         <div>
            <h1>Hello, world!</h1>
            <h2>It is {this.state.date.toUTCString ()}.</h2>
         </div>
      );
   }
}

ReactDOM.render (<Clock />, document.getElementById  ('root'));
```

- Notice how when the component is mounted, we set `timerID` to be an interval that executes itself every second. When it is executed, `state.date` is updated. Notice also how the interval is cleared when the component is removed.

- Regarding the handling of DOM events, React has two differences with plain HTML/js: event names are camelCased, and instead of passing a string as event handler, you pass a function instead. Compare these two equivalent snippets.

```html
<button onclick="activateLasers()"></button>
```

```javascript
<button onClick={activateLasers} />
```

- To prevent default behavior in React, you need to call `event.preventDefault` explicitly (instead of just returning false).

```javascript
function handleClick (e) {
   e.preventDefault ();
   console.log ('The link was clicked.');
}
```

- The event (`e`) received by the event handler is not a native DOM event, but rather a wrapper around it (more details [here](https://reactjs.org/docs/events.html)). This wrapper provides cross-browser compatibility. An important issue: **to call this event asynchronously, you must first invoke the following code:** `event.persist ()`.

- The following example defines a button that alternates between two values:

```javascript
class Toggle extends React.Component {
   constructor (props) {
      super (props);
      this.state = {isToggleOn: true};

      // This binding is necessary to make `this` work in the callback
      this.handleClick = this.handleClick.bind (this);
   }

   handleClick () {
      this.setState (prevState => ({isToggleOn: ! prevState.isToggleOn}));
   }

   render () {
      return (
         <button onClick={this.handleClick}>{this.state.isToggleOn ? 'ON' : 'OFF'}</button>
      );
   }
}

ReactDOM.render (<Toggle />, document.getElementById ('root'));
```

- Notice how `handleClick` must bind to `this` so that the function receives the `state` as its first argument. This problem could be solved by an arrow function, but the React guide discourages it: "(...) if this callback is passed as a prop to lower components, those components might do an extra re-rendering. We generally recommend binding in the constructor (...)".

If you want to pass extra arguments to the handler, you can write this:

```javascript
<button onClick={this.deleteRow.bind (this, id)}>Delete Row</button>
```

- In the example above, `deleteRow` will receive `id` as its first argument and it will receive automatically the `event` as its second argument. If more arguments were passed explicitly, `event` would be passed as the last argument.

- In the example below we perform conditional rendering, based on the value of `isLoggedIn`.

```javascript
function Greeting (props) {
   const isLoggedIn = props.isLoggedIn;
   if (isLoggedIn) return 'You are logged in';
   else            return 'You are not logged in';
}

ReactDOM.render (<Greeting isLoggedIn={false} />, document.getElementById ('root'));
```

- This is how you could make a component with a button that toggles a value. Notice how both the button and the message change depending on the state of the component.

```javascript
class LoginControl extends React.Component {
   constructor (props) {
      super (props);
      this.login  = this.login.bind (this);
      this.logout = this.logout.bind (this);
      this.state  = {isLogged: false};
   }

   login ()  {this.setState ({isLogged: true})}

   logout () {this.setState ({isLogged: false})}

   render () {
      const isLogged = this.state.isLogged;
      let button;

      if (isLogged)   button = <button onClick={this.logout}>Logout</button>;
      else            button = <button onClick={this.login}>Login</button>

      return (<div>
         You are {this.state.isLogged ? 'logged in' : 'logged out'}
         <br />
         {button}
      </div>);
   }
}

ReactDOM.render (<LoginControl />, document.getElementById ('root'));
```

- To avoid rendering a component, you can return `null` instead of JSX. Notice however that the lifecycle hooks (like `componentDidMount`) will be called anyway.

- This code takes an array of elements and creates a view with a set of `<li>`:

```javascript
function list () {
   var items = ['a', 'b', 'c'];
   return items.map (function (item) {
      return <li>{item}</li>
   });
}

ReactDOM.render (<ul>{list ()}</ul>, document.getElementById ('root'));
```

We can rewrite this code as a component:

```javascript
function List (props) {
   const listItems = props.items.map (function (item) {
      return <li key={item}>{item}</li>
   });
   return  (
      <ul>{listItems}</ul>
   );
}

const items = ['a', 'b', 'c'];

ReactDOM.render (<List items={items} />, document.getElementById ('root'));
```

- React strongly suggests using keys to identify elements, to allow for efficient redrawing of the DOM. In the example above, the line within `map` should be written as:

```javascript
      return <li key={item}>{item}</li>
```

- The `key` should be unique and it should not be something that changes, like an array index.

- Keys only make sense in the context of the surrounding array. If a component calls another one, the one that specifies the keys should be the one that performs the iteration.

```javascript
function Item (props) {
   return <li>{props.value}</li>
}

function List (props) {
   const listItems = props.items.map (function (item) {
      return <Item key={item} value={item} />
   });
   return (
      <ul>{listItems}</ul>
   );
}

const items = ['a', 'b', 'c'];

ReactDOM.render (<List items={items} />, document.getElementById ('root'));
```

- Notice above how `key` is specified in `List` and not in `Item`. Also notice how `key` is not accessible from with `Item`, so we need to pass it as a property named `value` so that it is available to `Item`.

- Keys must be unique among siblings but they are not required to be unique globally.

- The iterator (`Array.map`) can be embedded within JSX:

```javascript
function Item (props) {
   return <li>{props.value}</li>
}

function List (props) {
   return (
      <ul>{
         props.items.map (function (item) {
            return <Item key={item} value={item} />
         })
      }</ul>
   );
}

const items = ['a', 'b', 'c'];

ReactDOM.render (<List items={items} />, document.getElementById ('root'));
```

- In React, mutable state from `<input>`, `<textarea>` and `<select>` is kept on a component's state and updated exclusively through `setState ()`. When the value of an input/textarea/select is managed by React, we call that a *controlled component*.

- Below is an example of a form that has an input which updates state component and whose value depends on the state of the component.

```javascript
class Form extends React.Component {
   constructor (props) {
      super (props);
      this.state = {value: ''};
      this.change = this.change.bind (this);
      this.submit = this.submit.bind (this);
   }

   change (ev) {
      this.setState ({value: ev.target.value});
   }

   submit (ev) {
      alert ('A name was submitted: ' + this.state.value);
      ev.preventDefault ();
   }

   render () {
      return (
         <form onSubmit={this.submit}>
            <label>
               Name:
               <input type="text" value={this.state.value} onChange={this.change} />
            </label>
            <input type="submit" value="Submit" />
         </form>
      );
   }
}

ReactDOM.render (<Form />, document.getElementById ('root'));
```

- In HTML, the value of `<textarea>` goes in the body of the tag (instead of as a `value` attribute). In React, however, its value is a property. In the code above, you could replace the inner `<input>` with the following `<textarea>`:

```javascript
               <textarea value={this.state.value} onChange={this.change} />
```

- You can write equivalent code for a `<select>`.

```javascript
class Form extends React.Component {
   constructor (props) {
      super (props);
      this.state = {value: ['coconut']};
      this.change = this.change.bind (this);
      this.submit = this.submit.bind (this);
   }

   change (ev) {
      this.setState ({value: ev.target.value});
   }

   submit (ev) {
      alert ('A fruit was submitted: ' + this.state.value);
      ev.preventDefault ();
   }

   render () {
      return (
         <form onSubmit={this.submit}>
            <select value={this.state.value} onChange={this.change}>
               <option value="grapefruit">Grapefruit</option>
               <option value="lime">Lime</option>
               <option value="coconut">Coconut</option>
               <option value="mango">Mango</option>
            </select>
            <input type="submit" value="Submit" />
         </form>
      );
   }
}

ReactDOM.render (<Form />, document.getElementById ('root'));
```

- `coconut` will be automatically selected because we initialized it in the state. If however we didn't initialize it, the dropdown would show the first option (`Grapefruit`) but its value would still be `undefined`.

- You can select multiple elements with the `multiple` property. You'll also need to initialize the value to an array.

```javascript
      this.state = {value: ['coconut']};
      ...
            <select multiple={true} value={this.state.value} onChange={this.change}>
```

- **TODO: The above example does not work for me when I try to select an element**.

- `<input>` elements for handling files cannot be controlled by React.

- If you have multiple inputs, a way to handle them is to use the `name` property. Consider the following example:

```javascript
class Reservation extends React.Component {
   constructor (props) {
      super (props);
      this.state = {
         going: true,
         guests: 2
      };

      this.submit = this.submit.bind (this);
      this.change = this.change.bind (this);
   }

   change (ev) {
      this.setState ({
         [ev.target.name]: ev.target.type === 'checkbox' ? ev.target.checked : ev.target.value
      });
   }

   submit (ev) {
      alert (JSON.stringify (this.state));
      ev.preventDefault ();
   }

   render () {
      return (
         <form onSubmit={this.submit}>
            <label>
               Is going:
               <input name="going" type="checkbox" checked={this.state.going} onChange={this.change} />
            </label>
            <br />
            <label>
               Number of guests:
               <input name="guests" type="number" value={this.state.guests} onChange={this.change} />
            </label>
            <br />
            <input type="submit" value="Submit" />
         </form>
      );
   }
}

ReactDOM.render  (<Reservation />, document.getElementById ('root'));
```

- If you specify a value to an input, it will become read-only.

```javascript
               <input name="guests" type="number" value="5" onChange={this.change} />
```

- "In React, sharing state is accomplished by moving it up to the closest common ancestor of the components that need it. This is called “lifting state up”."

- After lifting state up, the parent component must pass an updater function to the child component, so that the child can update the value of its ancestor through that updater function.

- Here's an example of a Celsius/Fahrenheit calculator.

```javascript
function BoilingVerdict (props) {
   if (props.celsius >= 100) return <p>The water would boil.</p>;
   else                      return <p>The water would not boil.</p>;
}

class TemperatureInput extends React.Component {
   constructor (props) {
      super (props);
      this.change = this.change.bind (this);
   }

   change (e) {
      this.props.change (e.target.value);
   }

   render () {
      return (
         <fieldset>
            <legend>Enter temperature in {this.props.scale === 'c' ? 'Celsius' : 'Fahrenheit'}:</legend>
            <input value={this.props.temperature} onChange={this.change} />
         </fieldset>
      );
   }
}

class Calculator extends React.Component {
   constructor (props) {
      super (props);
      this.cchange = this.cchange.bind (this);
      this.fchange = this.fchange.bind (this);
      this.state = {temperature: 0, scale: 'c'};
   }

   cchange (temperature) {
      this.setState ({scale: 'c', temperature: parseInt (temperature)});
   }

   fchange (temperature) {
      this.setState ({scale: 'f', temperature: parseInt (temperature)});
   }

   render () {
      const t = this.state.temperature || 0;
      const c = this.state.scale === 'c' ? t : (t - 32) * 5 / 9;
      const f = this.state.scale === 'f' ? t : t * 9 / 5 + 32;
      console.log ('che', this.state.scale, t, c, f);

      return (
         <div>
            <TemperatureInput scale="c" temperature={c} change={this.cchange} />
            <TemperatureInput scale="f" temperature={f} change={this.fchange} />
            <BoilingVerdict celsius={parseFloat (c)} />
         </div>
      );
   }
}

ReactDOM.render (<Calculator />, document.getElementById ('root'));
```

- By using `props.children`, you can pass components to a certain component that acts as a "container":

```javascript
function Container (props) {
   return (
      <div className="Container">{props.children}</div>
   );
}

function WelcomeDialog () {
   return (
      <Container>
         <h1 className="Dialog-title">Welcome</h1>
         <p className="Dialog-message">Thank you for visiting our spacecraft!</p>
      </Container>
   );
}

ReactDOM.render (<WelcomeDialog />, document.getElementById ('root'));
```

- If you need to pass more than one set of children, you need to make up your own convention:

```javascript
function Container (props) {
   return (
      <div>
         <div className="left">{props.left}</div>
         <div className="right">{props.right}</div>
      </div>
   );
}

function WelcomeDialog () {
   return (
      <Container left={
         <p>Left</p>
      } right={
         <p>Right</p>
      } />
   );
}

ReactDOM.render (<WelcomeDialog />, document.getElementById ('root'));
```

- If you have a generic component, then the specific component uses the generic component, passing extra props to it. This way of working is based around composition, instead of inheritance. React is built with this principle.

"React is all about one-way data flow down the component hierarchy. It may not be immediately clear which component should own what state."

- To determine this (quoting from the guide):
   1. Identify every component that renders something based on that state.
   2. Find a common owner component (a single component above all the components that need the state in the hierarchy).
   3. Either the common owner or another component higher up in the hierarchy should own the state.
   4. If you can’t find a component where it makes sense to own the state, create a new component simply for holding the state and add it somewhere in the hierarchy above the common owner component.

- A piece of data is not state if 1) it's passed from a parent via props; 2) it changes over time; 3) you can compute it based on state/props in the component itself.

## [React & Redux for beginners](https://www.valentinog.com/blog/react-redux-tutorial-beginners/)

- "Redux solves a problem that might not be clear in the beginning: it helps giving each React component the exact piece of state it needs."

- "Redux holds up the state within a single location."

- "Also with Redux the logic for fetching and managing the state lives outside React."

- "What I found is that you should consider using Redux when: 1) multiple React components needs to access the same state but do not have any parent/child relationship; 2) you start to feel awkward passing down the state to multiple components with props."

- To install Redux: `npm i redux --save-dev`. To install react-redux (a library that combines React with Redux), `npm i react-redux --save-dev`. Then add the following lines to your main application file:

```javascript
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import { connect } from 'react-redux';
```

- My complete file header looks now like this:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import { connect } from 'react-redux';
```

- Notice that we import one function from Redux (`createStore`) and two from react-redux: `Provider` and `connect`.

- "In Redux reducers produce the state. The state is not something you create by hand."

- "A reducer is just a Javascript function. A reducer takes two parameters: the current state and an action (more about actions soon). Redux... is immutable and cannot change in place."

```javascript
const reducer = function (state, action) {
   return state || {counter: 0};
}
```

- The reducer above returns the state unchanged. If the state is not defined yet, it returns `{counter: 0}`.

- "But how does a reducer know when to produce the next state? The second principle of Redux says the only way to change the state is by sending a signal to the store. This signal is an action. “Dispatching an action” is the process of sending out a signal."

- Example Redux action:

```javascript
{type: 'increment', payload: amount}
```

A Redux action needs a `type` (is it always a string?) and can have an optional payload.

To add an action to a reducer, we can rewrite our reducer as follows:

```javascript
const reducer = function (state, action) {
   if (action.type === 'increment') return {counter: state.counter + 1};
   return state || {counter: 0};
}
```

- This reducer will return a *new* state with an incremented `state.counter`, whenever the action received is of type `increment`.

- There's three main actions in redux:

1. `getState` for accessing the current state of the application.
2. `dispatch` for dispatching an action.
3. `subscribe` for listening on state changes.

- Instead of using these functions directly, we'll instead use the handlers given by react-redux. Here's what should go after the reducer:

```javascript
const mapStateToProps = function (state) {
   return {counter: state.counter};
}

const mapDispatchToProps = function (dispatch) {
   return {
      increment: function () {
         dispatch ({type: 'increment'});
      }
   }
}
```

- The first function, `mapStateToProps`, converts parts of the state into the `props` that will be received by our components. Notice how it returns an object with a single key, `counter`, equal in value to `state.counter`.

- The second function, `mapDispatchToProps`, returns an object where each key is a function. Each of these functions should dispatch an action that will be received by our Redux reducer. In this case, `increment` will trigger an action of the form `{type: 'increment'}`.

- Let's now write a component that will both use and modify this state through Redux:

```javascript
export class App extends React.Component {
   constructor (props) {
      super (props);
      this.increment = this.props.increment.bind (this);
   }
   render () {
      return (
         <div>
            <h1>Counter {this.props.counter}</h1>
            <button onClick={this.increment}>Increment!</button>
         </div>
      );
   }
}
```

- The component is very similar to the ones we wrote before using Redux. Notice how both `this.props.counter` (coming from `mapStateToProps`) and `this.props.increment` (coming from `mapDispatchToProps`) are available. For using the latter, we bind it to the component itself (as we did many times in previous examples). Interestingly enough, both state and dispatchers are within the same object, `props`, so care should be taken not to use the same keys for state and actions.

- To make the example work, we need to add three more lines, to put the remaining boilerplate:

```javascript
const store = createStore (reducer);
const AppContainer = connect (mapStateToProps, mapDispatchToProps) (App);

ReactDOM.render(<Provider store={store}><AppContainer /></Provider>, document.getElementById ('root'));
```

- The code above initializes a `store`, then an `AppContainer` component that calls `connect` (passing the two mapping functions, and then passing `App` to the returned function). Finally, we render `Provider`, a built-in component from react-redux. I believe (but am not 100% sure) that this code cannot be simplified further, and that the top-level component of your app needs to be wrapped on both `Provider` and `AppContainer` (the former name comes directly from react-redux, the latter is a convention and could be modified).

This is the full example, including the six header lines:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import { connect } from 'react-redux';

const reducer = function (state, action) {
   if (action.type === 'increment') return {counter: state.counter + 1};
   return state || {counter: 0};
}

const mapStateToProps = function (state) {
   return {counter: state.counter};
}

const mapDispatchToProps = function (dispatch) {
   return {
      increment: function () {
         dispatch ({type: 'increment'});
      }
   }
}

export class App extends React.Component {
   constructor (props) {
      super (props);
      this.increment = this.props.increment.bind (this);
   }
   render () {
      return (
         <div>
            <h1>Counter {this.props.counter}</h1>
            <button onClick={this.increment}>Increment!</button>
         </div>
      );
   }
}

const store = createStore (reducer);
const AppContainer = connect (mapStateToProps, mapDispatchToProps) (App);

ReactDOM.render(<Provider store={store}><AppContainer /></Provider>, document.getElementById ('root'));
```

- Let's now modify the reducer & the component to pass payloads to the action.

```javascript
const reducer = function (state, action) {
   if (action.type === 'increment') return {counter: state.counter + (action.payload || 1)};
   return state || {counter: 0};
}

const mapDispatchToProps = function (dispatch) {
   return {
      increment: function (payload) {
         dispatch ({type: 'increment', payload: payload});
      }
   }
}

export class App extends React.Component {
   constructor (props) {
      super (props);
      this.increment = () => {
         this.props.increment (2);
      }
   }

   // Unchanged code...
```

- Notice three changes:

1. In the reducer, we increment `state.counter` by `action.payload`. If `action.payload` is not present, we default to incrementing by `1`.
2. In the `increment` dispatch handler, we're passing `payload` as the `payload`.
3. We have redefined `increment` (within the component) to call `this.props.increment` with a first argument of `2`. Notice the arrow function notation.

- If we wanted to specify the amount by which the counter should be incremented, we can modify the component as follows:

```javascript
export class App extends React.Component {
   constructor (props) {
      super (props);
      this.increment = () => {
         this.props.increment (parseInt (this.state.by, 10));
      }
      this.state = {by: 1};
      this.bychange = (ev) => {
         this.setState ({by: ev.target.value});
      }
   }
   render () {
      return (
         <div>
            <h1>Counter {this.props.counter}</h1>
            <input value={this.state.by} onChange={this.bychange}/>
            <button onClick={this.increment}>Increment!</button>
         </div>
      );
   }
}
```

- Notice that we define `this.increment` to call `this.props.increment` and pass `this.state.by` to it (after passing it to a number. We also need to initialize the state of the component to hold a component for `by`, and also create a method to update `by`. Finally, we add an `<input>` to control `this.state.by`.

## [Lifecycle hooks](https://reactjs.org/docs/react-component.html)

- "React lets you define components as classes or functions. Components defined as classes currently provide more features which are described in detail on this page."

- "The only method you must define in a React.Component subclass is called render(). All the other methods described on this page are optional."

- "Each component has several “lifecycle methods” that you can override to run code at particular times in the process."

- **Note**: in this section, we'll use the terms *lifecycle hook* and *method* interchangeably.

- There are three main "moments" of a component: *mounting*, *updating* and *unmounting*. The first one is when the component is placed; the second one, when it's redrawn; and the final one is when it's removed.

- The methods for each of the moments:
   1. **Mounting**: `constructor ()`, `getDerivedStateFromProps ()`, `render ()`, `componentDidMount ()`.
   2. **Updating**: `getDerivedStateFromProps ()`, `render ()`, `getSnapshotBeforeUpdate ()`, `componentDidUpdate ()`.
   3. **Unmounting**: `componentWillUnmount ()`.

- A few things to notice:
   - `constructor ()` only applies to mounting. `getSnapshotBeforeUpdate ()` only applies to updating.
   - Two methods are shared by mounting & updating: `getDerivedStateFromProps ()` and `render ()`. These are the methods used to draw/redraw the view and it makes sense that they should be shared by mounting & updating.
   - There's a final method corresponding to each of the moments (it's the only one in the case of unmounting): `componentDidMount ()`, `componentDidUpdate ()` and `componentWillUnmount ()`. Notice how the last one takes place **before** the actual unmounting.

- **Note**: deprectaed lifecycle hooks are not mentioned here, since we're trying to adhere to mid-2018 React best practices.

### Frequently used methods

- We've seen already the methods `constructor ()` and `render ()` in most of the examples above.

- "componentDidMount () is invoked immediately after a component is mounted (inserted into the tree). Initialization that requires DOM nodes should go here. If you need to load data from a remote endpoint, this is a good place to instantiate the network request. This method is a good place to set up any subscriptions. If you do that, don’t forget to unsubscribe in componentWillUnmount ()." This component seems to be the main "initialization" component. Regarding calling `setState` from within this method: "You may call setState () immediately in componentDidMount (). It will trigger an extra rendering, but it will happen before the browser updates the screen. (...) Use this pattern with caution because it often causes performance issues. In most cases, you should be able to assign the initial state in the constructor () instead." It is useful for "for cases like modals and tooltips when you need to measure a DOM node before rendering something that depends on its size or position."

- "componentDidUpdate (prevProps, prevState, snapshot)" is invoked immediately after updating occurs. This method is not called for the initial render. Use this as an opportunity to operate on the DOM when the component has been updated. This is also a good place to do network requests as long as you compare the current props to previous props (e.g. a network request may not be necessary if the props have not changed). You may call setState() immediately in componentDidUpdate() but note that it must be wrapped in a condition (...) or you’ll cause an infinite loop. It would also cause an extra re-rendering which, while not visible to the user, can affect the component performance." The `snapshot` argument is the optional value returned by the optional call to `getSnapshotBeforeUpdate` (see method description below).

- "componentWillUnmount () is invoked immediately before a component is unmounted and destroyed. Perform any necessary cleanup in this method, such as invalidating timers, canceling network requests, or cleaning up any subscriptions that were created in componentDidMount (). You should not call setState () in componentWillUnmount () because the component will never be re-rendered. Once a component instance is unmounted, it will never be mounted again."

### Rarely used methods

- "`getDerivedStateFromProps` is invoked right before calling the render method, both on the initial mount and on subsequent updates. It should return an object to update the state, or null to update nothing." It receives two arguments: `(props, state)`, and "it should return an object to update the state, or null to update nothing." According to [this official source](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#when-to-use-derived-state), "(it) exists for only one purpose. It enables a component to update its internal state as the result of changes in props. (...) as a general rule, derived state should be used sparingly." *Derived state*, I believe, means state that is calculated as a consequence of the `props` passed to a component. The "rare use cases for this method" are enumerated but not defined: 1) "For example, it might be handy for implementing a <Transition> component that compares its previous and next children to decide which of them to animate in and out." and 2) "loading external data specified by a source prop." Overall, it seems like a method that can be avoided.

- "`getSnapshotBeforeUpdate (prevProps, prevState)` is invoked right before the most recently rendered output is committed to e.g. the DOM. It enables your component to capture some information from the DOM (e.g. scroll position) before it is potentially changed. Any value returned by this lifecycle will be passed as a parameter to componentDidUpdate (). This use case is not common, but it may occur in UIs like a chat thread that need to handle scroll position in a special way."

### Non-lifecycle methods that you can invoke from the lifecycle methods above

- `setState (updater[, callback])`: "This is the primary method you use to update the user interface in response to event handlers and server responses. Think of setState () as a request rather than an immediate command to update the component. React does not guarantee that the state changes are applied immediately." The optional `callback` will fire after `setState` is processed.

- `component.forceUpdate (callback)`: "Calling forceUpdate () will cause render () to be called on the component, skipping shouldComponentUpdate (). This will trigger the normal lifecycle methods for child components, including the shouldComponentUpdate () method of each child. React will still only update the DOM if the markup changes. Normally you should try to avoid all uses of forceUpdate () and only read from this.props and this.state in render ()."

## Reselect

To explain the reselect library, we'll extract info from a few articles. Before doing that, we need to add reselect to our application. We add a seventh line to our header file:

```javascript
import { createSelector } from 'reselect'
```

Our entire header is now composed of seven lines:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import { createStore } from 'redux';
import { Provider } from 'react-redux';
import { connect } from 'react-redux';
import { createSelector } from 'reselect';
```

Notice that we'll only need the `createSelector` function from reselect, nothing else.

A selector is a function that takes the entire `state` and returns a part of the state. For example:

```javascript
var selector1 = function (state) {
   return state.items;
}
```

Normally, these selector functions are contained within `mapStateToProps`, which is the function that takes the Redux state and outputs the `props` needed by our components.

### [Usage of Reselect in a React-Redux Application](https://dashbouquet.com/blog/frontend-development/usage-of-reselect-in-a-react-redux-application)

- Reselect is a library that makes it easier/better to **get values from the store** in a React-Redux application.

- Its salient aspect is its memoization capability (memoization is saving the value of a function call and reusing it when the inputs to the function don't change). Reselect provides both performance and usability improvements. "In two words, when you use the createSelector () function, it memoizes an output of every input selector and recalculates the resulting value only if any of the input selectors changes its output."

- When using React-Redux, through the `connect` function, you get parts of Redux's state into your components (through the `mapStateToProps` function). After calling `mapStateToProps`, "the new props are shallow compared to the old ones and if they differ, components get re-rendered."

- Memoization is useful for two things:
   1. Performance improvements: when a small part of the state changes, other parts of the state might be recalculated unnecessarily. Reselect prevents this.
   2. Circular behavior: if part the state is modified as a result on a change of another part of the state, the application can go into an infinite loop of recalculating the state and re-rendering the components depending on it. Memoization prevents this.

However, circular behavior (to the best of my knowledge) only occurs when using the `componentWillReceiveProps` lifecycle hook, which is deprecated. So this leaves us only with 1), that is, memoization for performance improvements.

### [React, Reselect & Redux](https://medium.com/@parkerdan/react-reselect-and-redux-b34017f8194c)

Going back to our example above with `selector1`, here's how we can create a Reselect selector with it.

```javascript
var selector1 = function (state) {
   return state.items;
}

var reselector1 = createSelector ([selector1], (x) => x);
```

Quite abstruse. We can see though that `createSelector` here takes two arguments: an array with selector functions as first argument, and an anonymous function as second argument. This anonymous function receives one argument and returns it without doing anything else.

Let's now use this in the context of an application to list goods and their prices without tax and with tax:

```javascript
const reducer = function (state, action) {
   if (action.type === 'setTax') return {goods: state.goods, tax: action.payload};
   return state || {
      goods: [
         {name: 'tomatoes',  price: 1},
         {name: 'potatoes',  price: 3},
         {name: 'cucumbers', price: 2},
         {name: 'salad',     price: 4},
      ],
      tax: 0.20,
   }
}

var getGoods = createSelector ([function (state) {
   return state.goods;
}], (x) => x);

const mapStateToProps = function (state) {
   return {
      goods:  getGoods (state),
      tax:    state.tax,
   };
}

const mapDispatchToProps = function (dispatch) {
   return {
      setTax: function (payload) {
         dispatch ({type: 'setTax', payload: payload});
      }
   }
}

export class App extends React.Component {
   constructor (props) {
      super (props);
      this.setTax = (ev) => {
         var newTax = parseFloat (ev.target.value, 10);
         if (isNaN (newTax)) newTax = 0;
         this.props.setTax (newTax);
      }
   }
   render () {
      return (
         <div>
            <ul>
               {this.props.goods.map ((product, index) => {
                  return <li key={index}>{product.name} - {product.price} - ({product.price * (1 + this.props.tax)})</li>
               })}
               <input value={this.props.tax} onChange={this.setTax} />
            </ul>
         </div>
      );
   }
}

const store = createStore (reducer);
const AppContainer = connect (mapStateToProps, mapDispatchToProps) (App);
ReactDOM.render(<Provider store={store}><AppContainer /></Provider>, document.getElementById ('root'));
```

- Notice how within `mapStateToProps` we use `getGoods`, a selector created with `createSelector`. This allows us to avoid retrieving the goods again when the tax rate changes.

- "In Redux, whenever an action is called anywhere in the application, all mounted & connected components call their `mapStateToProps` function. This is why Reselect is awesome. It will just return the memoized result if nothing has changed." If selector functions are expensive, memoizing their return values avoids recalling them, hence saving processing time.

- "In the real world, you will most likely need the same certain part of your state object in multiple components. You will also want to pass props to your selector. To do this, you need to create a selector function that can be used on multiple instances of the same component at the same time….all while being properly memoized."

- To reuse a certain memoized selector, we can transform it into a function that returns a memoized selector, as follows:

```javascript
var getGoods = function () {
   return createSelector ([function (state) {
      return state.goods;
   }], (x) => x);
}

const mapStateToProps = function (state) {
   return {
      goods:   getGoods () (state),
      goods2:  getGoods () (state),
      tax:    state.tax,
   };
}
```

Notice how `getGoods` now returns a fresh selector. Also notice how we now invoke `getGoods` without arguments to get the selector, and then we invoke the selector (which is also a function) with the state. This allows for reusing a selector in multiple props & components.
